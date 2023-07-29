---
title: 'PHP: Dis Is It.'
author: admin
layout: post
permalink: /2011/12/php-dis-is-it/
categories:
  - PHP
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2011/12/php-dis-is-it/">Tweet</a>
</div>

http://www.slideshare.net/slideshow/embed_code/10628706

昨日2011.12.17にPHP Apocalypse というイベントで”PHP: Dis Is It”と題した発表をしました。PHP DisをそのDisそのものよりDisのありようやDis周辺つまり”Meta Dis”視点で考察を行い、PHPという言語のネイチャーを探ろうとしました。


<iframe src="http://www.slideshare.net/slideshow/embed_code/10628706" width="510" height="420" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC;border-width:1px 1px 0;margin-bottom:5px" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="https://www.slideshare.net/akihito.koriyama/php-dis-is-it-10628706" title="PHP: Dis Is It" target="_blank">PHP: Dis Is It</a> </strong> from <strong><a href="http://www.slideshare.net/akihito.koriyama" target="_blank">Akihito Koriyama</a></strong> </div>

## PHPに未来を感じる方いますか？

「PHPに未来を感じる方いますか？」こういう質問をされた発表者の方がいました。僕は勢いよく…と言わないまでも挙手したのですが、周囲を見渡してみると自分一人のようでした。その方の発表では「恐らくPHPは緩やかな下降線を辿っていくと、現在ターミナル医療のような状態だ」というような趣旨の発言で、TL見る限りはそれに同意する方も少なくないようでした。<sup><a href="#footnote_0_1084" id="identifier_0_1084" class="footnote-link footnote-identifier-link" title="つまり自分は少数派だったのですが、同時にだからこそ発表の価値もあるのではと思いました。">1</a></sup>　

## Inconsistency

inconsistency（一貫性の欠如）という誰もが認めるPHPの欠点をキーワードにその原因を”混血”にあるという仮説を立てます。そしてそのInconsistencyを「PHP Disそのもの」にも適用し、本当に言語として駄目設計なら、そのPHP批判にもConsistencyがあるはずなのでは？<sup><a href="#footnote_1_1084" id="identifier_1_1084" class="footnote-link footnote-identifier-link" title=" 名前空間やクロージャなど誰もが指摘してた欠陥が少なくなってきた現在のPHPなら尚更です ">2</a></sup> そこにConsistencyが無ければどういう理由なのだろうと考察します。

## X Sucks

ソフトウエアバッシングというのはPHPだけではありません。時代を通じでずっと存在し、あらゆる言語にあり、OSにあり、UNIXにもあります。  
UNIXはいわば”Mother of Software” 過去から今日にいたってあらゆるソフトウエアのファンデーションなのではないか？それを「時代遅れの異臭のするOS」という批判、歴史的で最大の、しかもUNIX制作者によるUNIX批判を紹介しました。そしてその批判の挫折、失敗の考察を紹介し、ではPHPはどうなのかと繋げます。

## 最良の者が生き残るのではない

> 最も強いものが生き残るのではなく、最も賢いものが生き延びるわけでもない。  
> 唯一、生き残るのは変化できるものだけである。 

ダーウィンの名著「種の起源」での一節です。PHPは強いアイデンティティを持ちません。<sup><a href="#footnote_2_1084" id="identifier_2_1084" class="footnote-link footnote-identifier-link" title="カリスマによる強いグランドデザインがありません">3</a></sup> 過去現在に渡って躊躇なく変化してきました。そしてその変化の度に少なからず批判を受けてきたように思います。<sup><a href="#footnote_3_1084" id="identifier_3_1084" class="footnote-link footnote-identifier-link" title="「だから、PHPにXXXなんか要らないんだって」XXXに色々な言葉がはいってきたように思えます。 ">4</a></sup>　

しかし、その変化し続けるという姿勢が、その変化そのものより重要なのであり、<sup><a href="#footnote_4_1084" id="identifier_4_1084" class="footnote-link footnote-identifier-link" title="マクルーハンの「メディアはメッセージである」とおなじように ">5</a></sup> 自分はそこに未来を見る。これが僕の「 This Is It」です。このように「Dis Is It」で始まったプレゼンテーションを「This Is It」で結びました。

## Outlook is Gold.

伝えたかった事はもう一つあります。

> 物事には常に多面性があって自分たちが見ているのは、常にコインのどちらか片方でしかない。 

「物事を深く広く知れば知るほどこの単純な真実に敬意を示し、反対の立場や考えの異なる人に対する物言いに慎重さや思慮深さが出てくる」．．．ところが現実はそれとは全く反対の方は少なくありません。

「環境に適用しようと変化し続けるものには未来がある可能性が高い」コンピュータ言語の生存をダーウィニズムになぞらえたこの自分の主張も、その多面性のある対象の一考察でしかありません。

高名な専門家が驚くほど一面的で一方的な持論を展開することがあります。その時でも「これも何かその理由があるんだろう」と常にその背景考察を問いかけ続け、自らに対しても「コインの片側だけで語ってないか？」と疑問を持ち続ける事ができる限り、自分の”自己規律ランプ”はグリーンに点灯できてるんじゃないかと思います。エンジニアとして、問題解決の多面性に対峙する仕事人として、もう少し長く生存できるんじゃないかと考えてます。

「アウトルックにこそ価値がある」…これがもう一つ伝えたかった事です。

<sup><a href="#footnote_5_1084" id="identifier_5_1084" class="footnote-link footnote-identifier-link" title="物事の視点、見解">6</a></sup><sup><a href="#footnote_6_1084" id="identifier_6_1084" class="footnote-link footnote-identifier-link" title="http://lists.sugarlabs.org/archive/iaep/2009-July/006940.html">7</a></sup>

自分でタイトルを決めてpublicな場で発表するのはこれが初めてでした。聞いて下さった方、主催関係者の方々、みなさんありがとうございました。

<ol class="footnotes">
  <li id="footnote_0_1084" class="footnote">
    つまり自分は少数派だったのですが、同時にだからこそ発表の価値もあるのではと思いました。 [<a href="#identifier_0_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_1_1084" class="footnote">
    名前空間やクロージャなど誰もが指摘してた欠陥が少なくなってきた現在のPHPなら尚更です [<a href="#identifier_1_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_2_1084" class="footnote">
    カリスマによる強いグランドデザインがありません [<a href="#identifier_2_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_3_1084" class="footnote">
    「だから、PHPにXXXなんか要らないんだって」XXXに色々な言葉がはいってきたように思えます。 [<a href="#identifier_3_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_4_1084" class="footnote">
    マクルーハンの「メディアはメッセージである」とおなじように [<a href="#identifier_4_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_5_1084" class="footnote">
    物事の視点、見解 [<a href="#identifier_5_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
  <li id="footnote_6_1084" class="footnote">
    http://lists.sugarlabs.org/archive/iaep/2009-July/006940.html [<a href="#identifier_6_1084" class="footnote-link footnote-back-link">↩</a>]
  </li>
</ol>