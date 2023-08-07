---
title: 'Advent Day 10: DI > SL ?'
author: admin
layout: post
permalink: /adv10
custom_permalink:
  - adv10
categories: blog
tags:
  - Advent2012
  - BEAR
  - PHP
  - CodeAsDocumentation
  - Service Locater
---

## 依存するから？

また違った面からDIを考察してみます。SLとの比較です。  
SLをアンチパターンとするときに良く言われるのが、「コンテナに依存するから」と説明されますがこれを考えてみます。

依存した事自体が問題なのでしょうか？（0 dependencyでなくなった)  
それとも依存しているコンテナの性質に依存している問題なのでしょうか？

## new, SL and DI

{% highlight php %}
class Conventional
{
    public function __construct()
    {
        $this->foo = new Foo;
    }
}
{% endhighlight %}
{% highlight php %}
class Di
{
    public function __construct(FooInterfeace $foo)
    {
        $this->foo = $foo;
    }
}
{% endhighlight %}
{% highlight php %}
class Sl
{
    public function __construct(Container $container)
    {
        $this->foo = $container->get('foo');
    }
}
{% endhighlight %}

オブジェクトを取得する３つのコードです。

Conventionalクラスではnewで生成して取得しています。依存はハードコードされていて$fooはFooのインスタンスです。

Diではインターフェイスを通じて依存を受け取っています。DIP原則に従って抽象に依存しています。$fooはFooInterfaceを実装した何かのクラスです。

Slでは&#8221;foo&#8221;というサービスオブジェクトを取得しています。ロケーターはこのクラスに記述してあった依存を隠してしまいました。このfooサービスはどういうもので何でしょうか？どうすれば分かるでしょうか？これを知るAPIはありません。コンテナ機構を理解して、コンテナにセットしているコードを見て、あるいは設定ファイルから？ドキュメントから？

これをうまく説明するには前の２つの方法に比べて、何かの規約や機構が必要です。人が容易に理解できるようになってもコードはこれを検出しません。IDE等でコード補完を受けるためには注釈が必要でしょう。

またこのクラスはコンテナに依存するようになったことで、独立したパッケージとしてリリースするには何らかの依存解決ツール(composer)を前提にする必要があります。

## more info

このエントリーはこのビデオを参考に起こしています。

[Decoupled Library Packages for PHP 5.4 &#8211; Paul Jones][1]

また大抵のデザインパターン・パラダイムは、XX is evil, XX is anti paternで検索すると色々でてきます<sup><a href="#footnote_0_1446" id="identifier_0_1446" class="footnote-link footnote-identifier-link" title="^^;">1</a></sup>

[Service Locator is an Anti-Pattern][2]

## CodeAsDocumentation

この記事はSLが駄目という記事ではなくて、SLは「コンテナに依存するから駄目なのだ」という意見のどの部分が駄目なのかを考察することで、依存性を自己記述的な[CodeAsDocumentation][3]として表すDIの性質を明らかにしようとしました。

<ol class="footnotes">
  <li id="footnote_0_1446" class="footnote">
    ^^; [<a href="#identifier_0_1446" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://youtu.be/KHKC470Gkic?t=11m22s
 [2]: http://blog.ploeh.dk/2010/02/03/ServiceLocatorIsAnAntiPattern.aspx
 [3]: /blog/2012/10/codeasdocumentation/
