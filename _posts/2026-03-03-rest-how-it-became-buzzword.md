---
layout: post
title: "RESTはどのようにバズワード化したか"
date: 2026-03-03 12:00:00 +0900
comments: false
categories: ["blog"]
tags:
  - REST
permalink: /blog/2026/03/03/rest-how-it-became-buzzword
image: /images/2026-03-03-rest-how-it-became-buzzword/roy-t-fielding.jpg
---

<figure class="author-portrait">
<img src="/images/2026-03-03-rest-how-it-became-buzzword/roy-t-fielding.jpg" alt="Roy T. Fielding">
<figcaption>Roy T. Fielding</figcaption>
</figure>

REST APIは一般に、`GET /users/123` のようなリソース指向のURL設計とHTTPメソッドによるCRUD操作のスタイルだと思われていますが、本来はサーバーのレスポンスに含まれるリンクを辿ってアプリケーション状態を遷移させる、ハイパーメディアを基本としたスタイルです。このことは広く誤解されています。

しかしそれを知っている人でもなお、RESTはAPIの設計手法の1つだと誤解しています。RESTはAPI設計についてまとめたものではありません。分散ハイパーメディアシステム——HTTPの設計原理を体系化したものです。

この違いを解説した記事は少ないですが、どうしてこのような誤解が広まったかを考察する記事はもっと少ない。本稿は、RESTがどのようにバズワード化し、広まっていったかについて考察します。

---

## 時系列で見るREST前史

### 1996年 — HTTP/1.0（RFC 1945）

Roy T. FieldingはTim Berners-Lee、Henrik Frystyk Nielsenとともに、HTTP/1.0の仕様（RFC 1945）を策定しました。FieldingはこのときすでにApache HTTP Serverの開発にも関わっており、Webインフラの実装と仕様の両方を知る立場にいました。

### 1997–1999年 — HTTP/1.1（RFC 2068 → RFC 2616）

Fieldingは HTTP/1.1 の共著者として、プロトコルの拡張設計に深く関わります。1997年にRFC 2068として初版が公開され、1999年にRFC 2616として標準化されました。

この策定プロセスの中で、Fieldingは具体的な設計判断を下しています。たとえば、複数リソースを一括取得するMGETやMHEADというメソッドの提案を、プロキシ可能性やキャッシュ可能性を損なうという理由で却下しました。代わりに、持続的接続（persistent connection）上で複数リクエストを送る設計が採用されています。

### 2000年 — REST論文

Fieldingはカリフォルニア大学アーバイン校に博士論文「Architectural Styles and the Design of Network-based Software Architectures」を提出しました。

この論文が、RESTの原典です。

---

## 論文は何を書いていたか

重要なのは、この論文の動機です。Fieldingは新しいAPI設計手法を提案したのではありません。HTTP/1.1の策定プロセスを導いたアーキテクチャ上の原理を、事後的に体系化したのです。

つまり順序が逆です。REST論文に基づいてHTTPが設計されたのではなく、HTTPの設計判断を振り返り、その背後にあった原理を「REST」として定式化した。論文は理論が先にあったのではなく、実践から理論を抽出したものでした。

さらに言えば、論文の主題はRESTそのものですらありません。論文の大半は「ネットワークベースのソフトウェアアーキテクチャスタイルの分類学」に費やされており、REST自体が語られるのは1章だけです。分類学に並ぶスタイルには、Pipe-and-Filter、Layered-Client-Server、Client-Cache-Stateless-Serverなどがあり、RESTはこれらの制約を組み合わせた派生スタイルです。正式に書けばUniform-Layered-Code-on-Demand-Client-Cache-Stateless-Server（ULCODC$SS）——さすがにこの名前は採用されませんでしたが。

論文の核心的メッセージは「一つのアーキテクチャスタイルをすべてに使うな。アプリケーションの要件に合ったスタイルを選べ」です。Fielding自身こう書いています。

> "design-by-buzzword is a common occurrence. ... a good designer should select a style that matches the needs of a particular problem being solved."
>
> バズワードに基づく設計はよく見られる現象だ。（中略）優れた設計者は、解決すべき特定の問題のニーズに合ったスタイルを選択すべきだ。

そしてRESTの適用範囲についても明確です。

