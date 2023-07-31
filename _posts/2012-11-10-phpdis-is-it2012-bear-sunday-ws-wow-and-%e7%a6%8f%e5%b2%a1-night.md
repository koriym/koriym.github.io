---
title: PHP:Dis Is It(2012), BEAR.Sunday WS, and WOW !
author: admin
layout: post
permalink: /phpmatsuri2012
custom_permalink:
  - phpmatsuri2012
categories: articles
tags:
  - BEAR
  - PHP
  - フレームワーク
  - BEAR.Sunday
  - phpmatsuri
  - travel
  - 九州
---

# PHP:Dis Is It(2012)

[ PHPMatsuri 2012][1]という国内最大のPHPハッカソンイベントで「PHP:Dis Is It(2012)」というタイトルで講演を行いました。

これは丁度一年前にPHP Apocalypseというイベントで行った[PHP:Dis Is It][2]とほぼ同じものですが、言い足りなかった事を加えたり伝え方をより工夫したりしたものです。最初の講演でice breakerになればいいと思ってPVも加えました。

一年前の資料を再度目を通しスピーチを練り直す作業は、去年の自分の考えが変化したりないかを問い直す事でもあります。PVでも紹介したように現在のPHPは変化の延長にあり、やはりこの視点でのPHP評価というものにはより確信が持てました。PVはその確信をうまく反映できて、なかなか楽しいものになったと思うのですがいかがだったでしょうか。

  
※ これは&#8221;This is it&#8221;です！

## 質疑応答 ?

前回と違うのは講演後に質疑応答があった事です。この内容で質問！？って感じだったのですが、何故か次々に手が挙がりました（？）しかも質問は講演と直接関係ないことばかりで（！？）「自己紹介」にも質問がありました。（！？！？）

「アセンブラ、Cと来て何故PHPか？」と聞かれ、「言語よりプロダクトや働き方・関わり方にフォーカスしてる」と答えましたが、後で良く考えてみたらもっと単純でその時々に自分が一番カッコイイと思ってるのをずっと追いかけてるだけだと思います。「ゲーム制作」って今でもかっこいいモノだと信じてますが、「90年代前半のファミコン＆ゲームボーイのコンシュマー黎明期、後半の2Dから3Dへのパラダイムチェンジの時期」は本当に特別な時期で全てが輝いていてカッコよかったです。<sup><a href="#footnote_0_1109" id="identifier_0_1109" class="footnote-link footnote-identifier-link" title="それとも自分が年齢を重ねたせいでしょうか">1</a></sup> 

また自分が16か17の時に始めて知ったテッド・ネルソンのハイパーテキストの概念<sup><a href="#footnote_1_1109" id="identifier_1_1109" class="footnote-link footnote-identifier-link" title="当時さっぱり実装を想像できませんでした">2</a></sup> がWWWという実装になって世界を繋ぐのを20世紀後半にリアルタイムで見た時、やっぱりカッコイイと思いました。アラン・ケイのダイナブックのような夢が実現するとは思いませんでした。<!--more-->

## PHP5.4は?

PHP5.4はどうか？という質問もありました。これは会場の多くの方が興味があることだろうし、良いところをズバっと言いたかったのですが例えばtraitのような横断的処理はBEAR.SundayではAOPのインターセプターが動的に行い静的束縛のtraitはあまり出番がありません。人目を奪う目玉機能よりbuilt-in web serverやshort array syntaxなどの機能がジワジワ良く感じられたと、あまり明快な答えにならなかったのですが主題と合わせて「漸進的な進化をしてる」ということでどうでしょうか。

# BEAR.Sunday Workshop #1

人気セッション「闇」と同時で迷った方もいたと思うのですが１０人以上の方に参加してもらいました。ワークショップが始めてなので事前にオンラインで練習をして　<sup><a href="#footnote_2_1109" id="identifier_2_1109" class="footnote-link footnote-identifier-link" title="久保さん、後藤さん、関山さん、岩崎さんありがとうございました">3</a></sup> 、アレンジして行いました。インストールからするとサポートが一人では大変なので事前インストールの無い方もバーチャルホストを使ってオンラインだけでコードを編集・保存してもらうことにしました。これも始めての試みでした。

