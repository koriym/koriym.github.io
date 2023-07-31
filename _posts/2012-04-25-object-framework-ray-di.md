---
title: 'Object Framework - Ray.Di'
author: admin
layout: post
permalink: /2012/04/di/
custom_permalink:
  - 2012/04/di/
categories: articles
tags:
  - 未分類
---

## Dependency Injection

[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.0102.png" alt="" title="bear-sunday-tmp-111219033305-phpapp02-1.010" class="alignnone size-full wp-image-1368" />][1]  
BEAR.Sundayではオブジェクトが必要とするインスタンスを、自ら取得しないでインジェクターに代入してもらうことを期待します。

コンストラクタやセッターメソッド経由で外部から代入されることをインジェクション（注入）、必要とするものをディペンデンシー（依存）と呼びます。ディペンデンシーを利用するクラスはコンシュマーと呼びます。

特に特定タスクを担当しオブジェクトがツールとして使うオブジェクトをサービスオブジェクトと呼びますが、依存はサービスオブジェクトに限りません。DB接続オブジェクトや、配列やスカラー値などの値も含みます。

前回記事の挨拶リソースに戻りましょう。このリソースはメッセージを返す為に、$messageデータを必要としています。<sup><a href="#footnote_0_1296" id="identifier_0_1296" class="footnote-link footnote-identifier-link" title="つまりこのリソースは$messageデータに依存しています">1</a></sup>

```php
class Greetings extends AbstractObject
{
    $message = [
        'en' => 'Hello World',
        'ja' => 'Konichiwa Sekai',
        'es' => 'Hola Mundo'
    ];

    public function onGet($lang = ‘en’)
    {
        $greeting = $this->message[$lang];
        return $greeting;
    }
}
```

$messageデータをクラスに固定で持たないで、外部から代入するように変更してみましょう。  
このようなセッターメソッドが必要です。

```php
/**
 * @Inject
 * @Name(“greeting_message”)
 */
public function setMessage(array $message)
{
    $this->message = $message;
}
```

固定されていたデータが外部から代入できるようになりました。

ディペンデンシーをセッターメソッド経由でインジェクションしてるので、これをセッターインジェクションと呼びます。 またコンストラクターにインジェクトするのをコンストラクターインジェクションと呼びます。BEAR.SundayはRai.DiというDIフレームワークを使っていますが、サポートするインジェクションはこの２つのみです。<sup><a href="#footnote_1_1296" id="identifier_1_1296" class="footnote-link footnote-identifier-link" title="プロパティにインジェクトするプロパティインジェクションはサポートされません。">2</a></sup>

このインジェクションを行うのがディペンデンシーインジェクターです。インジェクターは決められたルールでオブジェクトのコンストラクションを行います。クラスをインスタンス化しディペンデンシーをインジェクトします。コンストラクション後に行われる初期化メソッドの呼び出しや、オブジェクト破棄の直前に呼ばれるメソッドの呼び出し予約など「オブジェクトライフサイクル」に関する設定も行います。BEAR.Sundayでは原則的に全てのオブジェクトの生成はこのディペンデンシー・インジェクターが行い、オブジェクトの中からディペンデンシーを取得することは推奨されません。

## インジェクションポイントと@Inject

ユーザーがセッターメソッドに@Injectと注記（アノテート）することでRay.Diは『ここに依存の注入が必要だ』ということが分かります。この「外部からの代入を期待する部分」を**インジェクションポイント**と呼びます。

※注）アノテーションはDoctrine.CommonsのAnnotationを使用していて、このアノテーションを使うためのuse文が必要です。

```php
use Ray\Di\Di\Inject;
use Ray\Di\Di\Named;
```


インジェクションポイントに実際に何をセットするかは**モジュール**で設定します。モジュールではインジェクションポイントとインスタンスをバインドします。バインドの方法はいくつかありますがここでは特定の名前をつけてインジェクションポイントを指定する方法を使用していています。

実際のモジュールのコードはこのようになります。

```php
class AppModule extends AbstractModule
{
    protected function configure()
    {
        $message = [
            'en' => 'Hello World',
            'ja' => 'Konichiwa Sekai',
            'es' => 'Hola Mundo'
        ];
        $this->bind()->named(‘greeting_message’)->toInsntance($message);
    }
}
```

## インジェクターの生成とオブジェクトグラフ生成

インジェクターを使ってこのクラスにディペンデンシーをインジェクトしてインスタンスを取得します。

```php
$injector = Injector::create([new AppModule]);
$injector->getInstance(‘name\space\Greeting’);
```

モジュールにはインジェクションポイントに対してどのインスタンスを提供するかという、いわばアプリケーションの構成知識が凝縮されています。その構成知識を使ってインジェクターはオブジェクトのコンストラクション（生成、インジェクト、ライフサイクルのセット）を行いインスタンスを返します。

## インジェクションの連鎖とオブジェクトグラフ

ディペンデンシーインジェクションは専用のライブラリを使わなくても、手動でも行う事ができます。

たとえばUserクラスはDbクラスのインスタンスが必要でDbクラスはDB接続情報の文字列が必要だとします。これを手動のインジェクトするためには例えばこのようなコードが必要でしょう。

```php
$dsn = $_ENV['master_db'];
$db = new Db($dsn);
$user = new User($db);
```

一行だとこうです

```php
$user = new User(new Db($_ENV['master_db']));
```

このコードで明らかなのは、依存に依存があればその「依存の依存」から先に用意して順番に次の依存に渡さなければならないことです。Userクラスを生成する前に、DBオブジェクトの生成が完了してる必要があります。DBオブジェクトを生成するにはDB接続情報を取得しておく必要があります。これは通常のプログラムではごく当たり前のことです。

ところがRay.DIはUserクラスを作る前にDbオブジェクトを予め作っておく必要はありません。オブジェクトの構成知識を知っているインジェクターは必要な依存を遡って生成、インジェクトしてオブジェクトグラフをコンストラクションします。

例をあげます。

Userクラスを生成するときにRay.DiはそのクラスをつくるためにはDbオブジェクトが必要だと言う事を検知します。Dbオブジェクトを生成しようとしますが、ところがその生成にはDB接続情報が必要とも検知します。インジェクターがもつ構成知識でDB接続情報は得られます。得られた情報を使ってDBオブジェクトを生成します。そうやって依存の依存を順番に辿り依存性の解決（Dependency Resolution)を行い元のインスタンスを生成します。依存がツリー構造になっているこのオブジェクトをオブジェクトグラフと呼びます。