> "REST is designed to be efficient for large-grain hypermedia data transfer, optimizing for the common case of the Web, but resulting in an interface that is not optimal for other forms of architectural interaction."
>
> RESTは大粒度のハイパーメディアデータ転送に対して効率的になるよう設計されており、Webの一般的なケースに最適化されているが、他の形式のアーキテクチャ上のやりとりには最適ではないインターフェースをもたらす。

論文が対象とする問題領域は「アナーキックなスケーラビリティ」——組織や国境を越えて、ドキュメントを高パフォーマンスで接続するという課題です。これはWebそのものの構造であり、企業内のバックエンドAPIやモバイルアプリのBFFではありません。

---

## 統一インターフェース

RESTを構成するアーキテクチャ制約のうち、中心に位置するのが「統一インターフェース」（Uniform Interface）です。

Fieldingは論文で、この制約を4つのサブ制約として定義しています。

1. **リソースの識別**（Identification of Resources）— すべてのリソースにURIで識別できるアドレスを与える
2. **表現を通じたリソースの操作**（Manipulation of Resources through Representations）— リソースそのものではなく、その表現（JSON、HTML等）を介して操作する
3. **自己記述的メッセージ**（Self-descriptive Messages）— メッセージ自体に、処理に必要な情報がすべて含まれている（あるいはリンクされている）
4. **アプリケーション状態のエンジンとしてのハイパーメディア**（HATEOAS）— 次に何ができるかは、サーバーが返すリンクが教える

4番目のHATEOAS——クライアントはサーバーから返されるリンクを辿って次のアクションを発見する。Fieldingは後年、「これなしにRESTとは呼べない」と明言しています。

---

## SOAPの時代、そしてその反動

### 2000年代前半 — SOAP全盛

REST論文が発表された2000年、Web上のサービス連携はまったく別の方向に進んでいました。SOAP（Simple Object Access Protocol）です。

SOAPはXMLベースのプロトコルで、リクエストもレスポンスもXMLのエンベロープに包んで送受信します。型定義にはWSDL、サービス検索にはUDDI、セキュリティにはWS-Securityと、関連仕様が層をなしていました。エンタープライズの世界では、この形式性こそが信頼性の証と見なされていました。

### 2000年代中頃 — 反発と「REST」のバズワード化

しかし、SOAPの複雑さは実装者に重い負担を強いていました。XMLの構文解析、WSDLからのコード生成、名前空間の衝突——Webの現場でAPIを素早く作りたい開発者にとって、SOAPは過剰でした。