２時間のところを延長して３時間やったのですが、ところがこの心配したワークショップは大変好評なものになりました。

<blockquote class="twitter-tweet" width="550" lang="ja">

    BEARのワークショップしゅーりょー！また一歩設計への理解に進んだ！知らないキーワードが多いのでついて行くのが大変だけど、 @<a href="https://twitter.com/koriym">koriym</a> さんの話はおもしろいなー <a href="https://twitter.com/search/%23phpmatsuri">#phpmatsuri</a>

  

    &mdash; たかはしさん (@takahashiyuya) <a href="https://twitter.com/takahashiyuya/status/264763359273029632" data-datetime="2012-11-03T16:17:58+00:00">11月 3, 2012</a>

</blockquote>



<blockquote class="twitter-tweet" width="550" lang="ja">

    BEAR.Sundayのワークショップ、これを聴けただけで福岡に来た価値がありました。3時間超の丁寧な解説ありがとうございました！ <a href="https://twitter.com/search/%23phpmatsuri">#phpmatsuri</a>

  

    &mdash; Kei Sawadaさん (@remore) <a href="https://twitter.com/remore/status/264761660026257408" data-datetime="2012-11-03T16:11:13+00:00">11月 3, 2012</a>

</blockquote>



<blockquote class="twitter-tweet" width="550" lang="ja">

    <a href="https://twitter.com/search/%23phpmatsuri">#phpmatsuri</a> BEAR.Sunday やばい。アプリケーションの究極の形に近いんじゃないかと思う。

  

    &mdash; 焼きたて微糖さん (@bito_coffee) <a href="https://twitter.com/bito_coffee/status/264777166225551361" data-datetime="2012-11-03T17:12:50+00:00">11月 3, 2012</a>

</blockquote>



<blockquote class="twitter-tweet" width="550" lang="ja">

    やばい…。美しい…。BEAR.Sunday <a href="https://twitter.com/search/%23phpmatsuri">#phpmatsuri</a>

  

    &mdash; 品川 ノリコさん (@lllnorikolll) <a href="https://twitter.com/lllnorikolll/status/264719680659390465" data-datetime="2012-11-03T13:24:24+00:00">11月 3, 2012</a>

</blockquote>



３時間終わったあとも質問が次々に出てなかなか終わりません。BEAR.Sundayの話はこれまでも何回もしてるのですが、技術説明や背景説明の話が中心のものばかりでコードを触りながら背景を説明するというこれまでにないアプローチでの説明でした。

なかなかうまくいったのではないかと思います！<sup><a href="#footnote_3_1109" id="identifier_3_1109" class="footnote-link footnote-identifier-link" title="心配や疲れはこれらのtweetみて吹き飛びました">4</a></sup>

# Wow !

「アナタがBEAR.Sundayのヒトですか？」そうやって流暢な日本語で声をかけてくれたのは、イギリスさんから来たリチャードさんです。リチャードさんはいわば「フレームワーク・ソムリエ」のような人で多くの言語、多くのFWの見識があります。どうやって見つけたのかは伺いませんでしたがBEAR.Sundayもご存知でした＾＾

「この場合はどうするの？」「この心配はないの？」沢山の的確な質問が流暢な日本語で飛んで来て、それに答えて行くやり取りというのはこちらにとっても大変ありがたい事です。整理され、課題も生まれます。

<blockquote class="twitter-tweet" width="550" lang="ja">

    Completely impressed by @<a href="https://twitter.com/koriym">koriym</a> &#8216;s BEAR.sunday framework. This is after I thought I would no longer be distracted by frameworks. <a href="https://twitter.com/search/%23phpmatsuri">#phpmatsuri</a>

  

    &mdash; Richard McIntyreさん (@mackstar) <a href="https://twitter.com/mackstar/status/264907669465350145" data-datetime="2012-11-04T01:51:24+00:00">11月 4, 2012</a>

</blockquote>



「オモシロイネ、ミタコトナイネ」何回か繰り返した後、「僕は今日課題を決めてないからBEAR.Sundayの英語翻訳をやろうか？」と言ってくれました。  
「お願いします！！！！！！！」

「イギリスでもね、頑張って宣伝するからね」  
「ほんとですか！ではアドボケイトお願いします！」

