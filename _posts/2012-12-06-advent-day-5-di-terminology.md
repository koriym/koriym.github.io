---
title: 'Advent Day 5: DI Terminology'
author: admin
layout: post
permalink: /adv5
custom_permalink:
  - adv5
categories:
  - Advent2012
  - BEAR
  - PHP
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/adv5">Tweet</a>
</div>

## DI用語

Day５はDay4に続かないでDIで使われる用語を色々取り上げてみます。

DI関係の記事を色々見るに言葉がよく分からない事があります。もうあまり使われてる言葉でなかったり、あるいは書かれてる時期や人で使い方やスコープが違うような感じがするときもあります。

よく分からないながらも色々掘り起こして並べてみました。

## dependency

依存。オブジェクトに限らず単純な値も含みます。

## dependent (dependent consumer)

依存を利用するクライアント

## injector / provider / container

wikiによると&#8221;an injector (sometimes referred to as a provider or container)&#8221;  
依存をdependentに用意する。&#8221;DIコンテナ&#8221;

## dependency resolution

依存の解決、依存の取得方法。

## Dependency Lookup<sup><a href="#footnote_0_1326" id="identifier_0_1326" class="footnote-link footnote-identifier-link" title="Service Locator ?">1</a></sup>

直接の訳語は「依存の参照」

[wikipedia][1]では通常の処理でDIとは逆。と簡単に説明してます

http://xunitpatterns.comというサイトではこのように説明しています。

Dependency Lookup is characterized by the following:

*   Either a Singleton[GOF], a Registry[PEAA] or some kind of Thread-Specific Storage[POSA2], 
*   with an interface that fully encapsulated which implementation we are using, 
*   with a built-in substitution mechanism for replacing the returned object  
    with a Test Double 
*   accessed via well-known global name. 

Dependency Lookupは２つに分かれるそうです。  
<http://life.neophi.com/danielr/files/InversionOfControl.pdf>

## Dependency Pull

&#8220;中央&#8221;にある依存管理コンテナなどに問い合わせて依存を取得します。  
{% highlight php %}
$foo = Service::get('foo');
{% endhighlight %}

## Contextualized Dependency Lookup (CDL)

&#8220;渡された&#8221;コンテナから取得します。  
{% highlight php %}
public function do(ConteinerInterface $container)
{
    $this->foo = $container->get('foo');
}
{% endhighlight %}

## Global Registry

Dependency Pullが用いる中央のレジストリ/コンテナ。

## Service Locator

Dependency Lookupと[同じ（？）][2]、他にObject Factory, Component Broker, Component Registry等。

## Dependency Injection (DI)

{% highlight php %}
public function do(FooInterface $foo)
{
    $this->foo = $foo;
}
{% endhighlight %}

## 感想、ちょっと一言

*   dependency lookupは広義では外部を参照するという意味で使われれ、狭義では特定のパターンを指すような感じがします
*   CDLやLookupの言い方はちょっと古くService Locator(SL)で総称されているような感じがします。
*   一般的に「DIコンテナ」が使われますがwikiにあるように「injector」が表記としてより妥当であると思いました。
*   用語解説のよい記事になかなか巡り会えませんでした
<ol class="footnotes">
  <li id="footnote_0_1326" class="footnote">
    Service Locator ? [<a href="#identifier_0_1326" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://en.wikipedia.org/wiki/Dependency_injection
 [2]: http://xunitpatterns.com/Dependency%20Lookup.html