この反動の中で、すでに2004年には「REST」という言葉を使ってCRUDマッピングを明記した記録が登場しています。英国の図書館情報学者[Matthew J. Dovey](https://www.ceridwen.com/srw/record-update/crud-html/)が、Fielding論文を参照しつつ「REST maps the CRUD operations onto the HTTP verbs（Create=POST, Retrieve=GET, Update=PUT, Delete=DELETE）」と書いた記事を公開しました。

「もっとシンプルにHTTP本来の機能を使おう」——この流れの中で、SOAPへのカウンターカルチャーのバズワードとして「REST」という言葉が流通し始めます。HTTPのメソッドとステータスコードをそのまま使い、URLにリソースの構造を反映させる。WSDLもUDDIもいらない。この実用的な判断は合理的でした。HTTPがすでに提供しているセマンティクスとインフラ（プロキシ、キャッシュ、ロードバランサー）をそのまま活用できる。

ただし、このアプローチはFieldingの論文が記述したRESTとは別物です。[TwoBitHistory](https://twobithistory.org/2020/06/28/rest.html)（2020年）はこれを **FIOH**（**Fuck It, Overload HTTP** ——もういいからHTTPに全部載せよう）と呼んでいます。FIOH自体に問題はありません。問題は、FIOHに過ぎないものに「REST」という学術的な名前が被せられたことです。

### 2007年 — DHHとRails

Ruby on Railsの2.0リリースで、SOAPサポート（ActionWebService）がデフォルトから廃止されました。[公式ブログの発表](https://rubyonrails.org/2007/12/7/rails-2-0-it-s-done)でDHH（David Heinemeier Hansson）はこう書いています。

> "Rails has picked a side in the SOAP vs REST debate. Unless you absolutely have to use SOAP for integration purposes, we strongly discourage you from doing so."
>
> RailsはSOAP対RESTの論争で立場を選んだ。統合のためにどうしてもSOAPを使わなければならない場合を除き、使用しないことを強く推奨する。

同時に、RailsはRESTfulルーティングをフレームワークの中心的な規約として導入しました。`resources :articles` と書けば、HTTPメソッドとCRUD操作の対応が自動的に生成される。DHHはこれを「RESTful」と呼び、アンチSOAPの旗印としました。

DHHは無知だったのではありません。[2007年のブログ](https://dhh.dk/posts/12-good-times-at-railsconf-europe)で「この1年半は、Royが10年以上関わってきたアイデアと仕様を理解し実装しようとすることに費やされた」と書いており、RailsConf EuropeではFielding本人と直接対話もしています。その上で、[2012年のブログ](https://dhh.dk/2012/the-parley-letter.html)ではRESTを導入した動機について「知的純粋性のためではない」と書き、設計原則の評価基準は「最近何をしてくれたのか？」だと述べています。BasecampのAPIでハイパーメディアを試みたものの「実際の利益が少なかった」とも。[同年のSignal v. Noise](https://signalvnoise.com/posts/3373-getting-hyper-about-hypermedia-apis)ではさらに踏み込み、ハイパーメディアAPIを「completely overblown（完全に過大評価）」と断じました。

Fieldingの仕事を知り、本人と話し、HATEOASを試した上で「自分には要らない」と判断した。これは誤解ではなく選択です。「REST」の名を選んだのは、アンチSOAPの旗印としてのマーケティング的な意味と、他の解説にもあるように権威づけの意味もあったのではないかと筆者は考えます。ある種の"技術用語負債"です。

### 2008年 — Fieldingの反応

Fielding本人がブログ記事「REST APIs must be hypertext-driven」を公開しました。

> "I am getting frustrated by the number of people calling any HTTP-based interface a REST API."
>
> あらゆるHTTPベースのインターフェースをREST APIと呼ぶ人々の数に、フラストレーションを感じるようになっている。

Railsは2000年代後半から2010年代にかけてWeb開発の主要なフレームワークとなりました。「RailsでWebを学んだ」世代にとって、RESTを学ぶとはRailsのルーティング規約を学ぶことと、ほぼ同義でした。人々はDHHのブログやRESTのチュートリアルは読んでも、Fieldingの論文を読むことはなかったのではないでしょうか。

---

## "RESTish API" の誕生

こうして生まれたのが、今日「RESTful API」と呼ばれているもの——RESTish、つまり「なんちゃってREST」のAPIです。その規則を並べてみます。

**URL設計**：リソースを名詞で表現し、階層構造をパスで表す。

```text
GET  /users
GET  /users/123
GET  /users/123/posts
```

**HTTPメソッドのCRUDマッピング**：

| メソッド | 操作 |
|---------|------|
| GET     | 取得 |
| POST    | 作成 |
| PUT     | 全体更新 |
| PATCH   | 部分更新 |
| DELETE  | 削除 |

**ステータスコード**：`200 OK`、`201 Created`、`404 Not Found`、`422 Unprocessable Entity` など、HTTPのステータスコードでビジネスロジックの結果を表現する。

**データ形式**：JSON

**API仕様の共有**：OpenAPI（Swagger）などの外部ドキュメントで、エンドポイントの一覧・パラメータ・レスポンス形式を記述し、開発者に共有する。

これが現在「RESTful API設計のベストプラクティス」として広く教えられている内容です。Fieldingが定義した統一インターフェースの4つの制約のうち、「URLとHTTPメソッドの組み合わせ」という一番分かりやすい部分だけをつまみ食いしたものとも言えます。

HATEOASは落とされ、自己記述的メッセージの代わりにOpenAPIの外部ドキュメントが置かれました。

### "RESTish API" の普及

2007年、O'Reillyから「[RESTful Web Services](https://www.oreilly.com/library/view/restful-web-services/9780596529260/)」（Leonard Richardson、Sam Ruby著）が出版されました。RESTの名を冠した初の本格的な書籍です。この本はCRUD-over-HTTPのスタイルを「RESTful」として体系的に解説し、Ruby on Rails、Restlet（Java）、Django（Python）での実装方法を示しました。

Railsが規約として組み込み、書籍が体系化し、技術ブログやチュートリアルはこれらを参照して書かれ、そのブログを読んだ開発者がまた次のチュートリアルを書く。「RESTful API設計」は、Fieldingの論文を経由せずに自己増殖していきました。

### リチャードソン成熟度モデル

2008年、同じLeonard RichardsonがQConで「Richardson Maturity Model」を発表しました。REST APIの「成熟度」を4段階で分類するモデルです。2010年にはMartin Fowlerが[記事](https://martinfowler.com/articles/richardsonMaturityModel.html)にして広く知られるようになりました。

| レベル | 内容 |
|--------|------|
| Level 0 | HTTPを単なるトランスポートとして使う（POX） |
| Level 1 | 個別のリソースにURIを割り当てる |
| Level 2 | HTTPメソッド（GET/POST/PUT/DELETE）を使い分ける |
| Level 3 | HATEOAS——レスポンスにリンクを含め、クライアントを導く |

Level 2で「RESTful」、Level 3まで行けば「完全なREST」——このモデルはそう読めます。

Fieldingは「HATEOASなしにRESTとは呼べない」と言いました。しかしこの成熟度モデルでは、HATEOASは最上位のLevel 3、つまり「到達すれば理想的」なオプションになっています。

FieldingのRESTはWebのアーキテクチャ設計原理を蒸留したものでした。リチャードソン成熟度モデルは、その「なんちゃってREST」を蒸留したものです。そして**Martin Fowler**がそれに権威を与えました。

### ベストプラクティス化

2012年、API管理企業のApigeeが「[Web API Design — Crafting Interfaces that Developers Love](https://web.archive.org/web/2024/https://pages.apigee.com/rs/apigee/images/api-design-ebook-2012-03.pdf)」というeBookを公開しました。Fieldingの論文を参照した上で、こう宣言しています。

> "We advocate pragmatic, not dogmatic REST."
>
> 我々は教条的ではなく、実用的なRESTを推奨する。

Fieldingの論文に忠実であろうとする人々を「RESTifarian」（REST原理主義者）と呼び、その対極として「pragmatic REST」を提唱しています。内容は「URLに動詞を使うな」「リソースごとにベースURLは2つ」「HTTPメソッドでCRUD操作を表現せよ」——Fieldingの論文のどこにもない規則群です。

分散ハイパーメディアとしての疎結合の設計原則が、クライアントとサーバーの密結合プロトコルとなり、体系化され、URLにバージョニングナンバーをつけることが常識になっていきました。

ここまでくればほとんどコメディです。論文を出典として挙げ、その内容を「dogmatic」と退け、論文にない規則を「ベストプラクティス」として体系化した。その後2016年、GoogleがApigeeを買収して体系化に権威が加わりました。

### 遅すぎた訂正

2013年、同じO'Reillyから「[RESTful Web APIs](https://www.oreilly.com/library/view/restful-web-apis/9781449359713/)」（Richardson、Mike Amundsen、Ruby著）が出版されます。前著の「完全な置き換え」を意図した本で、ハイパーメディアを中心に据え直しています。

しかし、遅すぎました。確信的誤解は、体系化され、権威づけられ、強固なものとなり、コピーのコピーはすでに止まらなくなっていました。

### 修正の弱さ

誤解への指摘がなかったわけではありません。「それはFieldingのRESTではない」という声は折に触れて上がりました。Fielding本人も2008年にブログで反論しています。

しかし、そのたびに議論は「どちらが実用的か」という話にすり替わりました。「HATEOASは理想的だが現実的ではない」「学術的な正しさより動くコードだ」——こうして事実の問題（FieldingのRESTとは何か）が、価値判断の問題（どちらが実用的か）に置換されていきました。

この置換が起きると、修正は機能しません。「それは事実と違う」に対して「でも便利だから」は反論になっていませんが、議論としては決着がついたように見えてしまう。結果として、誤解は誤解のまま定着し続けました。

---

## 三つの問い

ここまでの話には、三つの別々の問いが含まれています。

1. **事実の問い** — Fieldingの論文は何を書いていたか
2. **実用の問い** — CRUD-over-HTTPは便利か
3. **命名の問い** — それを「REST」と呼ぶ必要があったか

2番の答えは「はい」です。HTTPのメソッドとステータスコードをそのまま使い、JSONでやりとりするアプローチは合理的でした。これはSOAPとの対比において明らかです。

しかし2番が正しいことは、1番や3番への回答にはなりません。「REST論争」では、この三つが「学術的な正しさ vs 実用性」という単純な二項対立に圧縮されてきました。この圧縮の下では、事実や命名への疑問が、実用性への攻撃として処理されます。「それはFieldingのRESTではない」と言えば「でも便利だから」と返される。問いが違うのに、答えたことになっている。

そして業界は誤用の上に大量のエコシステムを積み上げました。"RESTful API" はFieldingのRESTとは別のものとして、すでに機能しています。誤解の痛みを保ちながらも。

そうやって、"REST"のコピーのコピーは今日も続いています。以上が現実です。

## エピローグ - いつになったら気づくのだろう

2017年、Fieldingは学会の講演でこう述べています。

> "REST has become a buzzword. There's nothing particularly wrong with that… unless you happen to be me or working with me."
>
> RESTはバズワードになった。別にかまわない——この私か、私と働く人間でさえなければ。

2021年、TwoBitHistoryのREST記事をきっかけに、Fieldingはツイートスレッドを投稿しました。ワクチンブースター接種の副反応のせいかもしれないと言いつつ、こんなメッセージを残しています。

> 私の論文に対する「だいたい正しい」解釈の問題は、木を見て森を見ていないところだ。RESTはAPIについてのものではない。ネットワークベースのアプリにおける**システム間の相互作用**についてのものだ。ただし、その制約群は統一されたネットワークベースのAPI（URI+HTTP+HTML等）を誘発するよう設計されてもいた。
>
> 確かにRESTはバズワードだ。予想通り乱用されてきた。だがRESTの眼鏡を通してWebを見れば、そのアーキテクチャの中に汎用的なネットワークベースのAPIが設計されていることが分かるはずだ。
>
> なのに、APIが欲しい人々は「RESTから学べ」と言われると、ライブラリをimportするように「RESTを使え」と解釈してしまう。そういうものではないのに。さらに結局はアドバイスそのものが悪いということにされる...
>
> 私がここに座って開発者たちを責めても始まらない。当時、彼らを読者として想定しないと決めたのは私自身なのだから。
>
> だが、いつになったら気づくのだろう。自分が対価を得ている理由、優れた仕事で尊敬されている理由は、**自分の頭を使って自分のシステムの文脈に合わせた詳細を埋めていける能力**があるからだということに。Learn, apply, iterate!

RESTはツールではなく、レンズ。本物の設計者が教えてくれるのは道具の使い方ではなく問題の見方でした。

---

## 参照

- 2000: Roy T. Fielding, [Architectural Styles and the Design of Network-based Software Architectures](https://roy.gbiv.com/pubs/dissertation/top.htm)（[日本語訳](https://koriym.github.io/fielding_dissertation_ja/fielding_dissertation_ja.html)）
- 2004: Matthew J. Dovey, [C.R.U.D. WebServices and Record Update](https://www.ceridwen.com/srw/record-update/crud-html/)
- 2004: Bob DuCharme, [Telnet and REST Web Services?](https://www.xml.com/pub/a/2004/12/15/telnet-REST.html)（xml.com）
- 2007: DHH, [Good times at RailsConf Europe](https://dhh.dk/posts/12-good-times-at-railsconf-europe)
- 2007: Ruby on Rails, [Rails 2.0: It's done](https://rubyonrails.org/2007/12/7/rails-2-0-it-s-done)
- 2008: Roy Fielding, [REST APIs must be hypertext-driven](https://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)
- 2010: Martin Fowler, [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)
- 2012: DHH, [The Parley Letter](https://dhh.dk/2012/the-parley-letter.html)
- 2012: DHH, [Getting hyper about hypermedia APIs](https://signalvnoise.com/posts/3373-getting-hyper-about-hypermedia-apis)
- 2012: Apigee (Brian Mulloy), [Web API Design — Crafting Interfaces that Developers Love](https://web.archive.org/web/2024/https://pages.apigee.com/rs/apigee/images/api-design-ebook-2012-03.pdf)
- 2017: Roy Fielding et al., [Reflections on the REST Architectural Style](https://dl.acm.org/doi/10.1145/3106237.3121282) ESEC/FSE（[slides](https://roy.gbiv.com/talks/201709_Fielding_REST.pdf)）
- 2020: TwoBitHistory, [Roy Fielding's Misappropriated REST Dissertation](https://twobithistory.org/2020/06/28/rest.html)
- 2021: Roy Fielding, [Twitter](https://x.com/fielding/status/1458499369204785152)
