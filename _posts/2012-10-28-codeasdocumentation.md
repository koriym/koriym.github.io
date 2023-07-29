---
title: CodeAsDocumentation
author: admin
layout: post
permalink: /2012/10/codeasdocumentation/
categories: articles
tags:
  - BEAR
  - PHP
  - フレームワーク
  - clean code
  - CodeAsDocumentation
  - goto
  - self-descriptive
  - アノテーション
  - スクリプト
  - リンク
  - 自己記述的
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2012/10/codeasdocumentation/">Tweet</a>
</div>

## コードがドキュメントだ

> なぜコードが重要なドキュメントなのかというと、 詳細かつ正確なドキュメントはコードしかないからだ &#8212; Martin Fowler 

## アプリケーションシーケンス

どのようなシーケンス<sup><a href="#footnote_0_1035" id="identifier_0_1035" class="footnote-link footnote-identifier-link" title="あらかじめ定められた順序または手続きに従って制御の各段階を逐次進めていく制御">1</a></sup><sup><a href="#footnote_1_1035" id="identifier_1_1035" class="footnote-link footnote-identifier-link" title="http://ja.wikipedia.org/wiki/%E3%82%B7%E3%83%BC%E3%82%B1%E3%83%B3%E3%82%B9%E5%88%B6%E5%BE%A1">2</a></sup> によりフレームワーク／アプリケーションが実行されるかはフレームワークの設計思想を強く反映していて、柔軟度にも違いがあります。ほぼ固定的なものから、イベントシステムで柔軟性を持たせたもの、それらはアプリケーションテンプレートのようなWebフレームワークと、ライブラリ指向のフルスタックフレームワークで異なってきます。

しかし、しばしばこのシーケンスは分かりにくいものです。

BEAR.Sundayではこのシーケンスをアプリケーションの構成だと考え、完全にアプリケーションドメインのものにならないかと考えました。基本シークエンスをスクリプトにより簡潔に表現し可読性を高め、自由に編集できるようにします。特定の処理を追加したり、割込ませるためにはそのスクリプトを直接編集します。<sup><a href="#footnote_2_1035" id="identifier_2_1035" class="footnote-link footnote-identifier-link" title="CakePHP のシーケンス図を作ってみたという記事を読みました。CakePHP2のアプリケーション／フレームワークのシーケンスが分かるという記事なのですが、面白いと思ったと同時に現在のフレームワークのシーケンスの分かりにくさを表している事でもないかと考えました。
">3</a></sup>

{% highlight php %}
/** @global $mode application configuration mode */
// Clear
require dirname(__DIR__) . '/scripts/clear.php';
// Application instance with loader
$mode = 'Prod';
$app = require dirname(__DIR__) . '/scripts/instance.php';
// Dispatch
list($method, $pagePath, $query) = $app->router->match($GLOBALS);
// Request
try {
    $page = $app
    ->resource
    ->$method
    ->uri('page://self/' . $pagePath)
    ->withQuery($query)
    ->eager
    ->request();
} catch (NotFound $e) {
    $code = 404;
    goto ERROR;
} catch (BadRequest $e) {
    $code = 400;
    goto ERROR;
} catch (Exception $e) {
    $code = 503;
    error_log((string)$e);
    goto ERROR;
}
// Transfer
OK: {
    $app->response->setResource($page)->render()->prepare()->send();
    exit(0);
}
ERROR: {
    http_response_code($code);
    require dirname(__DIR__) . "/http/{$code}.php";
    exit(1);
}
{% endhighlight %}

これがアプリケーションスクリプトです。通常のOOPを使ったオブジェクトの世界ではなく、シェルスクリプトのように処理手順が記述してあるスクリプトの世界です。

アプリケーションオブジェクトはアプリケーションスクリプトが使う全てのサービス（オブジェクト）を保持したオブジェクトです。スクリプトで取得して１つの$appという変数に代入しています。アプリケーションスクリプトでそのサービスを使って全体のフローを簡潔に構成します。

### ページリソースで構成の意図を

アプリケーションスクリプトではページリソースのURIを組み立て、ページリソースリクエストを行いその結果を出力しています。

ページリソースは自らをページとして構成するのが役割ですが、**その構成意図はコードで表されます。**

{% highlight php %}
class Posts extends Page
{
    use ResourceInject;
    public $body = [
        'posts' => ''
    ];
    /**
     * @Cache
     */
    public function onGet($id)
    {
        $this['posts'] = $this->resource
            ->get
            ->uri('app://self/blog/posts')
            ->withQuery(['id' => $id])
            ->request();
        return $this;
    }
}
{% endhighlight %}

### セットすべきコンテンツ

$bodyプロパティはこのページでコンテンツ&#8217;posts&#8217;を用意する必要があるのを表しています。

### 指定可能なGETクエリー

GETリクエストにはidクエリーの指定が必須であることも表されています。

### コンテンツとリソースをリンク

メソッド内では、コンテンツ**posts**を**app://self/blog/posts?id=$id**リソースにするという意図が示されてます。

