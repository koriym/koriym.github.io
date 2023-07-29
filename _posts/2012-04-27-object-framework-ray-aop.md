---
title: Object Framework – Ray.Aop
author: admin
layout: post
permalink: /2012/04/object-framework-ray-aop/
categories:
  - BEAR
  - PHP
tags:
  - AOP
  - AOPアライアンス
  - アスペクト
  - インターセプター
  - オブジェクトフレームワーク
  - ランタイム
  - 横断的
  - 遅延束縛
  - 関心事の分離
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2012/04/object-framework-ray-aop/">Tweet</a>
</div>

# Apect Oriented Design

## メソッド・インターセプター

[<img src="/images/wp-content/uploads/2012/04/rayaop011.png" alt="" title="rayaop011" class="alignnone size-full wp-image-1410" />][1]  
例えばテスト用途にどんな引き数が渡されても特定の同じ値を返さなければならないとします。あるいはアジリティを重視した開発で、メソッド内のコードや利用データベースが用意できていない段階でも適当に用意した値を返す必要があるとします。

このような場合、通常はテスティングフレームワークを使いモックオブジェクトを生成して利用します。BEAR.SundayのRay.Diのモジュールでモックオブジェクトを用意して差し替える事もできます。しかしRay.Aopの提供するメソッドインターセプターを使えば更に簡単です。

メソッドインターセプターはメソッドの実行を横取りして(interceptして）**代理実行**します。モックオブジェクトは対象オブジェクトを入れ替えますが、インターセプターは対象オブジェクトとそれを利用するコンシュマークラスの間に割り込み（インターセプト）します。

まずは基本になるオリジナルのメソッド実行と同じ動作をするインタセプターのコードです。

[<img src="/images/wp-content/uploads/2012/04/rayaop.012.png" alt="" title="rayaop.012" class="alignnone size-full wp-image-1412" />][2]

<div class="codecolorer-container php blackboard" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2">class</span> GreetingInterceptor implements MethodInterceptor <span class="br0">{</span> &nbsp; &nbsp; <span class="co4">/** &nbsp; &nbsp; * (non-PHPdoc) &nbsp; &nbsp; * @see Ray\Aop.MethodInterceptor::invoke() &nbsp; &nbsp; */</span> &nbsp; &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> invoke<span class="br0">(</span>MethodInvocation <span class="re0">$invocation</span><span class="br0">)</span> &nbsp; &nbsp; <span class="br0">{</span> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re0">$result</span> <span class="sy0">=</span> <span class="re0">$invocation</span><span class="sy0">-></span><span class="me1">proceed</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">;</span> &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">return</span> &nbsp;<span class="re0">$result</span><span class="sy0">;</span> &nbsp; &nbsp; <span class="br0">}</span> <span class="br0">}</span>
  </div>
</div>

MethodInterceptorインターフェイスのinvoke（実行）というメソッドにはMethodInvocation（メソッド実行）オブジェクト$invocationが渡されます。

メソッド実行オブジェクトはメソッドの実行に必要な全ての知識（対象インスタンス、メソッド名、引き数等）を持っています。オリジナルのメソッドを実行するためには*$invocation->proceed();*を実行します。

この実行の前後に処理を記述したりすることで元も処理をまたぐ事ができます。引き数を操作したり変更したりすることもできます。<sup><a href="#footnote_0_1373" id="identifier_0_1373" class="footnote-link footnote-identifier-link" title="BEAR.SaturdayではAroundアドバイスとして実装されていものと同様のものです。">1</a></sup>またインターセプターを同じメソッドに複数適用することもできます。

[<img src="/images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.015.png" alt="" title="bear-sunday-tmp-111219033305-phpapp02-1.015" class="alignnone size-full wp-image-1418" />][3]

テスト用に常にここのメソッドが”HelloTest”を返す為には以下のように変更します。

```php
class GreetingInterceptor implements MethodInterceptor
{
    /**
    * (non-PHPdoc)
    * @see Ray\Aop.MethodInterceptor::invoke()
    */
    public function invoke(MethodInvocation $invocation)
    {
        $result = $invocation->proceed();
        return  $result;
    }
}
```

このGreetingリソースに限らず、何のリソースのメソッドが”HelloTest”を返す為すためには以下のように変更します。
```php
class GreetingInterceptor implements MethodInterceptor
{
    /**
    * (non-PHPdoc)
    * @see Ray\Aop.MethodInterceptor::invoke()
    */
    public function invoke(MethodInvocation $invocation)
    {
        // $result = $invocation->proceed();
        return  “HelloTest”;
    }
}
```

