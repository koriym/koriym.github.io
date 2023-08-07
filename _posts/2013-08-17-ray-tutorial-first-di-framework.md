---
title: 'Ray.Tutorial - First DI framework'
author: admin
layout: post
permalink: /first-di-framework
custom_permalink:
  - first-di-framework
categories: blog
tags:
  - BEAR
---
<h1>初めてのDIフレームワーク</h1>
<h2>準備</h2>
 1. PHP5.4+で動作します。mysqlで予め<a href="https://github.com/koriym/Ray.Tutorial/blob/master/doc/todo.sql">テーブルを作成</a>しておきます。
 2 フォルダをつくります。

{% highlight bash %}
$ mkdir ray.tutorial
$ cd ray.tutorial
{% endhighlight %}

まずは手動でインジェクションするコード<a href="https://github.com/koriym/Ray.Tutorial/blob/develop/src/todo2-manual-injection.php">ソース</a>を入力して実行します。

{% highlight php %}
<?php
class Todo
{
    /**
     * @var PDO
     */
    private $pdo;

    /**
     * @param PDO $pdo
     */
    public function __construct(PDO $pdo)
    {
        $this->pdo = $pdo;
    }

    /**
     * @param string $todo things to do
     */
    public function add($todo)
    {
        $stmt = $this->pdo->prepare('INSERT INTO TODO (todo) VALUES (:todo)');
        $stmt->bindParam(':todo', $todo);
        $stmt->execute();
    }
}
$pdo = new PDO('mysql:dbname=test;host=localhost');
$todo = new Todo($pdo);
$todo->add('Get laundry');
{% endhighlight %}

実行してみます。

{% highlight php %}
$ php manual-di.php
{% endhighlight %}

データベースにtodoが入力されたか、コンソールかツール等で確認します。<sup><a href="#footnote_0_2143" id="identifier_0_2143" class="footnote-link footnote-identifier-link" title="あるいはSELECTをするメソッドを追加してください！">1</a></sup>
確認できましたか？OK?
では、次にcomposerのプロジェクトを作ってこのクラスをDI化してみましょう。
<h2>composerでRay.Di依存の空プロジェクトを作る</h2>
まずはcomposerをダウンロードします。

{% highlight bash %}
$ curl -sS https://getcomposer.org/installer | php
{% endhighlight %}

composerを使ってRay.Diを使うプロジェクトを作ります。

{% highlight bash %}
$ php composer.phar init
{% endhighlight %}

すると色々質問されるので、ray/diのバージョン* (最新の安定板)をインストールするように答えます。

{% highlight php %}
  Welcome to the Composer config generator
This command will guide you through creating your composer.json config.
Package name (<vendor>/<name>) [akihito/ray.tutorial]:
Description []:
Author [Akihito Koriyama <akihito .koriyama@gmail.com>]:
Minimum Stability []:
License []:
Define your dependencies.
Would you like to define your dependencies (require) interactively [yes]?
Search for a package []: ray/di
Found 15 packages matching ray/di
   [0] ray/di
   [1] ray/aop
   [2] jms/di-extra-bundle
   [3] aura/di
   [4] orno/di
   [5] league/di
   [6] mnapoli/php-di
   [7] zendframework/zend-di
   [8] mnapoli/php-di-zf1
   [9] ocramius/ocra-di-compiler
  [10] lcobucci/di-builder
  [11] aimfeld/di-wrapper
  [12] kdyby/autowired
  [13] seiffert/console-extra-bundle
  [14] vojtech-dobes/extensions-list
Enter package # to add, or the complete package name if it is not listed []: 0
Enter the version constraint to require []: *
Search for a package []:
Would you like to define your dev dependencies (require-dev) interactively [yes]?
Search for a package []:
{
    "name": "akihito/ray.tutorial",
    "require": {
        "ray/di": "*"
    },
    "authors": [
        {
            "name": "Akihito Koriyama",
            "email": "akihito.koriyama@gmail.com"
        }
    ]
}
Do you confirm generation [yes]?
</akihito></name></vendor>
{% endhighlight %}

