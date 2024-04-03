---
title: 'Advent Day 12: debug print'
author: admin
layout: post
permalink: /adv12
custom_permalink:
  - adv12
categories: ["blog"]
tags:
  - Advent2012
  - BEAR
  - PHP
  - debug
---

## デバック表示 p($var);

BEAR.Sundayは前バージョンからの伝統(?)のデバック出力**p();**があります。  
表示はxdebugのvar_dump()と同じですが加えて、変数名とp()した場所が表示されます。

{% highlight php %}
p($a);
{% endhighlight %}

**webでp()すると**  
<a href="/blog/adv12/%e3%82%b9%e3%82%af%e3%83%aa%e3%83%bc%e3%83%b3%e3%82%b7%e3%83%a7%e3%83%83%e3%83%88-2012-12-17-23-31-50/" rel="attachment wp-att-1505"><img src="/images/wp-content/uploads/2012/12/a0f4eafe84c70f36f0bb364cfbac6e9f.png" alt="スクリーンショット 2012-12-17 23.31.50" class="aligncenter size-full wp-image-1505" /></a>

**コンソールでp()すると**

<div>
</div>

<a href="/blog/adv12/%e3%82%b9%e3%82%af%e3%83%aa%e3%83%bc%e3%83%b3%e3%82%b7%e3%83%a7%e3%83%83%e3%83%88-2012-12-17-23-34-23/" rel="attachment wp-att-1507"><img src="/images/wp-content/uploads/2012/12/35ee0b02f38504093b38b4eea755e8f3.png" alt="スクリーンショット 2012-12-17 23.34.23" class="aligncenter size-full wp-image-1507" /></a> <div>
</div>

引き数なしでp()すると(void)と表示されます。

<a href="/blog/adv12/%e3%82%b9%e3%82%af%e3%83%aa%e3%83%bc%e3%83%b3%e3%82%b7%e3%83%a7%e3%83%83%e3%83%88-2012-12-18-0-26-19/" rel="attachment wp-att-1510"><img src="/images/wp-content/uploads/2012/12/a6c3d84bee8a4d686cbadd8e643d9f43.png" alt="スクリーンショット 2012-12-18 0.26.19" class="aligncenter size-full wp-image-1510" /></a>  
その場所を通ったかどうかの確認になります。

表示されたファイル名はクリックしてweb上で編集できデバック表示したp()を消したり表示内容を変更したりできます。  
<a href="/blog/adv12/%e3%82%b9%e3%82%af%e3%83%aa%e3%83%bc%e3%83%b3%e3%82%b7%e3%83%a7%e3%83%83%e3%83%88-2012-12-18-0-35-26/" rel="attachment wp-att-1522">
  </a><a href="/images/wp-content/uploads/2012/12/6cc52f92b203a81ef57231115a153620.png"><img src="/images/wp-content/uploads/2012/12/6cc52f92b203a81ef57231115a153620.png" alt="スクリーンショット 2012-12-18 0.37.16" class="aligncenter size-full wp-image-1533" /></a>


<h2>
  デバック表示
</h2>


  「変数内容を画面上で確認」IDE+Debuggerでもっと洗練された内容確認の仕方やTDDを実践したあとでも、この原始的なやり方を手放す事ができません。



  一方、デバック情報を画面に表示するときの問題「どれがどの表示を表してるのか分からなくなった」「どこでかいたのか分からない」「エディタに戻ってまた消すのが面倒」&#8230;それらの解決とコーディング作業の中断を0.1秒単位で削りたいという考えからこのp()を作りました。



  前回バージョンのSaturdayで好まれた機能です。Sundayでは表示変更とコンソール表示のサポートを追加しました。その他は以前のものと編集できたところも含めて同じです。
