---
title: BEAR.Resource
author: admin
layout: post
permalink: /2012/04/bear-resource/
categories: ["blog"]
tags:
  - BEAR
  - PHP
---

BEAR.Sundayは**DI**、**AOP**、**REST**、この３つの技術をコアにしたオブジェクトフレームワークをベースにしています。  
[<img class="alignnone size-full wp-image-1257" title="2bear-sunday-tmp-111219033305-phpapp02.002-001" src="/images/wp-content/uploads/2012/04/2bear-sunday-tmp-111219033305-phpapp02.002-001.png" alt="" />][1]  
このオブジェクトフレームワークがある程度完成したのを機に、今回v0.1.0alphaとして一旦まとめました。<sup><a href="#footnote_0_1178" id="identifier_0_1178" class="footnote-link footnote-identifier-link" title="HelloWorldしか出ない状態が長く続いたのにも関わらずソースをみたり関心を持ってもらった方がおられました。ありがとうございます">1</a></sup> まだ実用レベルではなくこれからこのオブジェクトフレームワークの上に、APIフレームワーク、webアプリケーションフレームワークと、フレームワークのレイヤーを重ねていく予定です。

この記事では現在のv0.1.0alpha実装からBEAR.Sundayの特徴を紹介します。

## リソースオブジェクト

BEAR.Sudayで中心になるのがリソースオブジェクトです。MVCで言うとモデルに近いものですがwebからアクセスされる最初のリソースレイヤーの**pageリソース**はコントローラーのように振る舞います。

それぞれのリソースはURIで表すことができ、スキーマに応じて処理の仕組みが変わります。最も標準的なものはリソースURIとPHPクラスがマップされるリソースオブジェクトです。HTTPのリクエストメソッドに準じたインターフェイスメソッドを持ち、リクエスト処理を記述します。役割に応じてpageリソースやappリソースとスキーマや呼び方は代わりますが基本的な仕組みは同じです。

