---
title: DIP Dependency Inversion Principle
author: admin
layout: post
permalink: /2012/05/dip-dependency-inversion-principle/
custom_permalink:
  - 2012/05/dip-dependency-inversion-principle/
categories:
  - BEAR
  - PHP
tags:
  - DIP
  - OOD
  - Robert C. Martin
  - 抽象に依存せよ
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2012/05/dip%EF%BC%9Adependency-inversion-principle/">Tweet</a>
</div>

Robert C. Martin氏による有名なオブジェクト指向設計（OOD）の原則**Principles of OOD**があります。そのうちDIP:依存逆転原則はクラス同士の結合度をいかに減らすか、またはどちらに依存すべきかを判定する原則です。

> *   上位モジュールは下位モジュールに依存してはならない。両者は抽象に依存すべきである。
> *   抽象は詳細（抽象の実装クラス）に依存してはならない。詳細は抽象に依存すべきである。

*   オリジナル Robert C. Martin氏の[Dependency Inversion Principle (DIP) (PDF)][1]
*   [山口大輔][2]さんによる[和訳版(PDF)][3]
*   [ [オブジェクト指向設計原則]依存関係逆転の原則（DIP）: Strategic Choice: ][4]
*   [Dependency Inversion Principle (DIP) – OO設計のDIP依存逆転原則: オブジェクト指向設計][5]

以下はオリジナルの論文の結びの言葉です。

> 依存性逆転の原則は、**オブジェクト指向技術で得られると言われる数多くの利益のうち、もっとも根幹にあたる部分に位置付けられます。**この原則を適切に適用することは、再利 用可能なフレームワークを構築する上では不可欠なのです。また、変更に際して回復の早い(弾力的な)コードを書く上では決定的な重要性を持ちます。抽象とこまごまとしたものはそれぞれが分離さるので、コードはとてもメンテナンスしやすいものになります。

BEAR.SundayではこのDIPを最大限に重要と考え、コード全域に渡って適用しています。<sup><a href="#footnote_0_1528" id="identifier_0_1528" class="footnote-link footnote-identifier-link" title="実際にフレームワーク開発の前に着手したのがこの実現のためのDI systemのRay.Diです">1</a></sup>

## 実装ではなく抽象に依存する

解説してる文章で最も短く簡潔なのが[Strategic Choice][4]ブログの説明です。

> DIPを一言で説明すると「抽象に依存せよ」という経験則。  
> プログラムは具体的なクラスに依存してはいけない。  
> プログラム内の関係はすべて、抽象クラスかインターフェースで終結すべきである 

BEAR.Sundayのクラスでは、以下のように依存は原則全て注入してもらう事を期待します。

<div class="codecolorer-container php blackboard" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    &nbsp; &nbsp; <span class="co4">/** &nbsp; &nbsp; &nbsp;* @Inject &nbsp; &nbsp; &nbsp;*/</span> &nbsp; &nbsp; <span class="kw2">public</span> <span class="kw2">function</span> setPrinter<span class="br0">(</span>OutputDevice <span class="re0">$printer</span><span class="br0">)</span> &nbsp; &nbsp; <span class="br0">{</span> &nbsp; &nbsp; &nbsp; &nbsp; <span class="re0">$this</span><span class="sy0">-></span><span class="me1">printer</span> <span class="sy0">=</span> <span class="re0">$printer</span><span class="sy0">;</span> &nbsp; &nbsp; <span class="br0">}</span>
  </div>
</div>

ここで大事なのはタイプヒンティング（この場合OutputDevice）を**抽象**、つまりインターフェイスかまたは抽象クラスにして具象クラスを[タイプヒント][6]にしないことです。タイプヒンティングを**エラーチェックのためのアサーションとだけ捉えず、DIPに従った設計のための実装**として考えます。

*   [Object-oriented code : Drupal][7]

## アンチパターン考察

### 直接生成する

{% codeblock lang:php %}
$service = new MyService;
{% endcodeblock %}
$serviceがMyServiceのインスタンスであると[静的束縛][8]されています。

### スタティックコール

{% codeblock lang:php %}
MyService::doSomething();
{% endcodeblock %}
これも実装に依存しています。

### コンテナに依存する

<div class="codecolorer-container php blackboard" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="re0">$id</span> <span class="sy0">=</span> <span class="re0">$this</span><span class="sy0">-></span><span class="me1">container</span><span class="br0">(</span><span class="st_h">&#8216;serviceA&#8217;</span><span class="br0">)</span><span class="sy0">-></span><span class="me1">getB</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">-></span><span class="me1">getC</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">-></span><span class="me1">getId</span><span class="br0">(</span><span class="br0">)</span><span class="sy0">;</span>
  </div>
</div>

*   このコードでは抽象への依存はなくコンテナに入った**実装に依存**しています(DIP違反）
*   メソッドチェーンで繋がれた**依存の依存が持っている知識を前提**としていています([デメテルの法則違反][9]）
*   getterを使う事でidという**オブジェクトの構成要素の暴露**がされています。([カプセル化][10])

実装に依存してオブジェクトを探しまわるようなコードから、インターフェイスを通じて依存を受け取るコードに変更します。

## Conclusion

Robert C. Martin氏が「オブジェクト指向技術で得られると言われる数多くの利益のうちもっとも根幹にあたる部分」というこのDIPですが、「抽象のタイプヒントを指定しましょう」というのはただの一例で、「抽象に依存せよ」というの適用範囲の大変広いソフトウエア品質に寄与する大原則だと思います。メソッドのアクセス権 (visibility) と同じく実装での機能というより**設計指向の実装**として、設計者をよりよい設計にガイドする制約だと考えます。

 [1]: http://www.objectmentor.com/resources/articles/dip.pdf
 [2]: http://www2.ocn.ne.jp/~yamagu/
 [3]: http://www2.ocn.ne.jp/~yamagu/object/DIP-J.pdf
 [4]: http://d.hatena.ne.jp/asakichy/20090128/1233144989
 [5]: http://www.syboos.jp/sysdesign/doc/20080609145612887.html
 [6]: http://php.net/manual/ja/language.oop5.typehinting.php
 [7]: http://drupal.org/node/608152
 [8]: http://ja.wikipedia.org/wiki/%E6%9D%9F%E7%B8%9B_%28%E6%83%85%E5%A0%B1%E5%B7%A5%E5%AD%A6%29
 [9]: http://ja.wikipedia.org/wiki/%E3%83%87%E3%83%A1%E3%83%86%E3%83%AB%E3%81%AE%E6%B3%95%E5%89%87 "LoD"
 [10]: http://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%97%E3%82%BB%E3%83%AB%E5%8C%96