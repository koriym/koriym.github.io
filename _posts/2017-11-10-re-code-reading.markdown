---
layout: post
title: "Re: BEAR.Sundayをコードリーディングしたのでメモ程度にアウトプットする"
date: 2017-11-10 11:30:28 +0900
comments: true
categories: articles
tags: BEAR
---

このエントリーは
OTOBANK Engineering Blogの[BEAR.Sundayをコードリーディングしたのでメモ程度にアウトプットする](http://engineering.otobank.co.jp/entry/2017/10/20/120729)のReplyエントリーで、コードリーディングを補間する内容です。([@kalibora](https://twitter.com/kalibora)さん、ブログ記事ありがとうございます。)

元記事と合わせてお読みください。

> 3つのエントリポイント
>
> 何はともあれ開始地点であるエントリポイントを見てみます。 webからのアクセス用と、CLI用とで3つありました。
> それぞれの違いは単純にコンテキストを変えているだけ。 そして bootstrap.php を呼んでいるのみ。

エントリポイントではコンテキスト(`$context`)を指定して`bootstrap.php`ファイルを読み込みます。

３つのファイルは単に例としてあるだけなので、使用してないファイルは削除しても構いません。同様に新しいコンテキストファイルを追加するのも自由です。

条件を指定することもできます。例えばURIが`/api/`で始まるパスの時にはAPIとしてJSONを返し、その他はHTMLで返すサービスの時はこのようにします。

```php Webコンテキストによるアプリケーションコンテキストの変更
$context = strpos($_SERVER['REQUEST_URI'], '/api/') !== false ? 'hal-api-app' : 'html-app';
```

> bootstrap.phpでは何をしているのか？
> このように非常に短いスクリプトで全体の流れを記述しているのみ。

解説されてるようにスクリプト全体を記述しています。さらに短く記述するとこのようになります。

```php bootstrap.php
$context = PHP_SAPI === 'cli' ? 'cli-hal-app' : 'hal-app';

$app = (new Bootstrap)->getApp('MyVendor\MyApp', $context);
$request = $app->router->match($GLOBALS, $_SERVER);
try {
    $page = $app
        ->resource
        ->{$request->method}
        ->uri($request->path)($request->query)
        ->transfer($app->responder, $_SERVER);
    exit(0);
} catch (\Exception $e) {
    $app->error->handle($e, $request)->transfer();
    exit(1);
}
```

バッチ処理ならルーターも不要なのでこのようになります。

```php ミニマムなブートストラップコード
try {
    (new Bootstrap)->getApp('MyVendor\MyApp', 'prod-cli')
        ->resource
        ->post
        ->uri('/path/to/command')()
        ->transfer($app->responder, $_SERVER);
    exit(0);
} catch (\Exception $e) {
    $app->error->handle($e, $request)->transfer();
    exit(1);
}
```

さらに短く。単にコンソールでechoする出力で例外処理も不要なら一行になります。

```php 一行ブートストラップコード
echo (new Bootstrap)
->getApp('MyVendor\MyApp', 'prod-cli')
->resource
->post
->uri('/path/to/command')();
```

複数のアプリを組み合わせた出力を得たい場合には以下のようにできます。

```php 複数アプリを統合
$name = (new Bootstrap)->getApp('MyVendor\MyApp1', 'prod-cli')->resource->get->uri('/user')('id' => 'bear')['name'];
$role = (new Bootstrap)->getApp('MyVendor\MyApp2', 'prod-cli')->resource->get->uri('/role')('id' => 'bear')['role'];

echo json_encode(['name' => $name, 'role' => $role]);
```

アプリケーション全体を実行するスクリプトがフレームワーク本体にではなく、アプリケーションにあるので自由にカスタマイズできます。

> $app とは何者なのか？

`$app`は**オブジェクトグラフ**のルートオブジェクトです。

オブジェクトグラフとは何でしょうか？

> オブジェクト指向のアプリケーションは相互に関係のある複雑なオブジェクト網を含んでいます。オブジェクトはあるオブジェクトから所有されているか、他のオブジェクト（またはそのリファレンス）を含んでいるか、そのどちらかでお互いに接続されています。このオブジェクト網をオブジェクトグラフと呼びます。

DIのベストプラクティスとしてGoogleのGuiceでは以下の方法を勧めています。

> Your code should deal directly with the Injector as little as possible. Instead, you want to bootstrap your application by injecting one root object. The container can further inject dependencies into the root object's dependencies, and so on recursively. In the end, your application should ideally have one class (if that many) which knows about the Injector, and every other class should expect to have dependencies injected.


> 開発者のコードは、可能な限りInjectorを直接使うのを避けなければなりません。代わりに、1つのルートオブジェクトを注入してアプリケーションをブートストラップします。このルートオブジェクトのクラスは、依存する他のオブジェクト(`$app->router`や`$app->resourece`)を取得するためのDIする必要がり、依存するオブジェクトのクラスも同様に依存するオブジェクトのためのDIが必要です。
その代わりにルートの一つのオブジェクトに注入します。コンテナは、依存関係をルートオブジェクトの依存関係に注入を再帰的に行うことができます。
あなたのアプリケーションはInjectorについて知っている1つのクラスだけを持つのが理想です。その他のすべてのクラスは依存関係を注入することを期待するべきです。

オブジェクト網の一番最初のルートのオブジェクトが`$app`です。

可能な限りインジェクターをユーザーが直接使うことを避けるべきです。ライブラリにおいてもインジェクターを知るクラスを原則無しにします。ユーザーがコンテナを直接操作するのはDIではなく、アンチパターンのサービスロケーターです。(オブジェクトは他のオブジェクトの依存になっているのでで、通常のサービスクラスはユーザーがコンテナを触らずともDIで取得できるはずです。)

BEAR.Sundayでは基本２箇所だけ。ルートオブジェクトを生成する`Bootstrap`とリソースを生成するためのファクトリークラスです。

`bootstrap.php`ではその`$app`のプロパティだけを使ってアプリケーションを実行します。

リソースオブジェクトリクエスを埋め込む`@Embed`の機能は実質リソースオブジェクト(ResourceObject)のDIです。`@Embed`や`@Link`を使うとリソースファクトリーのコードを使うことなくルートの`$app`の取得の時のみインジェクターが利用されます。ResourceObject内では`$this->resource->uri()`でリソースオブジェクトを生成するより、`@Embed`でインジェクトすることを考慮してみましょう。

> AppInjector？
> AppInjector::getInstance() メソッドでは、指定したinterfaceに束縛されたインスタンスを、依存解決済みで返してくれる。

アプリケーションの`名前`と`コンテキスト`とインターフェイス（また抽象クラス）指定すると、インスタンスを取得することができるのがAppInjector（アプリケーションディペンデンシーインジェクター）です。
BEAR.Sundayでは複数のアプリケーションがそれぞれ名前空間を持ち同時に存在できます。

`AppInjector`はプロダクションコードでは（Gooogle Guiceのベストプラクティスの通り）Bootstrapで一度使われるだけですが、テストに有用です。

無名クラスを使って以下のように、モックやスタブを束縛することができます。


```php 無名クラスで上書き束縛
public function testAnonymousClassBinding()
    $injector = new AppInjector('FakeVendor\HelloWorld', 'hal-app');
    $module = new class extends AbstractModule {
        protected function configure()
        {
            $this->bind(FooInterface::class)->to(Foo::class);
        }
    };
app');
    $index = $injector->getOverrideInstance($module, Index::class);
    $name = $index(['id' => 1])->body['name'];
    $this->assertSame('BEAR', $name);
}
```

[http://bearsunday.github.io/manuals/1.0/ja/test.html](http://bearsunday.github.io/manuals/1.0/ja/test.html)

> （最初の1回目の場合は、依存解決したものをすべてフラットなPHPファイルとしてダンプする。これをコンパイル処理と呼んでいるみたい）

全ての依存ファイルのファクトリーコードは生のPHPファイルとしてダンプされます。インターフェイスだけで作られたシステムは、実際にどのオブジェクトがどのように生成されるか明らかにするのが難しい場合がありますがファクトリークラスを見れば明らかです。シングルトンかプロトタイプかも確認できます。詳細は[DI](http://bearsunday.github.io/manuals/1.0/ja/di.html)のデバックをご覧ください。

> $contexts = cli-hal-api-app であれば`MyVendor\MyPackage\Module\AppModule`...の順番で読み込まれる。しかし優先順位はその逆である。

これはGoFのデコレーターパターンです。最初に`AppModule`で束縛されたDIとAOPの設定を外側で"デコレート" 変更しています。

モードで振る舞いを変更するのではなく、後読み優先のモジュールで束縛したクラスを変更して振る舞いを変えています。
`cli-hal-api-app`であれば`AppModule`でされている束縛はその後の`ApiModule`や`CliModule`で変更することができます。


```php
public function foo()
{
  $isDebug = Config::get('app.debug');
  if ($isDebug) {
    $this->logger->log($text);
  }
}
```

などとメソッド内でモードを判定して、振る舞いを変えるのではなく

```php
public function __construct(LoggerInterface $logger)
{
  $this->logger = $logger;
}

public function foo()
{
  $this->logger->log($text); // 開発以外は何もしないNullLoggerが束縛されている
}
```

上記のように`LoggerInterface`に対する束縛をモジュールで変更します。条件や状態を少なくすることは、コード品質の向上に役立ちます。

> $requestとは？
> HTTPリクエストから、BEARで扱う形式への変換、マッピングというのがこの処理の肝なのではないだろうか。

その通りです。WebリクエストをPHPリクエストに変える（ディスパッチ）のためのルーターの結果の値オブジェクト [RouterMatch](https://github.com/bearsunday/BEAR.Sunday/blob/1.x/src/Extension/Router/RouterMatch.php)です。

`$request`には`$method`、`$path`、`$query`の値が保存されています。`$path`がリソースクラスに、$methodがリソースクラスのメソッドに、
名前付き引数（named parameters)の$queryがPHPの（順序）引数(oredered parameters)に変換されます。

リソースクラスではWebコンテキスト($_SERVERなどの値)がどのようになってるかを調べ回るようなコードは避けるべきで、リソースクラスの外側でWebコンテキストの値を全て単なるPHPの値に変換しておきます。そうすることコンソールとWebのどちらでも実行が可能でテストが容易なコードになります。

```php
$page = $app->resource->{$request->method}->uri($request->path)($request->query);
```

> $pageとは？
>

```php
> $page = $app->resource->{$request->method}->uri($request->path)($request->query);
```

元記事で順番に辿ってる通りです。

```php
> $resource = $app->resource; // BEAR\Resource\Resource
```

リソースクラアイントが取得され

```php
$resource = $resource->get; // BEAR\Resource\Resource
```

`get`リクエストをプロパティとして保存します

```php
$request = $resource->uri('app://self/path/to'); // BEAR\Resource\Request
```

`uri()`はリクエストオブジェクトののファクトリーメソッドです。

```php
$page = $request('key'=> 'value', 'hoge' => 'fuga']); // BEAR\Resource\ResourceObject
```

リクエストオブジェクトは`__invoke`を実装しているので関数のように直接実行できます。`__toString`メソッドも実装しているので文字列評価すると文字列になります。この時の文字がはリソースの状態の表現(representational resource state)です。

> 今回のまとめ
> $app が面白いですね。全部そこにまとまっているっていうのが。

$appはアプリケーションはシリアライズ可能でプロダクションではキャッシュされて実行されます。
１つのオブジェクト網なのでビジュアライゼーションも可能です。

![$app](/images/blog/app.png)(http://koriym.github.io/print_o/v1/libs/bear.sunday.html)

[$appのビジュアライゼーション](http://koriym.github.io/print_o/v1/libs/bear.sunday.html)

var_dump()などでは表現できないシングルトンオブジェクトなども表現できていることに気づかれるでしょう。重大なエラーが発生した時にバックトレースばかりでなく、`/var/log/`フォルダにあるログで`$app`がどのように生成されているかを確認することもできます。

アノテーションやDI、codegenを用いたAOPコード作成など膨大な本来は膨大な初期化コストがかかりますが、`$app`を１つのオブジェクトとして保存することにより[パフォーマンスの問題を解決](https://github.com/kenjis/php-framework-benchmark)しています。ResourceObjectも全てが最初にコンパイル（ファクトリーコードの生成）されるので、依存解決の問題がプログラムの途中で発生することがないと言うメリットもあります。
