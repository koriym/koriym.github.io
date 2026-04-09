---
layout: post
title: "First BEAR.Sunday Stable Release !"
date: 2015-05-31 18:50
comments: true
categories: ["blog"]
tags:
---

BEAR.Sundayの初めてのstableバージョンを[リリース](https://github.com/bearsunday/BEAR.Sunday/releases/tag/1.0.0)しました。

たくさんの方々にお世話になりました。@mackstar 日本と欧米の開発事情に精通していて様々なアドバイスをいただきました。
リチャードさんのアドバイスは一貫して、「無駄を省いていく」というもので日本人以上に日本人なZenの心も持ち主です。彼のおかげでイギリスのカンファレンスで登壇することもできました。

Lithium開発者[@nateable](https://twitter.com/nateabele) さんは私が初めて会ったフレームワーク設計者です。様々な質問を伺い刺激になりました。
その後、今度は海外のカンファレンスで何度も[BEAR.Sunday](http://sssslide.com/speakerdeck.com/nateabele/designing-hypermedia-apis)や[私自身](http://sssslide.com/speakerdeck.com/nateabele/the-future)を紹介してもらって、
BEAR.Sunday開発の継続する[自信](https://twitter.com/nateabele/status/391317896615714817)にもなりました。
CakePHP3のコアディベロッパーの [@jose_zap](https://twitter.com/jose_zap)さんはCakePHP3のORMのモジュールを提供してくれました。Ray.Diを実務で使用されていて様々なフィードバックをいただいてAOP使用例も参考になりました。

初期の頃の試みとしてPHPメンターズの [@hidenorigoto](https://twitter.com/hidenorigoto)さん、 [@iteman](https://twitter.com/iteman) さん、[array syntax](https://wiki.php.net/rfc/shortsyntaxforarrays)の [@rsky](https://twitter.com/rsky)rsky さんとオンラインmeetupでその時の「これからの野望」を色々聞いて頂きました。またリアルなmeetupは[@NEKOGET](https://twitter.com/NEKOGET), [@brtriver](https://twitter.com/brtriver), [@kuma_nana](https://twitter.com/kuma_nana), [@zumkimochi](https://twitter.com/zumkimochi), [@zingooo](https://twitter.com/zingooo) さん他お世話になりました。参加してくれたみなさんもありがとうございました。
Symfonyユーザー会でも何度もお話させていただきました。(BEAR.Sundayしかなかった回がありました！)[OAuthModule](https://github.com/Ray-Di/Ray.OAuthModule)のモジュールをかいてくれた [@kawanamiyuu](https://twitter.com/kawanamiyuu) さん、[TwigModule](https://github.com/madapaja/Madapaja.TwigModule)の [@madapaja](https://twitter.com/madapaja) さん、FakeModuleの [@shingo-kumaagi](https://twitter.com/shingo-kumaagi) さん
0.x版でPHPtalの[@tanakahisateru](https://twitter.com/tanakahisateru)さん。BlogやQiitaでBEAR.Sundayの記事を書いてくれたカルテットコミュニケーションズの [@qckanemoto](https://twitter.com/qckanemoto) さんや [@77web](https://twitter.com/77web) さん
　イラストをかいてくれた [@tdakak](https://twitter.com/tdakak) さん、ベア吉ステッカーで協力してくれたshimadaさん、reikoさん
エキサイトの [@usomillp](https://twitter.com/usomillp) さん、[@gokigendori](https://twitter.com/gokigendori)さん、iwafujiさん、fukushimaさん、kobayashiさん、toshinaiさん他
BEAR.Saturdayの時からずっとBEARのファンでいてくれる [@ryo88c](https://twitter.com/ryo88c) [@zingooo](https://twitter.com/zingooo) [TOM](http://profile.hatena.ne.jp/stellaqua/) さん
大勢の方にコードやアイデアのコントリビュートをいただきました。[@iteman](https://twitter.com/iteman) にはフレームワークの拡張点として視点、
[@mugeso](https://twitter.com/mugeso)さんや[@kenji_s](https://twitter.com/kenji_s)さんにはいくつものPR、
[@hidenorigoto](https://twitter.com/hidenorigoto)さんには[雑誌で紹介](http://phpmentors.jp/post/43944158326/web-db-press)していただきました。[@kuma_nana](https://twitter.com/kuma_nana)には音声入りの画面チュートリアルをつくってもらいました。

海外の方にもお世話になりました。Guiceの使用経験もないのにRay.Diをつくっていた私に[@akkie](https://twitter.com/akkie)は色々と教えてくれました。
[@craigjbass](https://twitter.com/craigjbass)とのディスカッションはRay.Compiler誕生のきっかけになりました。[@auraphp](https://twitter.com/auraphp)には大きな影響を受けいくつもの指針を得ました。
リードの[@pmjones](https://twitter.com/pmjonesや)さんや精力的に活動されている[@harikt](https://twitter.com/harikt)さんに感謝したいと思います。

下記はGitHubでのコントリビューターリストです。meetupに参加してくれた方、ブログ記事を投稿してくれた方、フィードバックをくれた方、ベア吉の好きな人、関心をもってくれた方全ての方に感謝します。
これからもよろしくお願いします。

http://bearsunday.github.io/

[<img alt="akkie" src="https://avatars.githubusercontent.com/u/307006?v=3&s=117" width="117">](https://github.com/akkie)
[<img alt="mackstar" src="https://avatars.githubusercontent.com/u/197328?v=3&s=117" width="117">](https://github.com/mackstar)
[<img alt="zukimochi" src="https://avatars.githubusercontent.com/u/529051?v=3&s=117" width="117">](https://github.com/zukimochi)
[<img alt="yuya-takeyama" src="https://avatars.githubusercontent.com/u/241542?v=3&s=117" width="117">](https://github.com/yuya-takeyama)
[<img alt="yutakachiba" src="https://avatars.githubusercontent.com/u/5999747?v=3&s=117" width="117">](https://github.com/yutakachiba)
[<img alt="yoshikitanaka" src="https://avatars.githubusercontent.com/u/460480?v=3&s=117" width="117">](https://github.com/yoshikitanaka)

[<img alt="vlakarados" src="https://avatars.githubusercontent.com/u/386678?v=3&s=117" width="117">](https://github.com/vlakarados)
[<img alt="tomverran" src="https://avatars.githubusercontent.com/u/1388226?v=3&s=117" width="117">](https://github.com/tomverran)
[<img alt="tanakahisateru" src="https://avatars.githubusercontent.com/u/403893?v=3&s=117" width="117">](https://github.com/tanakahisateru)
[<img alt="shingo-kumagai" src="https://avatars.githubusercontent.com/u/7978290?v=3&s=117" width="117">](https://github.com/shingo-kumagai)
[<img alt="sasezaki" src="https://avatars.githubusercontent.com/u/42755?v=3&s=117" width="117">](https://github.com/sasezaki)
[<img alt="remore" src="https://avatars.githubusercontent.com/u/424277?v=3&s=117" width="117">](https://github.com/remore)

[<img alt="qckanemoto" src="https://avatars.githubusercontent.com/u/4360663?v=3&s=117" width="117">](https://github.com/qckanemoto)
[<img alt="nishigori" src="https://avatars.githubusercontent.com/u/928692?v=3&s=117" width="117">](https://github.com/nishigori)
[<img alt="nateabele" src="https://avatars.githubusercontent.com/u/18288?v=3&s=117" width="117">](https://github.com/nateabele)
[<img alt="MugeSo" src="https://avatars.githubusercontent.com/u/250446?v=3&s=117" width="117">](https://github.com/MugeSo)
[<img alt="malukenho" src="https://avatars.githubusercontent.com/u/3275172?v=3&s=117" width="117">](https://github.com/malukenho)
[<img alt="madapaja" src="https://avatars.githubusercontent.com/u/491357?v=3&s=117" width="117">](https://github.com/madapaja)

[<img alt="lorenzo" src="https://avatars.githubusercontent.com/u/37621?v=3&s=117" width="117">](https://github.com/lorenzo)
[<img alt="kyanny" src="https://avatars.githubusercontent.com/u/10515?v=3&s=117" width="117">](https://github.com/kyanny)
[<img alt="kumamidori" src="https://avatars.githubusercontent.com/u/384567?v=3&s=117" width="117">](https://github.com/kumamidori)
[<img alt="ktutumi" src="https://avatars.githubusercontent.com/u/3712782?v=3&s=117" width="117">](https://github.com/ktutumi)
[<img alt="kenjis" src="https://avatars.githubusercontent.com/u/87955?v=3&s=117" width="117">](https://github.com/kenjis)
[<img alt="kenchingh" src="https://avatars.githubusercontent.com/u/5219653?v=3&s=117" width="117">](https://github.com/kenchingh)

[<img alt="kawanamiyuu" src="https://avatars.githubusercontent.com/u/1461463?v=3&s=117" width="117">](https://github.com/kawanamiyuu)
[<img alt="kaepapa" src="https://avatars.githubusercontent.com/u/1049772?v=3&s=117" width="117">](https://github.com/kaepapa)
[<img alt="jingu" src="https://avatars.githubusercontent.com/u/892913?v=3&s=117" width="117">](https://github.com/jingu)
[<img alt="jamolkhon" src="https://avatars.githubusercontent.com/u/817941?v=3&s=117" width="117">](https://github.com/jamolkhon)
[<img alt="iteman" src="https://avatars.githubusercontent.com/u/52985?v=3&s=117" width="117">](https://github.com/iteman)
[<img alt="holyshared" src="https://avatars.githubusercontent.com/u/167190?v=3&s=117" width="117">](https://github.com/holyshared)

[<img alt="hidenorigoto" src="https://avatars.githubusercontent.com/u/89830?v=3&s=117" width="117">](https://github.com/hidenorigoto)
[<img alt="harikt" src="https://avatars.githubusercontent.com/u/120454?v=3&s=117" width="117">](https://github.com/harikt)
[<img alt="fivestar" src="https://avatars.githubusercontent.com/u/30999?v=3&s=117" width="117">](https://github.com/fivestar)
[<img alt="fiahfy" src="https://avatars.githubusercontent.com/u/7123916?v=3&s=117" width="117">](https://github.com/fiahfy)
[<img alt="desigrammer" src="https://avatars.githubusercontent.com/u/1431057?v=3&s=117" width="117">](https://github.com/desigrammer)
[<img alt="craigjbass" src="https://avatars.githubusercontent.com/u/1889973?v=3&s=117" width="117">](https://github.com/craigjbass)

[<img alt="bar" src="https://avatars.githubusercontent.com/u/88155?v=3&s=117" width="117">](https://github.com/bar)
[<img alt="atakig" src="https://avatars.githubusercontent.com/u/552033?v=3&s=117" width="117">](https://github.com/atakig)
[<img alt="77web" src="https://avatars.githubusercontent.com/u/296615?v=3&s=117" width="117">](https://github.com/77web)

## コメント

![Disqusコメントのアーカイブ](/images/2015/disqus-comments-2015-stable-release.png){:width="720px"}

<!-- Disqus comments archived 2026-04-10 -->
<!-- Source: https://koriym.github.io/blog/2015/05/31/first-bear-sunday-stable-release/ -->
<!-- Comment count: 35 -->
<!--
[ryo88c] Monday, June 1, 2015 11:11 PM (+60)
おめでとうございます！BEAR.Saturday の時と同様、ビジネスユース勢として応援してます！

  [Akihito Koriyama] Friday, December 4, 2015 1:39 PM
  ありがとうございます!

  [Akihito Koriyama] Monday, June 1, 2015 11:41 PM
  林さんの熱い応援、力になりましたよ！ありがとうございます。

[NEKOGET] Tuesday, June 2, 2015 10:29 AM (+33)
リリースおめでとうございます !!

  [Akihito Koriyama] Friday, December 4, 2015 1:40 PM
  ありがとうございますー!

  [Akihito Koriyama] Tuesday, June 2, 2015 12:43 PM
  ありがとうございます!

[hidenorigoto] Monday, June 1, 2015 10:09 PM (+32)
BEAR.Sundayにこれまでチョコチョコと関わりを持ってきた身として、とても嬉しいです！BEAR.Sundayの開発を支える温泉へご一緒したのが、つい昨日のようです。またどこかでゆっくりお話したいです！

  [Akihito Koriyama] Monday, June 1, 2015 10:16 PM (+1)
  「何してるんですか？」と何も宣伝してないリポジトリを見つけて声をかけてくれたのが後藤さんです。それから沢山のことがありました。また今度たっぷり話しましょう！

[madapaja] Monday, June 1, 2015 10:47 PM (+19)
おめでとうございます！私も微力ながら関われたことがうれしいです！これからも継続的に関われられればと思いますので宜しくお願いします！！

  [Akihito Koriyama] Monday, June 1, 2015 11:41 PM
  ありがとうございます。音楽を奏でるように新しいものを作っていきましょう！

[77web] Monday, June 1, 2015 8:42 PM (+19)
おめでとうございます！

  [Akihito Koriyama] Monday, June 1, 2015 9:46 PM
  ありがとうございます！

[Hisateru Tanaka] Monday, June 1, 2015 7:46 PM (+19)
Congrats!

  [Akihito Koriyama] Monday, June 1, 2015 9:46 PM
  Thanks !

[OGAWA Katsuhiro] Tuesday, June 2, 2015 12:49 AM (+18)
リリースおめでとうございます。コードなど参考にさせてもらう一方で、全然お力になれてないなあと反省していますが、何か貢献できるよう、今後ともよろしくお願いします。

[tadaaki] Tuesday, June 2, 2015 2:52 PM (+1)
stableリリースおめでとうございます！！

  [Akihito Koriyama] Tuesday, June 2, 2015 7:36 PM
  ありがとうございます！

[Yoshitaka Jingu] Tuesday, June 2, 2015 11:03 AM (+1)
Stableおめでとうございます！Saturdayの頃からですが、頻繁にコンセプトやアイディア、幾つもの課題と課題へのアプローチを間近で話を聞けたのはとてもラッキーだったと思います。現在も絶賛業務で利用中ですので、そこからもっとプロジェクトにフィードバックをしていけたらと思っています。これからもよろしくお願いします！

  [Akihito Koriyama] Tuesday, June 2, 2015 12:42 PM
  神宮君、ありがとう。これからもよろしくね！

[shishi] Tuesday, June 2, 2015 1:44 AM (+1)
特に貢献しないでアレでしたがおめでとうございます！

  [Akihito Koriyama] Tuesday, June 2, 2015 2:20 AM
  ありがとうございますー

[zukimochi] Monday, June 1, 2015 11:29 PM (+1)
stableリリースおめでとうございます。ランチに行っていろんなアイデアを聞いたり、どんどんいいプロダクトになってゆく様子を近くでを見たりと素晴らしい経験になりました。ありがとうございました！

  [Akihito Koriyama] Monday, June 1, 2015 11:39 PM
  変化を間近で見てくれて話も聞いて頂きました。ありがとうございました！

[おかぽん] Monday, June 1, 2015 10:46 PM (+1)
本当におめでとうございます！！

  [Akihito Koriyama] Monday, June 1, 2015 11:38 PM
  ありがとうございます。Symfonyユーザー会ではお世話になりました！

[kawanamiyuu] Monday, June 1, 2015 10:38 PM (+1)
おめでとうございます！こんな素晴らしい瞬間に関わらせて頂くことができて本当に嬉しいです！

  [Akihito Koriyama] Monday, June 1, 2015 10:51 PM
  ありがとうございます!こちらも沢山のコントリビュート感謝です。

[Issei_M] Monday, June 1, 2015 10:13 PM (+1)
おめでとうございます！！！！

  [Akihito Koriyama] Monday, June 1, 2015 10:23 PM
  ありがとうございます。CakePHP+Ray.Diの時もありがとうございました！

[Kei Sawada] Monday, June 1, 2015 8:21 PM (+1)
Finally! Congratulations!

  [Akihito Koriyama] Monday, June 1, 2015 9:46 PM
  Yay ! Thanks :)

[Takashi Kanemoto] Monday, June 1, 2015 7:49 PM (+1)
おめでとうございます！！これからも微力ながら貢献していきたいと思います！

  [Akihito Koriyama] Monday, June 1, 2015 9:46 PM
  ありがとうございます！

[Hari K T] Monday, June 1, 2015 7:02 PM (+1)
Yey :) .Congrats! . And always good to see Bear moving to 1.0 stable. Really happy to see the release.Enjoy!

  [Akihito Koriyama] Monday, June 1, 2015 7:12 PM
  Thanks Hari !
-->