入力の必要な質問はこれだけでした。

{% highlight php %}
Search for a package []: ray/di
Enter package # to add, or the complete package name if it is not listed []: 0
Enter the version constraint to require []: *
{% endhighlight %}

すると最後に表示されたcomposer.jsonが出来上がりますが、まだray/diはインストールされていません。installコマンドでインストールします。

{% highlight php %}
$ php composer.phar install
{% endhighlight %}

initコマンドで作成したcomposer.jsonに従ってRay.Diとその依存ファイルとダウンロードされ、現在の依存の状態が記録されたcomposer.lockファイル、それにautoloaderを含むcomposerのファイル群もインストールされました。

{% highlight php %}
$ tree -L 2
├── composer.json
├── composer.lock
├── composer.phar
└── vendor
    ├── aura
    ├── autoload.php
    ├── composer
    ├── doctrine
    └── ray
{% endhighlight %}

<a href="https://github.com/koriym/Ray.Tutorial/blob/develop/src/todo3-ray-di.php">Ray.Diを使ったコード</a>を入力してsrc/フォルダを作ってその下に配置します。
src/todo3-ray-di.php

{% highlight php %}
<?php
use Doctrine\Common\Annotations\AnnotationRegistry;
use Ray\Di\AbstractModule;
use Ray\Di\Injector;
use Ray\Di\Di\Inject;
use Ray\Di\Di\Named;
$loader = require dirname(__DIR__) . '/vendor/autoload.php';
AnnotationRegistry::registerLoader([$loader, 'loadClass']);
class Todo
{
    private $pdo;
    /**
     * @Inject
     */

    public function __construct(PDO $pdo)
    {
        $this->pdo = $pdo;
    }

    /**
     * @param $todo
     */
    public function add($todo)
    {
        $stmt = $this->pdo->prepare('INSERT INTO TODO (todo) VALUES (:todo)');
        $stmt->bindParam(':todo', $todo);
        $stmt->execute();
    }
}
class Module extends AbstractModule
{
    public function configure()
    {
        $pdo = new PDO('mysql:dbname=test;host=localhost');
        $this->bind('PDO')->toInstance($pdo);
    }
}
$injector = Injector::create([new Module]);
$todo = $injector->getInstance('Todo');
/** @var $todo Todo */
$todo->add('Walking in Ray');
{% endhighlight %}

これがRay.Diを使ってDIを行っているコードです。変わった部分をそれぞれ見て行きます。
<h3>オートローダー</h3>

{% highlight php %}
$loader = require dirname(__DIR__) . '/vendor/autoload.php';
AnnotationRegistry::registerLoader([$loader, 'loadClass']);
{% endhighlight %}

composerを使うと依存ファイルのオートローディングの設定が含まれた、vendor/autoload.phpというオートローダーのファイルが自動で生成されます。
Ray.Diのアノテーションは<a href="http://docs.doctrine-project.org/projects/doctrine-common/en/latest/reference/annotations.html">Doctrineのアノテーション</a>を使っています。アノテーションの読み込みにはオートローダーの登録が必要で、いくつかの方法がありますがここではcomposerのオートローダーをそのまま使っています。
<h3>アノテーション</h3>