このインターセプターを特定のクラス、特定のメソッドにバインドするのも「モジュール」で行います。このバインドはsandbox\Resource\App\Greetingクラス（およびその継承したクラス）のどのメソッドにもMockInterceptorインターセプターを適用します。

```php
class MockResourceInterceptor
{
    private $mock;
    public function __construct($mock)
    {
        $this->mock = $mock;
    }
    public function invoke(MethodInvocation $invocation)
    {
        $return $this->mock;
    }　
}
```

バインド対象を指定するためのマッチャーはアノテーションを指定したり、Callableオブジェクトを指定することもできます。

## @Cacheアノテーション


BEAR.Sundayはいくつかのインターセプターを用意しています。その内、Cacheインターセプターは特に有用でしょう。このアノテーションはメソッド実行の結果を指定の秒数キャッシュします。

```php

  /**
   * @Cache(60)
   */
  public function onGet($lang = ‘en’)
  {
      …
  }
```

このインターセプターのソースを見てましょう。

```php
public function __construct(Cache $cache) {
    $this->cache = $cache;
}

public function invoke(MethodInvocation $invocation) {
    $class = get_class($invocation->getThis());
    $args = $invocation->getArguments();
    $id = $this->getId($class, $args);
    $saved = $this->cache->fetch($id);
    if ($saved) {
        return unserialize($saved);
    }
    $data = $invocation->proceed();
    $annotation = $invocation->getAnnotation();
    $time = $annotation->time;
    $this->cache->save($id, serialize($data), $time);
    return $data;
    }
```

インターセプターはというMethodInvocationインターフェイスを実装します。2 引き数や対象メソッドをキーにキャッシュにを生成していて@Cacheアノテーションで指定された括弧内の秒数だけキャッシュデータを再利用するようになっています。
@Cacheアノテートされたされたリソースはアノテートがされただけでこれが実際にはどのインターセプターがバインドされるのかはこのクラスからは宣言していません。3 このアノテーションが実際にどのインターセプターが適用されるのか、あるいはそもそもインターセプターが適用されない（開発時など）といった構成知識に関わりがありません。Ray.Diの@Injectアノテーションと同じように構成は利用される側でなく利用する側が持ちます。
            
## ランタイムインジェクター


BEAR.Sundayのオブジェクトの実行は最初の１リクエストでオブジェクトフラフのコンストラクションを完了する<strong>コンパイル</strong>と、以降の<strong>ランタイム</strong>に分けられます。
コンパイルでリクエストをまたいで再利用可能なオブジェクトをつくるために、リクエスト毎にディペンデンシーを変えてインジェクトをしたりすることはできません。<sup><a href="#footnote_3_1373" id="identifier_3_1373" class="footnote-link footnote-identifier-link" title="またPDOなどのPHP標準組み込みオブジェクトもインジェクトできません。">4</a></sup>
例えばDBオブジェクト<sup><a href="#footnote_4_1373" id="identifier_4_1373" class="footnote-link footnote-identifier-link" title="PDOと違って組み込みオブジェクトではないので@Injectでコンパイル時にインジェクトすることは可能です">5</a></sup> のmaster / slaveをメソッドに応じて自動で選択するために、GET（読み込み）かそれ以外のメソッドでインジェクトを変えるという事はRay.Diのインジェクターではできません。
この場合インターセプターを使ってDBオブジェクトをメソッドにセットしてやることができます。
インターセプターはメソッド実行の情報が渡されるので、実行メソッド名をみてmaster/slaveのDBを選択することができます。master/slaveに限らずユーザーIDに応じたDB選択や、DBに応じた初期化などもインターセプターで記述できます。リソースをリクエストするクライントもそれを受けるappリソースも本来の仕事、つまり本質的関心(core concern)にのみ専念し、DBオブジェクトの準備というリソースをまたいだ<a href="http://netail.net/aosdwiki/index.php?%B2%A3%C3%C7%C5%AA%B4%D8%BF%B4%BB%F6">横断的関心事</a>(cross cutting concern)から<a href="http://ja.wikipedia.org/wiki/%E9%96%A2%E5%BF%83%E3%81%AE%E5%88%86%E9%9B%A2">関心を分離</a>することができます。