app://self/blog/postsが何を示すのかはこのコードには現れてないのに注意してください。これが特定のPHPクラスを指すのか、特定ファイルの内容なのか、あるいはリモートアクセスの結果なのか、ここでは指定されていません。**app://self/blog/postsというURIで表されるリソースを自身のpostsコンテンツにするという意図**が表されているだけです。<sup><a href="#footnote_3_1035" id="identifier_3_1035" class="footnote-link footnote-identifier-link" title="もしかしたらThriftで繋がれたC++モジュールが何かの実行を行ってくれるのかもしれません">4</a></sup>

このメソッドには@Cacheというアノテーションがありますが、**キャッシュを行うという意図**が表されてるだけです。実際にどのようなキャッシュアダプターがどのようにキャッシュを行うかはこのコードには現れません。

これは単に設定ファイルをアノテーションにしただけではないのに注意してください。アプリケーションはbootstrap時にこの意図をみて、このメソッドとキャッシュの実装アスペクトを織り込みます。

### フォームの仕様

以下はPOSTメソッドはフォームからPOSTサブミットのリクエストインターフェイスです。  
{% highlight php %}
    public function onPost(
        $name,
        $gender,
        $age = null,
        $hobby = null,
        $lang = 'PHP'
    ) {
{% endhighlight %}
ここではPOSTメソッドでサブミットされるフォームに対してのリクエストインターフェイスがメソッドシグネチャーで表されています。$nameと$genderは入力必須、その他はオプションで$langを入力しなければ&#8221;PHP&#8221;になります。

リクエストサービスオブジェクトから値を取り出す方法と比べて、PHPのメソッドとHTTPリクエストがシームレスに統合され、**コードがPOSTフォームのドキュメンテーションになっています。**<sup><a href="#footnote_4_1035" id="identifier_4_1035" class="footnote-link footnote-identifier-link" title="webに&rdquo;型&rdquo;がないようにweb言語であるPHPにスカラータイプヒントがありません">5</a></sup>

### リソースのリンク

{% highlight php %}
    public $links = [
        'payment' => [
            Link::HREF => 'app://self/restbucks/payment{?id}',
            Link::TEMPLATED => true
        ],
    ];
{% endhighlight %}
リソースの関係はAPIドキュメント内だけにあるのではなく、コードにも存在しています。この例では注文IDと支払URIの関係がリンクで表されています。

### アプリケーションリソース

{% highlight php %}
    /**
     * @Time
     * @Transactional
     * @CacheUpdate
     */
    public function onPost($title, $body)
    {
        $values = [
            'title' => $title,
            'body' => $body,
            'created' => $this->time
        ];
        $this->db->insert($this->table, $values);
        //
        $lastId = $this->db->lastInsertId('id');
        $this->code = Code::CREATED;
        $this->links['new_post'] = [Link::HREF => "app://self/posts/post?id={$lastId}"];
        $this->links['page_new_post'] = [Link::HREF => "page://self/blog/posts/post?id={$lastId}"];
        return $this;
    }
{% endhighlight %}

このアプリケーションリソースメソッドではデータベースオブジェクトを利用してSQLを発行しますが、ここではアノテーションが実装意図を表すドキュメンテーションになっています。

@Transactionalとアノテートするとこのメソッド内のクエリーはトランザクションとして扱われますが、@Cacheと同様、アノテーションそのものがトランザクションの機能をもっているわけではありません。@Transactionalというアノテート（注記）が（意図を表すドキュメンテーションとして扱われ）アプリケーションでトランザクションインターセプターと合成されています。

## 構造、意図、実装

アプリケーションの構造がアプリケーションスクリプトで表され、アプリケーションを構成するページリソースでは構成の意図が表されています。そのページリソースを構成するためのアプリケーションリソースでは、その構成のための実装が行われますが横断的実装はアスペクトで表され可能な限り下位のレイヤーで実装を行おうとします。

上位のレイヤーでは構成意図を、下位のレイヤーでその実装を行うことでアプリケーションに解決空間の構成と実装のレイヤリングをフレームワークとして与えようとします。

### アプリケーションオブジェクト

アプリケーションスクリプトからデーターベース操作を行う下位レイヤーまで、レイヤーを上から下に見て来ましたが、最期にそのトップのアプリケーションオブジェクトをみてみましょう。

アプリケーションという[オブジェクトグラフ][1]の頂点になるこのインスタンスはアプリケーションスクリプトが使う全てのサービス（オブジェクト）を保持するのがその最大責務です。

{% highlight php %}
final class App implements Context
{
    /** application dir path @var string */
    const DIR = __DIR__;
    public $injector;
    public $resource;
    public $logger;
    public $response;
    public $exceptionHandler;
    public $router;
    public $globals;
    /**
     * Constructor
     *
     * @param \Ray\Di\InjectorInterface                        $injector         Dependency Injector
     * @param \BEAR\Resource\ResourceInterface                 $resource         Resource client
     * @param \BEAR\Sunday\Exception\ExceptionHandlerInterface $exceptionHandler Exception handler
     * @param \BEAR\Sunday\Application\Logger                  $logger           Application logger
     * @param \BEAR\Sunday\Web\ResponseInterface               $response         Web / Console response
     * @param \BEAR\Sunday\Web\RouterInterface                 $router           Resource cache adapter
     * @param \BEAR\Sunday\Web\GlobalsInterface                $globals          GLOBALS value
     *
     * @Inject
     */
    public function __construct(
        InjectorInterface $injector,
        ResourceInterface $resource,
        ExceptionHandlerInterface $exceptionHandler,
        ApplicationLogger $logger,
        ResponseInterface $response,
        RouterInterface $router,
        GlobalsInterface $globals
    ) {
        $this->injector = $injector;
        $this->resource = $resource;
        $this->response = $response;
        $this->exceptionHandler = $exceptionHandler;
        $this->logger = $logger;
        $this->router = $router;
        $this->globals = $globals;
    }
}
{% endhighlight %}

オブジェクトの生成と実行が完全に分離されたDIシステムを持つBEAR.Sundayでは、アプリケーションスクリプトが利用するサービスの全ては、このアプリケーションのコードで表されます。

アプリケーションの構成に必要な全てのサービスがここに記述されているはずで、要不要が生じればこのコードに反映されることになります。

## Readability, Simplicity and Self-documenting

**簡潔で、可読性が高く、[自己記述的][2]であること** &#8211; これはBEAR.Sundayが開発当初から一貫して指向してきたことです。

これは単に表記の問題ではありません。解決しようとする問題の本質を明らかにして、本質的関心だけを浮かび上がる、そういう記述を指向しているということです。そのためにはスコープを適切に持ち、関心を分離し付随的な関心を削ぎ落とす事が必要でこれには多くの前提が必要です。

生成と利用を完全に分離したDIや、横断的関心事を注記<sup><a href="#footnote_5_1035" id="identifier_5_1035" class="footnote-link footnote-identifier-link" title="アノテーション">6</a></sup>に応じて織り込むAOP、アプリケーションプロトコル/プラットフォームであるHTTPとフレームワークとの融合を即するRESTオブジェクト、これらのその前提です。これらDI/AOP/RESTを道具として指向するのではなく、簡潔で可読性が高く自己記述的なアプリケーションコーディングの前提、オブジェクトフレームワークとして機能させようとしています。

これまでの開発で最も多くのスクラップ＆ビルドを繰り返しよりよい解を求めて試行錯誤を繰り返し注力したたのはフレームワークの機能ではなく、アプリケーション記述のあり方です。<sup><a href="#footnote_6_1035" id="identifier_6_1035" class="footnote-link footnote-identifier-link" title="これはアプリケーションドメインなのでアプリケーションアーキテクトが責任を持ちますが、そのスタンダードを示す事が大切だと考えより注力しました。">7</a></sup> 完成が近づくにつれ、その点の関して完成度が高まったと考え、マーティンファウラーの[CodeAsDocumentation][3]というタイトルで記事にしました。

次の11/03 22:00から[PHP Matsuri2012][4]というイベントでBEAR.Sunday初のワークショップを行う事になりました。当日は簡単なリソース作成を通じて、今回の記事のようなことやDI/AOP、それにOOPや設計の話が出来ればと思ってます。よろしくお願いします。

<ol class="footnotes">
  <li id="footnote_0_1035" class="footnote">
    あらかじめ定められた順序または手続きに従って制御の各段階を逐次進めていく制御 [<a href="#identifier_0_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_1035" class="footnote">
    http://ja.wikipedia.org/wiki/%E3%82%B7%E3%83%BC%E3%82%B1%E3%83%B3%E3%82%B9%E5%88%B6%E5%BE%A1 [<a href="#identifier_1_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_2_1035" class="footnote">
    <a href="http://blog.xao.jp/blog/cakephp/sequence-diagram-of-a-part-of-cakephp-for-my-studying/">CakePHP のシーケンス図を作ってみた</a>という記事を読みました。CakePHP2のアプリケーション／フレームワークのシーケンスが分かるという記事なのですが、面白いと思ったと同時に現在のフレームワークのシーケンスの分かりにくさを表している事でもないかと考えました。 [<a href="#identifier_2_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_3_1035" class="footnote">
    もしかしたらThriftで繋がれたC++モジュールが何かの実行を行ってくれるのかもしれません [<a href="#identifier_3_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_4_1035" class="footnote">
    webに&#8221;型&#8221;がないようにweb言語であるPHPにスカラータイプヒントがありません [<a href="#identifier_4_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_5_1035" class="footnote">
    アノテーション [<a href="#identifier_5_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_6_1035" class="footnote">
    これはアプリケーションドメインなのでアプリケーションアーキテクトが責任を持ちますが、そのスタンダードを示す事が大切だと考えより注力しました。 [<a href="#identifier_6_1035" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://en.wikipedia.org/wiki/Object_graph
 [2]: http://en.wikipedia.org/wiki/Self-documenting
 [3]: http://capsctrl.que.jp/kdmsnr/wiki/bliki/?CodeAsDocumentation
 [4]: http://www.phpmatsuri.net/2012/
