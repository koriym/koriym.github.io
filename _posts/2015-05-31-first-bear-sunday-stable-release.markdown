---
layout: post
title: "First BEAR.Sunday Stable Release !"
date: 2015-05-31 18:50
comments: true
categories: articles
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