### Postsリソースクラス
```php
/**
 * Posts
 *
 * @Db
 */
class Posts extends ResourceObject implements DbSetter
{
    /**
     * Table
     *
     * @var string
     */
    private $table = ‘posts’;
    /**
     * DB
     *
     * @var Doctrine\DBAL\Connection
     */
    private $db;

    /**
     * Set DB
     *
     * @param Connection $db
     *
     * @return void
     */
    public function setDb(Connection $db = null)
    {
        $this->db = $db;
    }

    /**
     * Get
     *
     * @return array
     */
    public function onGet()
    {
        $sql = “SELECT id, title, body, created, modified FROM {$this->table}“;
        $stmt = $this->db->query($sql);
        $this->body = $stmt->fetchAll(PDO::FETCH_ASSOC);
        return $this;
    }

    /**
     * Post
     *
     * @param string   $title
     * @param string   $body
     * @param DateTime $created
     * @param DateTime $modified
     *
     * @return \sandbox\Resource\App\Posts
     */
    public function onPost($title, $body, $created = null, $modified = null)
    {
        $this->db->insert($this->table, ['title' => $title, 'body' => $body]);
        $this->code = 204;
        return $this;
    }
}
```

#### @Dbアノテーションクラス

```php
/**
 * Db
 *
 * @Annotation
 * @Target(“CLASS”)
 *
 * @package    BEAR.Framework
 * @subpackage Annotation
 */
final class Db
{
}
```

#### モジュール内でDbインジェクターをバインド

```php
$this->bindInterceptor(
            $this->matcher->annotatedWith(‘BEAR\Framework\Annotation\Db’), // クラスに@Dbとアノテートされた全てのクラス
            $this->matcher->any(), // 全てのメソッド
            [$dbInjector] // 複数バインドできます
        );
```
## Conclusion
<strong>Ray.Di</strong>のインジェクションシステムはコンシュマーとディペンデンシーの関係を疎にしアプリケーション構成を柔軟にしますがコンパイルされた関係性は再利用されオブジェクトとオブジェクトの結びつき（オブジェクトグラフ）はリクエストをまたいでも変わりません。つまりRay.Diでは早期束縛の依存ののみを扱います。
対してRay.Aopのメソッドインターセプターはコンシュマーとメソッドを動的に束縛します。<strong>横断的関心事</strong>メソッドをコールしてもそれが実際にオリジナルなメソッドをコールしたかにコンシュマーは関心を払いません。メソッド内ではDBデータを読み込んでるのに、バインドされたインターセプターはmemcacheからデータを読みその値を返し、オリジナルのメソッドはデータ更新の際の最初の１度しか呼ばれないかもしれません。関係性は外部で構成され、その束縛はリクエストの実行時に決定されます。つまり遅延束縛です。
このようにRay.AopのインセプターはAOPとしてメソッドの振る舞いを返るだけでなく、ランタイムでの依存解決にも使われます。例えばDBオブジェクトは実際のメソッドリクエストがあるまで、master/slave/partioningどのDB接続をするべきかは決定することができません。インターセプターとして束縛された<strong>DBインジェクター</strong>が依存を動的に注入します。
現代的なPHPフレームワークの多くは、アプリケーションコントローラーの役割を様々な関心をアスペクトととらえそれぞれの実装がなされています。例えばフィルターチェーンであったり、シグナルスロット、イベンドディスパッチャー、イベントサブスクライバー実装パターンや呼び方が違っても問題をアスペクトとしてそれぞれの解決をしようとしているのは同じように思えます。
Ray.AopでのAOPはコンシュマーにもサービスにもAOPフレームワークの依存がなく利用するためのサービスクラスには、イベント通知などイベントハンドリングのための仕事をする必要はありません。Ray.Diで生成されるアスペクトが織り込まれるサービスクラスは、イベントハンドリングをするサービスに含まれた状態で渡されます。該当メソッドの適用インターセプター知識をそれぞれが保持していて、イベントハンドリングがそれぞれのサービス<sup><a href="#footnote_5_1373" id="identifier_5_1373" class="footnote-link footnote-identifier-link" title="詳しくはサービスを含んだプロキシー">6</a></sup>内で行われます。<sup><a href="#footnote_6_1373" id="identifier_6_1373" class="footnote-link footnote-identifier-link" title=" この仕組みはBEAR.Resourceでリソースそれぞれがレンダラーを持っているのと似ています。サービス（レンダラー、イベントハンドラー）にデータ（テンプレート、イベントシグナル）を渡すのではなく、オブジェクトがサービスを内包しているのです。 ">7</a></sup>
Ray.Aopを使ったアプリケーションコントローラー<sup><a href="#footnote_7_1373" id="identifier_7_1373" class="footnote-link footnote-identifier-link" title="フォームや認証、セキュリティ、ログ">8</a></sup>
フレームワークやアプリケーションがコンシュマーとサービスの利用の関係をダイナミックにします。これを完全に外側から構成できる拡張性、関心の分離の促進によるソフトウエア品質の向上には期待をしています。<sup><a href="#footnote_8_1373" id="identifier_8_1373" class="footnote-link footnote-identifier-link" title="一方このパターンを採用する事で発生するデメリットにも注意深く対処していかなければなりません。">9</a></sup>
v0.1.0alphaリリースを機に<a href="/blog/2012/04/bear-resource/">BEAR.Resource</a>、<a href="/blog/2012/04/di/">Ray.Di</a>、Ray.AopとBEAR.Sundayのオブジェクトフレームワークというべきものについて記事を一つ一つかいてきました。Ray.Diはオブジェクトの生成を、Ray.Aopはそのオブジェクトのメソッドの利用にこれまでにない拡張性と機能性を与えます。そうやってできたオブジェクトにRESTという制約を被せ、オブジェクトの関係を（データではなく）DSLによって記述される関係性で結合しようとするのがBEAR.Resourceです。Ray.Diはオブジェクトグラフを、BEAR.Resourceはリソースグラフを構成しようとし、それぞれのリソースクラスは自らを構成しようとします。

                  
<div class="wp_social_bookmarking_light">
<div>
</div>