{% highlight php %}
    /**
     * @Inject
     */
    public function __construct(PDO $pdo)
    {
{% endhighlight %}

依存を受け取るメソッドには<strong>@Inject</strong>とアノテート（注釈）されています。Ray.Diはこのアノテーションを目印にして依存が必要なメソッドを割り出します。<sup><a href="#footnote_1_2143" id="identifier_1_2143" class="footnote-link footnote-identifier-link" title="コンストラクタ以外でも依存を受け取る事ができます。@Injectとアノテートしてメソッド名は何でもかまいません">2</a></sup>
アノテーションはクラスで、名前解決のためuse文が必要です。<sup><a href="#footnote_2_2143" id="identifier_2_2143" class="footnote-link footnote-identifier-link" title="Doctrineアノテーションの仕様です">3</a></sup>

{% highlight php %}
use Ray\Di\Di\Inject;
{% endhighlight %}

<h3>モジュール</h3>
モジュールでは依存を必要とする場所に依存をどう渡すかを記述します。

{% highlight php %}
class Module extends AbstractModule
{
    public function configure()
    {
        $pdo = new PDO('mysql:dbname=test;host=localhost');
        $this->bind('PDO')->toInstance($pdo);
    }
}
{% endhighlight %}

AbstractModuleを継承したクラスのconfigure()というメソッド内で、bind()メソッドを使って依存を束縛（バインド＝結びつけます）します。ここではPDOクラスを必要とするインジェクションポイントに作成した$pdoインスタンスを束縛しています。
これによって<strong>アノテーション</strong>の節で説明したように@injectとアノテートされPDOクラスのタイプヒントを持つ引き数には$pdoインスタンスが渡されるようになります。
<h3>インジェクター</h3>
モジュールを使って作成した<strong>インジェクターは、どの依存が求められれば何を渡せばいいかを知っています</strong>。そのインジェクターを使って&#8217;Todo&#8217;クラスを取得するとインジェクターは必要とされる依存をモジュールで決めたルールで渡し、<strong>依存解決</strong>(dependency resolution)が行われます。

{% highlight php %}
$injector = Injector::create([new Module]);
$todo = $injector->getInstance('Todo');
{% endhighlight %}

ついに出来ました！！！！
$todoオブジェクト！！！
依存の問題を解決（外部の変数を外側から渡す）を自動化するために、様々な事が必要になりました。
依存が必要な箇所にアノテーションが必要です。そのアノテーションクラスのオートローディング登録も必要で、モジュールでも依存の束縛の記述、束縛を使ったインジェクターの作成をしてようやく依存解決をするインジェクターが作成されました。
１つの問題を解決するためにこれだけの事をしたのです。<sup><a href="#footnote_3_2143" id="identifier_3_2143" class="footnote-link footnote-identifier-link" title="NateさんのLithiumのスライド A Framework for People who Hate Frameworks &ndash; Lithium もご覧下さい">4</a></sup>DIフレームワークはRayだけではありません。他のDIフレームワークも同じような、あるいはこれ以上の準備の手順の複雑さを持っています。
<h3>オーバーエンジニアリング?</h3>
オーバーエンジニアリング（作り込みのし過ぎ、過剰技術）でしょうか？
まず、他の技術同様に、<strong>説明のための単純な例で実利を感じる事は往々にして難しい</strong>事は頭に入れておく必要があります。例えば、HelloWorldのサンプルでフレームワークのメリットを実感する事はなかなか難しいでしょう。
DIフレームワークの使用がオーバーエンジアリングか、クラス名のハードコーディングがアンダーエンジニアリングなのか、その辺りの判断を直感で出すのはひとまず置いといて、Ray DIフレームワークの使い方の実例をもう少し見て行きましょう。
&#8230;続く
<ol class="footnotes"><li id="footnote_0_2143" class="footnote">あるいはSELECTをするメソッドを追加してください！ [<a href="#identifier_0_2143" class="footnote-link footnote-back-link">&#8617;</a>]</li><li id="footnote_1_2143" class="footnote">コンストラクタ以外でも依存を受け取る事ができます。@Injectとアノテートしてメソッド名は何でもかまいません [<a href="#identifier_1_2143" class="footnote-link footnote-back-link">&#8617;</a>]</li><li id="footnote_2_2143" class="footnote">Doctrineアノテーションの仕様です [<a href="#identifier_2_2143" class="footnote-link footnote-back-link">&#8617;</a>]</li><li id="footnote_3_2143" class="footnote">NateさんのLithiumのスライド <a href="http://www.slideshare.net/jperras/tekx-a-framework-for-people-who-hate-frameworks-lithium">A Framework for People who Hate Frameworks &#8211; Lithium</a> もご覧下さい [<a href="#identifier_3_2143" class="footnote-link footnote-back-link">&#8617;</a>]</li></ol>