[<img class="alignnone size-full wp-image-1263" title="page resource" src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.005-001.png" alt="" />][2]

ウエブページのようなオブジェクトと思ってみてください。

例えばGETリクエストすればリクエスト用のメソッドがコールされ、結果でもあるリソースオブジェクト自身が帰ってきます。

まずはHello Worldページです。

```php
class Hello extends Page
{
    public function onGet($name)
    {
        $this->body = ‘Hello ‘ . $name;
        return $this;
    }
}
```


$nameを受け取り連結して返すだけのページです。bodyプロパティに代入して自身を返してる事に注目してください。

このページクラスは以下のように表記しても同じです。<sup><a href="#footnote_1_1178" id="identifier_1_1178" class="footnote-link footnote-identifier-link" title="シンタックスシュガー ">2</a></sup>文字列を返しても、クライアントにはbodyプロパティがセットされたオブジェクトが返ります。
```php
class Hello extends Page
{
    public function onGet($name)
    {
        return ‘Hello ‘ . $name;
    }
}
```

## ４つの公開されたプロパティ

リソースクラスは以下の４つのpublicプロパティを持ちます。 

*   リソースコード
*   リソースメタ情報（ヘッダー）
*   リソースコンテンツ（リソース状態）
*   リソース表現
コードはHTTPステータスコードに準じたリソースの状態がはいっています。リソースの本質的な値はリソースコンテンツに入っていますが、クライアントが利用するリソース表現は別のプロパティが用意されています。

このHelloページリソースではリソーススコンテンツ$bodyプロパティをセットしてクライアントに返しています。

## レイヤード・リソース

[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.005.jpg" alt="" title="Layerd Resource" class="alignnone size-full wp-image-1301" />][3]  
pageリソースはwebクライアウントから最初にコールされるリソースレイヤーです。多くの場合、（更に関心を細分化した）appリソースをページからリクエストします。コントローラーがモデルをリクエストするようなもので、pageリソースは**pageコントローラー**、appリソースは**内部APIとして振る舞うモデル**として機能します。

最初の例の単純な例のpageリソースはコントローラーとして機能するよりも、リソースコンテンツを変更することで自身を構成しています。自身がレスポンスオブジェクトのようですがこれは不作法なことではありません。リソースは関心に応じてレイヤリングしますが、単純なHelloページではそのページだけで完結する場合もあるでしょう。一方、appリソースでも必要があれば関心にレイヤリングを適用して、適切なネストのレイヤーを持つのが良いでしょう。

最初にwebブラウザのリクエストURIに応じてルートされたpageリソースがリクエストされます。pageリソースはappリソースやその他のリソースをリクエストしますが、他のpageリソースもリクエストすることができます。階層構造MVCのように振る舞います。

## アプリケーション(app)リソース

アプリケーションリソースはいわば内部APIです。MVCでの主役がMであるように、BEAR.Sundayでもアプリケーション価値の多くはこのappリソースにあります。<sup><a href="#footnote_2_1178" id="identifier_2_1178" class="footnote-link footnote-identifier-link" title="一方、pageリソースの役割はリクエストURIに応じて複数のappリソースをアクセスする事がほとんどです。">3</a></sup>

appリソースの簡単な例をコードで紹介します。$lang(言語)を引数で渡すと挨拶文字列を返す 「挨拶リソース」です。

```php
class Greetings extends AbstractObject
{
    private $message = [
        'en' => 'Hello World',
        'ja' => 'Konichiwa Sekai',
        'es' => 'Hola Mundo'
    ];

    public function onGet($lang = ‘en’)
    {
        $greeting = $this->message[$lang];
        return $greeting;
    }
```

これはpageリソースで説明したように、以下のコードと同じものです。

```php
public function onGet($lang = ‘en’)
{
    $this->body = $this->message[$lang];
    return $this;
}
```

挨拶のデータは$messageプロパティに格納されていて、引数$lang（言語）によって指定されたメッセージを返します。このリソースクラスはonGetメソッドがあり、GETリクエストだけを行う事ができます。

リソースクライアントからはのリクエストはこのようにして作る事ができます。withQuery()メソッドで与えてるのは順番による引数ではなくて、変数名を指定した名前付き引数（named parameter)であることに注意してください。引数の順番は関係ありません。

## リソースリクエスト

リソースリクエストは以下のように記述します。

```php
$request = $resource
           ->get
           ->uri(‘app://self/greeting’)
           ->withQuery(['lang'] => ‘ja’)
           ->request();
```
この文で作られたのは**リクエスト**です。このリクエストを実際に行うにはこのリクエストオブジェクトをクロージャのように扱います。

```php
$result = $request();
```

リクエストは再利用することができます。引数を指定して指定した名前付き引数に追加、上書きすることができます。

```php
$result = $request(['lang' => 'en]);
```

### イーガーリクエスト

リクエストの結果をすぐに求めるにはリクエストに eagerをつけます。

```php
$result = $resource
          ->get
          ->uri(‘app://self/greeting’)
          ->withQuery(['lang'] => ‘ja’)
          ->eager
          ->request();
```

この文ではすぐにリクエストが行われます。

## リソースのレンダリング

リクエストにより求められたリソースオブジェクトはプロパティにリソースの状態を持っています。プロパティを直接使う事もできますが、それよりオブジェクトとして利用するのがスマートです。オブジェクトはコンテキストに応じて評価されます。

連想配列として扱うとリソースコンテンツのエレメントが取り出せます。例えばユーザーリソースのID等です。

```php
echo $result['id']; // $result->body['id'] と同じ
```

オブジェクトなのでメソッドを利用することはもちろんできます。

```php
echo $result->log();
```

文字列として扱うと、そのリソースはコンテンツを表現に変えようとします。

BEAR.Sundayではリソースからデータを受け取ってテンプレートエンジンに渡すのではなく、**リソースがテンプレートエンジンを保持**し（データを外部に公開することなく）**自身がレンダリング**を行います。 

図をご覧下さい。

[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.008.jpg" alt="" title="Web MVC" class="alignnone size-full wp-image-1303" />][4]  
[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.009.jpg" alt="" title="BEAR.Resource" class="alignnone size-full wp-image-1304" />][5]

存在するリソースの１つ１つのオブジェクトがレンダラー（Viewオブジェクト）を保持します。<sup><a href="#footnote_3_1178" id="identifier_3_1178" class="footnote-link footnote-identifier-link" title="アプリケーションスコープでシングルトン">4</a></sup>　

例えばユーザーリソースのコンテンツは以下のようなものだとします。

<pre>$user->body = ['name' => 'koriym', 'gender' => 'male']</pre>

このJSON表現はこうなります。

<pre>{"name":"koriym","gender":"male"}</pre>

このレンダリングはjson_encode()関数を使うだけの単純なものでしょう。

また、HTML表現は例えば以下のようなものになります。リソース専用のテンプレートを用意してレンダリングしています。

<pre><span id="user">ユーザー名:koriym 性別:male </span></pre>

リソースは自身の構成やコンテンツに関して関心を持ちますが、表現には直接関心をもたず、セットされたリソースレンダラーに自身（$this)を渡してレンダリングを命じます。

レンダリングはリソースオブジェクトが文字列として評価されたときに行われます。

```php
echo $user;
// または
$userHtml = (string)$user;
```

レンダラーの保持はオプションです。もしリソースがレンダラーを持たなくても、コンテンツが文字列として評価可能ならコンテンツを表現にします。リソースコンテンツが文字列表現ができないフォーマット（例えばarray型）で、レンダラーも持たないときに文字列として評価されれば文字列”を返します。

## リソースリクエストのレンダ    リング

リソースリクエストが文字列として扱われたときには、リクエストを行い、その結果をレンダリングした結果の文字列が得られます。

```php
echo $request;
// または
$userHtml = (string)$request;
```

## レイジーリクエストとレイジーレンダリング

リソースクライアントが生成したリクエストオブジェクトは文字列として評価されるときに初めてリクエストを行います。

v0.1.0alphaでインストールされるindexデモ画面。これにapp://self/performanceというフレームワーク実行パフォーマンスを測定するappリソースがありindexページではpageのGETリクエストに応じてセットしています。

```php
$this['performance'] = $this
                       ->resource
                       ->get
                       ->uri(‘app://self/performance’)
                       ->request();
```
page / secで表示されている値がそうです。  
[<img src="/images/wp-content/uploads/2012/04/9bed6295cf4f6d5746d67365cba086de.png" alt="" title="スクリーンショット 2012-04-25 4.15.17" class="alignnone size-full wp-image-1358" />][6]

これはeagerが使われていないので、bodyのperformanceにapp://self/performanceへのGETリクエストがセットされたことになります。このリソースが実際に行われるのはコンパイル済みのテンプレートでこのリソースが出現するタイミングです。

smartyのコンパイル済みテンプレートではこの部分です。

```php
<p>&copy; 2012 <a href=”https://twitter.com/#!/bearsunday”>@BEARSunday</a> (<?php echo $_smarty_tpl->tpl_vars['performance']->value;?>
</p>
```

ここで初めてリクエストが行われます。パフォーマンスを測定するという目的からすればよページクラスのメソッドで計測するより、テンプレートで表示される直前に計測される値の方がより正確でしょう。またこのパフォーマンスリソースはテンプレートで出現しなければ計測されることもありません。実際に利用されるかされないかを気にしないでページはテンプレートにリクエストをセットすることができます。

## リソースキャッシュ

パフォーマンスappリソースを使ったページリソースをキャッシュしても、パフォーマンスリソースの値は正しく反映されることに注目してみてください。これはページが保持してるのはリソースリクエストの結果ではなく、リソースリクエストの方法だからです。リクエスト方法はキャッシュされ再利用されても、その値は毎回変わります。

## リソースまとめ

*   リソースリクエストはリクエストの生成と実行というライフスタイルを持ちます。
*   リソースはURIから生成され、リソースリクエストを実行するアダプターはURIのスキーマで決まります。
*   リソースリクエストはURI文字列で表現されます。
*   リソースリクエストの引き数はネームドパラメーターで渡されます
*   作成されたリソースリクエストはクロージャのように扱えます
*   リソースはレンダラーを個別に保持し自身をリソース状態からリソース表現にレンダリングすることができます。
*   リソース表現は文字列評価することで得られます。
*   レンダラーの使用はオプションです。なければコンテンツが表現にならないか検討します。
*   レンダラーはユーザーが記述することができます。
*   レンダラーはテンプレートエンジンを持ちます。設計上固定化されたものはありませんが、現在のデモではSmarty3が使われています。

他にもサービスを通じて一度しか行われないオブジェクトコンストラクション、不足している引き数を補充するパラメータープロバイダー、スキーマアダプターを構成するDSL、リソースの関係性を内部で持つリソースリンク、webブラウザでタブを開くようにリンク先を加えていくnewリンク、リソースリンクをクローリングしてリソースのリンク構造を構成するクローラーリンク、リソースリンクを露出することなくアプリケーション状態を変更するHATEOUS（Hypermedia as the Engine of Application State）等様々な機能がありますがまた別の機会に紹介したいと思います。<sup><a href="#footnote_4_1178" id="identifier_4_1178" class="footnote-link footnote-identifier-link" title="きっともうお腹一杯でしょう！">5</a></sup>

## 次回はRay.Di

次回の記事はDIです。BEAR.SundayはRay.Diというディペンデンシーインジェクターを用いてオブジェクトのコンストラションおよびインジェクションを行います。Ray.DiはBEAR.Sundayのために開発したGoogle GuiceクローンのDIフレームワークです。アノテーションを用いつつサービスコンテナにも依存しないよりクリーンなDIが可能です。

 [1]: /images/wp-content/uploads/2012/04/2bear-sunday-tmp-111219033305-phpapp02.002-001.png
 [2]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.005-001.png
 [3]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.005.jpg
 [4]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.008.jpg
 [5]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02.009.jpg
 [6]: /images/wp-content/uploads/2012/04/9bed6295cf4f6d5746d67365cba086de.png