<div>
</div>

<div>
<a href="http://b.hatena.ne.jp/entry//blog/2012/04/object-framework-ray-aop/" title="はてなブックマーク - Object Framework – Ray.Aop" rel="nofollow" class="wp_social_bookmarking_light_a" target="_blank"><img src="http://b.hatena.ne.jp/entry/image//blog/2012/04/object-framework-ray-aop/" alt="はてなブックマーク - Object Framework – Ray.Aop" title="はてなブックマーク - Object Framework – Ray.Aop" class="wp_social_bookmarking_light_img" /></a>
</div>

<div>
<div id="___plusone_0" style="height: 20px; width: 90px; display: inline-block; text-indent: 0px; margin: 0px; padding: 0px; background: none repeat scroll 0% 0% transparent; border-style: none; float: none; line-height: normal; font-size: 1px; vertical-align: baseline;">
</div>
</div>

<div>
<a href="http://www.tumblr.com/share?v=3&u=http%3A%2F%2Fwww.bear-project.net%2Fblog%2F2012%2F04%2Fobject-framework-ray-aop%2F&t=Object%20Framework%20%26%238211%3B%20Ray.Aop" title="" style="display:inline-block; text-indent:-9999px; overflow:hidden; width:61px; height:20px; background:url('http://platform.tumblr.com/v1/share_2.png') top left no-repeat transparent;"></a>
</div>
</div>


<br class="wp_social_bookmarking_light_clear" /> <ol class="footnotes">
<li id="footnote_0_1373" class="footnote">
BEAR.SaturdayではAroundアドバイスとして実装されていものと同様のものです。 [<a href="#identifier_0_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_1_1373" class="footnote">
これはJavaの<a href="http://aopalliance.sourceforge.net/">AOPアライアンス</a>の<a href="http://aopalliance.sourceforge.net/doc/org/aopalliance/intercept/MethodInterceptor.html">MethodInterceptor</a>を元にしたもので、Google Guice, Spring, SeasorのAOPもこのAOPアラインアンスのインターフェイス群を実装しています。 [<a href="#identifier_1_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_2_1373" class="footnote">
前バージョンのBEAR.Saturdayでは実クラスを指定していて、アスペクトという関心の分離と適用はできたのですがそれが固定化されていました。 [<a href="#identifier_2_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_3_1373" class="footnote">
またPDOなどのPHP標準組み込みオブジェクトもインジェクトできません。 [<a href="#identifier_3_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_4_1373" class="footnote">
PDOと違って組み込みオブジェクトではないので@Injectでコンパイル時にインジェクトすることは可能です [<a href="#identifier_4_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_5_1373" class="footnote">
詳しくはサービスを含んだプロキシー [<a href="#identifier_5_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_6_1373" class="footnote">
この仕組みはBEAR.Resourceでリソースそれぞれがレンダラーを持っているのと似ています。サービス（レンダラー、イベントハンドラー）にデータ（テンプレート、イベントシグナル）を渡すのではなく、オブジェクトがサービスを内包しているのです。 [<a href="#identifier_6_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_7_1373" class="footnote">
フォームや認証、セキュリティ、ログ [<a href="#identifier_7_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
<li id="footnote_8_1373" class="footnote">
一方このパターンを採用する事で発生するデメリットにも注意深く対処していかなければなりません。 [<a href="#identifier_8_1373" class="footnote-link footnote-back-link">↩</a>]
</li>
</ol>

 [1]: /images/wp-content/uploads/2012/04/rayaop011.png
 [2]: /images/wp-content/uploads/2012/04/rayaop.012.png
 [3]: /images/wp-content/uploads/2012/04/bear-sunday-tmp-111219033305-phpapp02-1.015.png