この「Wow !」を忘れる事はないでしょう＾＾今もとてもワクワクしてます。これはリチャードさんもきっとそうだと思います！

# 福岡 night

ハッカソンイベントなのですが、全エネルギーをセッションとワークショップに使い果たして<sup><a href="#footnote_4_1109" id="identifier_4_1109" class="footnote-link footnote-identifier-link" title="いつもそうなんですが、何故かセッション終わった後にフラフラになります">5</a></sup>ちょっとチェックインのつもりで戻ったホテルから戻れず、密かにやろうと思ってた深夜のファミコン大会でのファミコンプログラミング技術解説などはもちろん、事前準備してきたプログラムにほとんど手をつけれずLTも参加できませんでした。（残念！）

セッションはきちんと見れたのはアンドロイドだけだったのですが、これは面白かった！！Androidヒストリーとbuyoutヒストリーに、「何が大切か」という視点をユーモアを交えて織り込んである、非常に充実したセッションでした。LTでは途中で終わって残念でしたけど組み込みPHPにおお！！っとなりました。

後夜祭はスタッフ、参加者、外国人ゲストの人達たちと福岡の夜に繰り出して美味しいもの沢山食べて沢山色々な話をしました。

これが大変充実してました！！！ <sup><a href="#footnote_5_1109" id="identifier_5_1109" class="footnote-link footnote-identifier-link" title="その日に返るのはかなりもったないです！">6</a></sup> 参加者やスタッフの人達と話すのはもちろんですが、外国のPHPディベロッパーの方達と話すのは始めてです。 Symfonyアドボケイトのダスティンさんはなかなかの冒険家でアメリカ人なのにハバナ（キューバ）に言った事があったり（アメリカ人行けるのだ！？）、飛行機のライセンスを持ってその取得プロセスを教えてくれたりと、PHP以外の話でも盛り上がりました。アメリカとヨーロッパにおけるPHP FW事情とか、やはり海外のデベロッパーの交流のさかんな人から生の話を聞けるのは貴重なチャンスです。ヨーロッパではやっぱりSymfonyで、アメリカでは数年置きにトレンド変わったり分散したりで、Zend Framework, Symfony, Codeigniter, CakePHP辺りが人気FWだそうです。

ベネズエラ出身のホセさん(CakePHP)とはほんと沢山話しました。貴重な意見交換も沢山できたと思いますが話しが楽しかった！楽しすぎて後で「あーもうちょっと他の人のために通訳に回ったら良かったかも」とか思ったくらいです＾＾；　内容はまたどこかでシェアできたらと思います！<sup><a href="#footnote_6_1109" id="identifier_6_1109" class="footnote-link footnote-identifier-link" title="書けない！">7</a></sup>

# 九州良いとこ一度はおいで

九州良いとこです。今回のPHPMatsuriに前後して、佐賀のバルーンフェスティバル吉野ヶ里、長崎のグラバー邸、軍艦島、色々訪ねました。ヘリにも乗りました！ハッカソンは一泊なのに九州に５泊もしました。 沢山話して、沢山美味しいもの食べて、沢山観光して、エネルギーをフル充電して東京に帰りました。

楽しかったです。みなさんありがとうございました！

<https://www.youtube.com/embed/WL5dfOC8kfA>

---

<ol class="footnotes">
  <li id="footnote_0_1109" class="footnote">
    それとも自分が年齢を重ねたせいでしょうか [<a href="#identifier_0_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_1109" class="footnote">
    当時さっぱり実装を想像できませんでした [<a href="#identifier_1_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_2_1109" class="footnote">
    久保さん、後藤さん、関山さん、岩崎さんありがとうございました [<a href="#identifier_2_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_3_1109" class="footnote">
    心配や疲れはこれらのtweetみて吹き飛びました [<a href="#identifier_3_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_4_1109" class="footnote">
    いつもそうなんですが、何故かセッション終わった後にフラフラになります [<a href="#identifier_4_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_5_1109" class="footnote">
    その日に返るのはかなりもったないです！ [<a href="#identifier_5_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_6_1109" class="footnote">
    書けない！ [<a href="#identifier_6_1109" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://www.phpmatsuri.net/2012/
 [2]: /blog/2011/12/php-dis-is-it/
