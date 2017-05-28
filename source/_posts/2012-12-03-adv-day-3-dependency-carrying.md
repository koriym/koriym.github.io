---
title: 'Advent Day 3:  Dependency Carrying'
author: admin
layout: post
permalink: /adv3
custom_permalink:
  - adv3
categories:
  - Advent2012
  - BEAR
tags:
  - dependency
  - DI
  - object carrig
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="http://www.bear-project.net/blog/adv3">Tweet</a>
</div>

## Dependency Carrying

DI以前に不便や疑問を感じてたところで、DI以降すっきり解決できたものにタイトルの**Dependency Carrying**の問題があります。この**Dependency Carrying**に問題はコードを読みにくくしメンテナンス性も低下させテストも難しくする厄介な問題ですが、開発規模が大きくなってくるとこの問題が出やすい様です。

では&#8221;Dependency Carrying&#8221;の問題とは何のことでしょうか？

*Aというメソッドが他のメソッド(B)を呼ぶ場合に、その呼ばれたメソッド(B)が必要の無いオブジェクト（α）を渡さなければならない場合があります。その呼ばれたメソッド(B)がメソッド(C)を呼ぶのに必要だからです。つまりCはαに依存しています。メソッドBはαが必要にないのにも関わらず、オブジェクトをcarryするためだけに引数で受け取り、Cに渡します。*

## ソリューションとしてのDI

使用しないオブジェクトを&#8221;carry&#8221;するということは双方のメソッドシグネチャーに影響を及ぼします。変更箇所を増やし結合を強め、保守性を低下させます。Aで生まれたオブジェクトαをDが必要としてそのコールフローが`A->B->C->D`というときB,Cは自ら不要なオブジェクトをcarryします。そして改修によりDがそのオブジェクトを必要としなくなったら？無意味と分かりつつ互換性のためにB,Cのコードを改修する事は難しいでしょう。使われないαは意味も無くcarryされ続けるかもしれません。

この問題の解決にしばしばstaticなシングルトンが使われてきました。しかしもちろん、staticなシングルトンはもちろんそれ自体が他の多くの問題を含みます。

DIは多くの場合この問題を解決します。控えめに言っても大幅に低減します。単に解決するだけでなくもっと洗練された方法で必要なオブジェクトをデリバリーすることができます。