[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.035.png" alt="" title="Object Graph" class="alignnone size-full wp-image-1370" />][2]

## コンストラクションの再利用

BEAR.Sundayではプログラムの中でいつでもインジェクターを使い必要なインスタンスをオンデマンドで生成できますが、実際にはほとんどその出番はありません。boot時のルートオブジェクトグラフ（ページリソースやリソースクライアント）が生成される時に必要なオブジェクト、またはファクトリーの生成が全て完了するからです。<sup><a href="#footnote_2_1296" id="identifier_2_1296" class="footnote-link footnote-identifier-link" title="現在のBEAR.Sundayで登場する独立したオブジェクトはアプリケーションはappリソースを除くと基本的には３つしかありません。アプリケーションとリソースクライアントとページリソースです。その他のオブジェクトをそれらの「ルートオブジェクト」を構成するプロパティでしかない場合がほとんどです。
">3</a></sup>

前バージョンBEAR.Saturday (2008年）ではアプリケーションスクリプトからnew演算子を取り除くことが一つの目標でしたが、BEAR.Sundayではフレームワークサイドでもnewの利用はほとんどありあません。オブジェクトはコンストラクションされるとAPCのストレージに格納され、リクエストをまたいで再利用されます。

つまり現在のBEAR.Sundayではリクエスト毎に異なった処理をコンストラクタで記述することはできません。この制約はメリットとデメリットがあります。オブジェクトシリアライズ前提のためシリアライズできないクロージャや組み込みオブジェクトをコンストラクション時にプロパティにセットできません。一方、固定化されたオブジェクトグラフとより安定したフロー、強力で容易なキャッシュ機構、アノテーションやDI、AOP等を採用しながらも維持している強力なパフォーマンス等は大きなメリットです。

再利用はオブジェクトグラフの膨大な取得コスト<sup><a href="#footnote_3_1296" id="identifier_3_1296" class="footnote-link footnote-identifier-link" title="アノテーションが必要とするコメント文のパースだけでなくアノテーションの名前解決のためのPHPスクリプトのパースも行われてます">4</a></sup> を最小限にします。30,000を超えるindexページのファンクションコールは500以下になり、実行速度は数十倍になっています。

オブジェクトのコンストラクタは基本的にサービス開始の最初の１リクエストしか通りません。その特徴をv0.1.0alphaインストールの時に用意されるindexページでみてます。

##### indexページ画面

[<img src="/images/wp-content/uploads/2012/04/77289491623cb7d47294d7b9e69ed064.png" alt="" title="Indexページ" class="alignnone size-full wp-image-1371" />][3]

##### indexページリソーススクリプト

```php
class Index extends Page
{
    use ResourceInject;
    public function __construct()
    {
        $this['greeting'] =‘Hello, BEAR.Sunday.’;
        $this['version'] = [
            'php'  => phpversion(),
            'BEAR' => Framework::VERSION
        ];
        $this['extentions'] = [
            'apc'  => extension_loaded('apc') ? phpversion('apc') : 'n/a',
            'memcache'  => extension_loaded('memcache') ? phpversion('memcache') : 'n/a',
            'mysqlnd'  => extension_loaded('mysqlnd') ? phpversion('mysqlnd') : 'n/a',
            'pdo_sqlite'  => extension_loaded('pdo_sqlite') ? phpversion('pdo_sqlite') : 'n/a',
            'Xdebug'  => extension_loaded('Xdebug') ? phpversion('Xdebug') : 'n/a',
            'xhprof' => extension_loaded('xhprof') ? phpversion('xhprof') : 'n/a'
        ];
    }
    /**
     * Get
     */
    public function onGet()
    {
        $cache = apc_cache_info(‘user’);
        $this['apc'] = [
           'total' => $cache['num_entries'],
           ‘size’ => $cache['mem_size']
        ];
        // page / sec
        $this['performance'] = $this->resource->get->uri(‘app://self/performance’)->request();
        return $this;
    }
}
```
## トレイトを使ったセッターインジェクション
違うクラスでも求める依存が同じなら、同じセッターが利用できます。セッターメソッドはクラスをまたいで横断的に再利用できます。モジュールに新たな設定はありません。 メソッドの横断的利用、PHP5.4の新機能のtraitにすると便利で表記も簡潔になります。

```php
use Ray\Di\Di\Inject;
use Ray\Di\Di\Named;
trait MessageInject
{
  private $message;
  /**
   * @Inject
   * @Name(“greeting_message”)
   */
  public function setMessage(array $message)
  {
      $this->message = $message;
  }
}
```
アノテーションのuse文も入っているので、利用クラスでは簡単にインジェクションが表記できます。まとめるとこうなります。

```php
class Greetings extends AbstractObject
{
    use MessageInject;

    public function onGet($lang = ‘en’)
    {
        $greeting = $this->message[$lang];
        return $greeting;
    }
}
```
ボイラープレートになりがちなセッターインジェクションコードがスッキリ一行になり、依存関係を記すセルフドキュメントのようになってるのではないでしょうか。

## プロバイダー<sup><a href="#footnote_5_1296" id="identifier_5_1296" class="footnote-link footnote-identifier-link" title="このセクションはstackoverflowの記事を元にしています。http://stackoverflow.com/questions/2504798/dependency-injection-in-constructors ">6</a></sup>
Day.DiによるDIは次の２つのパートで成り立っています。

 * モジュールでのオブジェクトコンストラクション（<strong>Construction</strong>）
 * コンシュマーでの利用（<strong>Execution</strong>）


コンストラクションはモジュールで完了させ、コンシュマーでは利用だけを行います。
<strong>肝心なのはコンストラクションとエクスキューションを完全に分離して混ぜないことです。</strong>コンシュマーにディペンシーインジェクターやサービスコンテナを渡したりする事は推奨されません。<sup><a href="#footnote_6_1296" id="identifier_6_1296" class="footnote-link footnote-identifier-link" title="コンシュマーがインジェクターを使ってサービスを取得することは、デメテルの法則（最小知識の法則）に違反します。">7</a></sup><sup><a href="#footnote_7_1296" id="identifier_7_1296" class="footnote-link footnote-identifier-link" title="例外はそのコンシュマーがファクトリークラスの場合です。BEAR.Sundayで唯一インジェクターを依存として受け取りオブジェクトコンストラクションをしてるのはリソースのnewInstance()メソッドです。">8</a></sup>
ンシュマー内で新しいインスタンスが都度欲しい時は<strong>プロバイダー</strong>を使います。プロバイダーは最小のファクトリーで、引き数なしのget()というメソッドだけを持ちます。たとえばテンポラリーファイルのハンドルが都度、複数欲しいなら(Provider)$tmpFilePorviderをインジェクトしてもらって、このように使います。

            
```php
$tmpFile1 = $this->tmpFileProvider->get();
$tmpFile2 = $this->tmpFileProvider->get();
```

プロバイダーはどのようにインスタンスを作るかという知識を全て持っています。引き数は渡す事ができず、最小化されたこのファクトリーに伝える事ができるのは生成のタイミングだけです。
なお、newは絶対に使っていけないというわけではありません。ごく小さなオブジェクトやPHPの組み込みオブジェクトなどは問題ないでしょう。

## コンテナ?
Ray.Diでは内部でライフサイクル（シングルトンなど）の管理にオブジェクトコンテナを使いますが、依存性の解決には使いません。ユーザーがコンテナを触る必要も出番もほとんどありません。依存性は原則インターフェイスによって解決されます。SOLIDのD、 依存関係逆転の原則（DIP：Dependency Inversion Principle)です。

## bootstrapのみで出現
このディペンデンシーインジェクターは通常、bootstrapスクリプト時にルートとなるアプリケーションオブジェクトを取得するのみです。ランタイムではオブジェクトの生成は原則行いません。`provider`や`PHPのclone`で複製をつくります。

## Conclusion
この記事では、BEAR.Sundayアプリケーションの役割と働きをみてきました。Ray.Diは<a href="http://code.google.com/p/google-guice/">Google Guice</a>のクローンです。<sup><a href="#footnote_8_1296" id="identifier_8_1296" class="footnote-link footnote-identifier-link" title="Guiceが使われているGoogleの代表的なプロダクトにAdSenseがあります">9</a></sup> 全ての機能が移植されてるわけではありませんが、ここで紹介した機能以外にも沢山の機能があります。<a href="http://docs.doctrine-project.org/projects/doctrine-common/en/latest/reference/annotations.html">Doctrine.Commons.Annotation</a>ライブラリを使い、AuraというPHP5.4フレームワークの<a href="https://github.com/auraphp/Aura.Di">Aura.Di</a>ライブラリを拡張して作成しています。またRay.Diインジェクター自身の依存も手動でインジェクトされ拡張可能です。



<a href="http://code.google.com/p/rayphp/">Ray.Di – Guice style annotation-driven dependency injection framework for PHP</a>



インジェクションポイントとディペンデンシーのバイディングはモジュールで設定し、インジェクターはどのオプジェクとが求められればどのインスタンスを渡すかという知識を持っています。モジュールはクラスをどのようにコンストラクトするかではなく<strong>求められた依存に対してどのインスタンスを渡すか</strong>という設定が行われています。そのため同じ依存を要求する違うクラスに新たな設定は必要なく、セッターメソッドのtraitを使ってより簡素な記述でディペンデンシーが取得できます。



Ray.Diの特徴はオブジェクトの生成と利用が完全に分離されていること<sup><a href="#footnote_9_1296" id="identifier_9_1296" class="footnote-link footnote-identifier-link" title="利用クラスでサービスコンテナへの依存がない">10</a></sup>、モジュールでのDSLによるバインディング、APCを使ったオブジェクトグラフコンストラクションの再利用等です。<sup><a href="#footnote_10_1296" id="identifier_10_1296" class="footnote-link footnote-identifier-link" title="コンストラクションは常にキャッシュされ、再利用されることを考慮したコーディングが必要です。">11</a></sup>



Ray.Diは<strong>可変点の明確化と最小化</strong>というBEAR.Sundayのアーキテクチャ全体を通しての原則を支持します。



またRay.Diはモジュールで特定メソッドの実行にインターセプターをバインドすることが可能で、アスペクト指向プログラミングが利用できます。BEAR.Sundayではフレームワークやアプリケーションの動作や役割を様々なアスペクトの集合だと考えています。次回の記事ではAOPをサポートすRay.AopのBEAR.Sundayでの役割、アスペクト指向デザイン(AOD)により実装されたアプリケーション機能を紹介します。


<div class="wp_social_bookmarking_light">
<div>
</div>

<div>
</div>

<div>
<a href="http://b.hatena.ne.jp/entry//blog/2012/04/di/" title="はてなブックマーク - Object Framework – Ray.Di" rel="nofollow" class="wp_social_bookmarking_light_a" target="_blank"><img src="http://b.hatena.ne.jp/entry/image//blog/2012/04/di/" alt="はてなブックマーク - Object Framework – Ray.Di" title="はてなブックマーク - Object Framework – Ray.Di" class="wp_social_bookmarking_light_img" /></a>
</div>

<div>
<div id="___plusone_0" style="height: 20px; width: 90px; display: inline-block; text-indent: 0px; margin: 0px; padding: 0px; background: none repeat scroll 0% 0% transparent; border-style: none; float: none; line-height: normal; font-size: 1px; vertical-align: baseline;">
</div>
</div>

<div>
<a href="http://www.tumblr.com/share?v=3&u=http%3A%2F%2Fwww.bear-project.net%2Fblog%2F2012%2F04%2Fdi%2F&t=Object%20Framework%20%26%238211%3B%20Ray.Di" title="" style="display:inline-block; text-indent:-9999px; overflow:hidden; width:61px; height:20px; background:url('http://platform.tumblr.com/v1/share_2.png') top left no-repeat transparent;"></a>
</div>
</div>


<br class="wp_social_bookmarking_light_clear" /> <ol class="footnotes">
<li id="footnote_0_1296" class="footnote">
  つまりこのリソースは$messageデータに依存しています [<a href="#identifier_0_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_1_1296" class="footnote">
  プロパティにインジェクトするプロパティインジェクションはサポートされません。 [<a href="#identifier_1_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_2_1296" class="footnote">
  現在のBEAR.Sundayで登場する独立したオブジェクトはアプリケーションはappリソースを除くと基本的には３つしかありません。アプリケーションとリソースクライアントとページリソースです。その他のオブジェクトをそれらの「ルートオブジェクト」を構成するプロパティでしかない場合がほとんどです。 [<a href="#identifier_2_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_3_1296" class="footnote">
  アノテーションが必要とするコメント文のパースだけでなくアノテーションの名前解決のためのPHPスクリプトのパースも行われてます [<a href="#identifier_3_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_4_1296" class="footnote">
  例えばYAMLファイルのCSVファイルのパースなどをコンストラクタで行いコンテンツとしてセットすると再利用されるので個別にキャッシュしたりする必要がありません。 [<a href="#identifier_4_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_5_1296" class="footnote">
  このセクションはstackoverflowの記事を元にしています。http://stackoverflow.com/questions/2504798/dependency-injection-in-constructors [<a href="#identifier_5_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_6_1296" class="footnote">
  コンシュマーがインジェクターを使ってサービスを取得することは、<a href="http://ja.wikipedia.org/wiki/%E3%83%87%E3%83%A1%E3%83%86%E3%83%AB%E3%81%AE%E6%B3%95%E5%89%87">デメテルの法則（最小知識の法則）</a>に違反します。 [<a href="#identifier_6_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_7_1296" class="footnote">
  例外はそのコンシュマーがファクトリークラスの場合です。BEAR.Sundayで唯一インジェクターを依存として受け取りオブジェクトコンストラクションをしてるのはリソースのnewInstance()メソッドです。 [<a href="#identifier_7_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_8_1296" class="footnote">
  Guiceが使われているGoogleの代表的なプロダクトにAdSenseがあります [<a href="#identifier_8_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_9_1296" class="footnote">
  利用クラスでサービスコンテナへの依存がない [<a href="#identifier_9_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_10_1296" class="footnote">
  コンストラクションは常にキャッシュされ、再利用されることを考慮したコーディングが必要です。 [<a href="#identifier_10_1296" class="footnote-link footnote-back-link">↩</a>]
</li>
</ol>

 [1]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.0102.png
 [2]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.035.png
 [3]: /images/wp-content/uploads/2012/04/77289491623cb7d47294d7b9e69ed064.png
