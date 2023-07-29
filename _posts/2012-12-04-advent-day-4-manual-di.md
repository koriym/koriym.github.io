---
title: 'Advent Day 4: Manual DI'
author: admin
layout: post
permalink: /adv4
custom_permalink:
  - adv4
categories:
  - Advent2012
  - BEAR
  - PHP
tags:
  - DI
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/adv4">Tweet</a>
</div>

## DI考察ブログエントリー

[@Hiraku][1]さんが自身のブログで「PHPのDIで動的にオブジェクトを確保する考察」というDIのエントリーをポストされてます。

> *Dependency InjectionがPHPでも流行っているそうです。が、未だによくわからないので、わからないところを自分なりに考察してみます。*
> 
> [PHPのDIで動的にオブジェクトを確保する考察][2]

この記事の中で@Hirakuさんは特定のDIコンテナを使わないで、DIというパターンを考察しています。依存を手動で渡す様々な方法を試し、それらのメリット、デメリットを考察しています。

> 確かに、DBやLoggerなど、クラスの中で一つだけあれば十分なものも多く、そういうオブジェクトはコンストラクタに渡す形で「外から突っ込む」ことができるでしょう。  
> しかし、動的にオブジェクトを作るケースだって山ほどあるはずです。「必要なオブジェクトはクラスの生成時に全部できあがっている」なんて状態はそうそうあるもんじゃありません。 

<blockquote class="twitter-tweet" data-in-reply-to="275127780000284672" width="550" lang="ja">

    @<a href="https://twitter.com/hiraku">hiraku</a> DIの記事読ませて頂きました。後半のConfigの例についてですが、ランタイムにしか決まらない内容があるのであれば、それはオブジェクトのコンストラクションとは別メソッドに分けるという方向になるかと私は思います。

  

    &mdash; Hidenori Gotoさん (@hidenorigoto) <a href="https://twitter.com/hidenorigoto/status/275228755713216512" data-datetime="2012-12-02T13:23:43+00:00">12月 2, 2012</a>

</blockquote>



この記事の例で言うと基本的には@hidenorigotoさんの意見に+1です。

## ３つのInject

エントリーの中でランタイムでの動的取得に対して３つの方法をあげられています。

*   factoryをinjectする
*   クラス名をinjectする
*   プロトタイプをinjectする

> 他にいいやり方があったら教えてください。

&#8230;考えてみました。  
例えばfactory名をinjectするというのはどうでしょうか？

{% highlight php %}
function setConfig(ConfigInterface $config)
{
    $this->config = $config;
}
function __construct($configFactory = 'ConfigFactory')
{
    $this->setConfig((new $configFactory)->get());
}
{% endhighlight %}

利用コードはクラス名を渡すのと同様です  
{% highlight php %}
$c = new C('MockConfigFactory');
{% endhighlight %}

型の保証が必要でないならsetterメソッドを除去して直接代入します。

また、以下のようなインスタンス生成スクリプトで渡す方法も考えてみました。<sup><a href="#footnote_0_1288" id="identifier_0_1288" class="footnote-link footnote-identifier-link" title="factoryの方が良さそうですね&hellip;">1</a></sup>  
{% highlight php %}
function __construct($configFactoryScript = 'ConfigFactoryScript') {
    $this->config = include INSTANCE_DIR . $configFactoryScript;
}
{% endhighlight %}

これらの方法は懸念された問題のうちいくつかを解決しています。factoryによる自由な生成と呼び出しコードの簡素化、必要個数のランタイムでの取得も可能になります。

## 手動DIの限界

しかしやはり、考察を続けると色々な懸念が出て来ます。これらのインジェクトをシングルトン、あるいは限定個数で行う場合は？また依存にもまた依存が必要です。それらの依存もアプリケーションコンテキストで変化していきます。

以下はBEAR.Sundayのリソースクライアントの生成コードです。

{% highlight php %}
$config = new Config(new Annotation(new Definition, new Reader));
$injector = new Injector(new Container(new Forge($config)));
$injector->setModule(new TestModule);
$scheme = new SchemeCollection;
$scheme
->scheme('app')
->host('self')
->toAdapter(new App($injector, 'testworld', 'ResourceObject'));
$scheme
->scheme('page')
->host('self')
->toAdapter(new App($injector, 'testworld', 'Page'));
$scheme->scheme('http')->host('*')->toAdapter(new \BEAR\Resource\Adapter\Http);
$invoker = new Invoker(
    $config,
    new Linker(new Reader)
    new Manager(new HandlerFactory, new ResultFactory, new ResultCollection)
);
$resource = new Resource(
    $new Factory($scheme),
    $invoker, new Request($invoker)
);
{% endhighlight %}

こんな複雑なオブジェクトの生成をコンストラクタで毎回記述するわけにもいきません。factoryを利用することになりますが、ではこれらの生成のうち一部だけを違うものに差し替えたりするのはどうしたらいいでしょうか？実際にBEAR.SundayではDevモードの時はInvokerはDevInvokerに変わっています。ContainerもPersistentContainerに変更したいと思っています。

このインスタンスを都度生成するのではなくてシングルトンで供給したい時にはどうしたらいいでしょうか？あるいはこの生成にも依存が必要ならどうしたらいいでしょうか？その依存にもそのまた依存があるはずです。

手動によるDIを実践あるいは深く考察することはDIの理解に有意義な事ですが、ある時点からその限界が見えて来る事に気付きます。テストコードやごく小規模の開発では問題にならないでしょう。しかしこういう手続きの記述で複雑なオブジェクトを構成するのは、柔軟性や結合度、DRY原則で問題が出て来ます。しかしそれは同時にDependency Injectorにどのような役割や機能が求められるかの考察にもなるでしょう。  
**  
オブジェクトの生成・構成を手続きの記述ではなくルールに基づいたロジックで行う**ことでこれらの問題を解決します。次回Day 5はその考察を行います。

<ol class="footnotes">
  <li id="footnote_0_1288" class="footnote">
    factoryの方が良さそうですね&#8230; [<a href="#identifier_0_1288" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: https://twitter.com/Hiraku
 [2]: http://blog.tojiru.net/article/304867046.html
