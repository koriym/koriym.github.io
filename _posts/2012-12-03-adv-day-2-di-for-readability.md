---
title: 'Advent Day 2: DI for Readability'
author: admin
layout: post
permalink: /adv2
custom_permalink:
  - adv2
categories: ["blog"]
tags:
  - Advent2012
  - BEAR
  - PHP
  - Dependency Injection
---

## Dependency Injection Benefits

Jakob Jenkovさんのブログ記事&#8221;[Dependency Injection Benefits][1]&#8220;でDIのメリットを以下の様に述べてます。

*   Reduced Dependencies
*   Reduced Dependency Carrying
*   More Reusable Code
*   More Testable Code
*   More Readable Code

DIの技術的紹介記事は数多くありますが、このうち多くはテストの容易性やモジュラティーの向上による再利用性の高さをメリットとしてあげていますが、この記事ではDIの他のメリットとして可読性の向上を紹介します。

BEAR.Sundayではインターフェイスを通してオブジェクトを受け取ります。インターフェイスとオブジェクトの生成の関係を知るインジェクターがbootstrap時に必要な依存を注入（外部から代入）します。コンストラクタで受け取るものとセッターメソッドによるインジェクションがあります。

### コンストラクタインジェクション

{% highlight php %}
    /**
     * Set resource
     *
     * @param ResourceInterface $resource
     *
     * @Inject
     */
    public function __construct(ResourceInterface $resource)
    {
        $this->resource = $resource;
    }
{% endhighlight %}

### セッターインジェクション

{% highlight php %}
    /**
     * Set resource
     *
     * @param ResourceInterface $resource
     *
     * @Inject
     */
    public function setResource(ResourceInterface $resource)
    {
        $this->resource = $resource;
    }
{% endhighlight %}

メソッドには**@Inject**がアノテートされ、外部からの注入が行われる事を示しています。これはコードを読むプログラマにとっても、注入ポイントを知る必要があるインジェクターにとっても文字通りアノテーション（注記）となっています。

外部のインスタンスを取得方法は前回記事のincludeと同じく最小化されています。取得方法のコーディングが必要ないということは、依存の取得が容易であると同時に依存提供の方法や依存内容も変更可能だということを意味します。インターフェイスを通じて受け取る方法は**「詳細ではなく抽象に依存せよ」**という[DIP原則][2]にも従っています。

また依存の利用個所はクラス前半に記述してあるコンストラクタとセッターメソッドに集約されていてそのクラスがどの依存を利用するかをすぐに知るのは容易です。依存を知るためにコードの全てを見る必要がありません。依存は外部から代入され、ランタイム（実行時のコード途中で）で&#8221;PULL&#8221;されないためです。<sup><a href="#footnote_0_1259" id="identifier_0_1259" class="footnote-link footnote-identifier-link" title="この点はSLより優れています">1</a></sup>

## トレイトによるセッターインジェクション

PHP5.4では横断的に利用するメソッドをtraitで集約できます。BEAR.SundayではこれをDIのボイラープレート削減のために使用することができます。これを利用したクラスはこのようになります。

{% highlight php %}
class A
{
    use ResourceInject;
    use LoggerInject;
    use WebContextInject;
{% endhighlight %}
一つの依存が一行で表さ集約されました。コンパクトになりクラスの依存が簡潔に表現されています。

一方、そのインターフェイスはtraitファイルを見なければならなくなりました。この点はSLと同じと割り切るか、出現頻度の高いオブジェクトに限るなどの工夫をするのが良いかも知れません。

<ol class="footnotes">
  <li id="footnote_0_1259" class="footnote">
    この点はSLより優れています [<a href="#identifier_0_1259" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://tutorials.jenkov.com/dependency-injection/dependency-injection-benefits.html
 [2]: /blog/2012/05/dip%EF%BC%9Adependency-inversion-principle/
