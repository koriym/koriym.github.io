---
title: BEAR (Sunday)
author: admin
layout: post
permalink: /2011/07/bear-sunday/
---
<div style="width:425px" id="__ss_8720699">
  <strong style="display:block;margin:12px 0 4px"><a href="http://www.slideshare.net/akihito.koriyama/bear-suday-design" title="BEAR (Suday) design" target="_blank">BEAR (Suday) design</a></strong> <div style="padding:5px 0 12px">
    View more <a href="http://www.slideshare.net/" target="_blank">presentations</a> from <a href="http://www.slideshare.net/akihito.koriyama" target="_blank">BEAR-project</a>
  </div>
</div>

[BEAR Sundayデザインメモ][1]

PHP5.3+（またはPHP5.4）専用BEARのデザインメモをスライドとgist（テキストメモ）にしました。

アーキテクチャ的には「リソース指向」の更なる追求と「RESTful CQRS」が大きなテーマです。沢山のアイデアがあるのですが、今回はその２つを前回の[BEAR1 to Saturday (2003, 2011)][2]に続いて記事にします。

### リソース指向フレームワーク

リソース指向で、MVCのモデルにあたる部分をリソースとして扱うだけでなく、ページやビューにあたる他のコンポーネントもリソースとして扱います。それによりコントローラー、ビューもリソース同様のテスト可能性や再利用性の向上が求められるのではないかと考えます。もし全てのコンポーネントがCLIで簡単にリクエストして結果が求められるのであれば、テストの記述も簡単なものになるのではないでしょうか。キャッシュや外部アプリ/ホストの利用も同様に有効になるのではと思います。<sup><a href="#footnote_0_592" id="identifier_0_592" class="footnote-link footnote-identifier-link" title="例えばidを受け取ってそのIDのユーザーリソースとリンクされたその友達リソースを取得しHTML viiewリソースにリンクする「ページリソース」を考えてみます。ページはユーザーや友達リソースの値や実装には関心を持ちません。リソースの値の取得・セットではなく、接続性だけが記述してあります。そのように高度に疎結合なページではアプリケーションを超えた利用が可能です。写真サイトでもブログサイトでも「ユーザーと友達を表示する」というリソースの接続性が変わらないためです。写真サイトのユーザーページリソースをブログサイトのユーザーページが利用する事ができます。 ">1</a></sup>

### CQRS (コマンドクエリ責務分離)

[<img src="/images/wp-content/uploads/2011/07/cqrs_architecture-300x212.jpg" alt="" title="CQRS architecture" width="300" height="212" class="alignnone size-medium wp-image-616" />][3]  
CQRSとは「コマンドクエリ責務分離」といって簡単にいうと参照と更新では責任や仕組みを分離しましょうというアーキテクチャパターンです。

[Greg Young流CQRS &#8211; Mark Nijhof][4]

例えばブログ記事の投稿を考えてみます。記事の投稿の時には、モデルはデータベースのテーブルの様々な項目に興味を持ちます。正規化され、結合され、複数のテーブルが更新されます。テーブルのコラムの情報はアトミック性をもち、関係性を持ちます。リレーショナルデータベースが有効に機能します。

ところが投稿されたこのブログ記事を読み込むときには、記事表示ページはこの関連付けされたリレーションを持った情報に興味を持たない事がほとんどです。結合、ソートされ、HTML化されたブログの記事の「文字列」が読めさえすれば読めればいいのです。この場合はただの１つの文字列がブログ記事IDから読み込めればいいということになります。つまり更新と参照では関心が異なるのです。

これを現状のBEAR（あるいは多くのフレームワーク）では通常はキャッシュとして実装し速度向上を目的にします。キャッシュは生成は簡単ですが破壊のタイミングが問題になることがあります。適当な秒数で破壊再生成したり、データ更新の時にモデルやコントローラが破壊します。

代わりにフレームワークがこれを行い、キャッシュの生成と破壊を透過にするだけでなく、キャッシュ生成のトリガーをユーザーでなくデータ作成、更新時にします。キャッシュというより、読み込み用データを書き込み時に作成すると言った方がピンと来るかもしれません。<sup><a href="#footnote_1_592" id="identifier_1_592" class="footnote-link footnote-identifier-link" title="クローラーがインデックスをつくるように">2</a></sup>

あるいは更新処理が、参照を行うまで保持されているモデルはどうでしょうか？。そのような&#8221;遅延更新モデル&#8221;ではブログを誰も見なければ、記事が実データベーステーブルにinsertされる事はありません。代わりに更新イベントハンドラ（BEARではリソースリクエストハンドラ）は誰かがそのブログを見てくれるその日まで、その書き込みイベントを保持してるはずです。<sup><a href="#footnote_2_592" id="identifier_2_592" class="footnote-link footnote-identifier-link" title="つまり、遅延読み込みだけでなく、遅延作成、遅延更新といった遅延結合が可能になります。">3</a></sup>

### REST meets CQRS

同じインターフェイスを持つ（RESTful)、実処理とそのリクエストが関心に応じて分離(CQRS)がされるという事はフレームワークやアプリケーションにとって他にどういう力をもたらすでしょうか？クエリー（参照）とコマンド（更新）はその実処理においてだけでなく、コーディングについても非対称性があります。多くの場合、<del datetime="2011-08-03T02:42:39+00:00">更新</del>参照のページの方がコーディングが簡単なのです。

あるモデルの更新と参照を違うプログラマが担う事ができます。参照データだけ用意すれば参照ページが機能するという事は、参照しか必要のないプロトタイプ開発にも役立てる事ができるでしょう。<sup><a href="#footnote_3_592" id="identifier_3_592" class="footnote-link footnote-identifier-link" title="&ldquo;この種のアーキテクチャを採用することで得られるメリットとしてもう１つ強調しておきたいものがあります。それは異なるチーム間で作業負荷を分担するのがきわめて簡単であるということです。これはチーム間に時差がある場合に特に言うことができます。ドメインロジックは正しくなければならないものです。これは単価の高い開発者を投入したいと思う場所でしょう。つまり、業務を理解し、正しいコーディングプラクティスを理解した開発者をということです。言っていることは分かりますよね？しかし、参照部分はそれほど重要ではありません。もちろん正しい必要はあるのですが、価値がある場所ではなく、素早く作って１、２年のうちに作り替えることができます。つまり、単価の低い開発者に作ってもらうことができるものだということです。ドメインに関する知識が多く求められることもなく、本当に重要なのは、GUIがどのように機能し、どのようなコマンドが使え、どのようなイベントが要求されるかということだけです。&rdquo; &ndash; Greg Young流CQRS &ndash; Mark Nijhofより引用">4</a></sup>

リクエストの公開宣言と境界の明確化、リクエストと実行の分離などCQRSの特徴の多くはフレームワークやアプリケーションのみならず、プロジェクトの進め方までに柔軟性を与える優れた可能性を持っているのではと考えます。

### webアプリケーションをwebのアーキテクチャで構築する

CQRSのwebフレームワークというものは自分が知る限り数は少なく、またこれをRESTfulにというのはアイデアとしては語られても<sup><a href="#footnote_4_592" id="identifier_4_592" class="footnote-link footnote-identifier-link" title="
DDD/CQRS  REST and CQRS  ">5</a></sup> 実装を知りません。

しかしCQRSは「コンポーネントよりむしろ接続に注目する」BEARを拡張するアーキテクチャとしては最良のものではないかと考えます。またこの実装はチャレンジな部分が大きく<sup><a href="#footnote_5_592" id="identifier_5_592" class="footnote-link footnote-identifier-link" title="今までもそうでしたが">6</a></sup> 開発時に再検証や方針の再検討など必要な不確定部分が多いとは思います。スライドやGistではアイデアを大きく広げましたが、どこもまで、あるいはどのように実装するかは検討すべき点はまだまだあります。メモでかいたデザインのいくつかは実装、検討過程でボツになるかもしれません。<sup><a href="#footnote_6_592" id="identifier_6_592" class="footnote-link footnote-identifier-link" title="その過程でよりよい実装アイデアがでるかもしれませんが ">7</a></sup>

色々な技術がありますが、やはりこれまでのBEARの開発と運用で学んだ「webアプリケーションをwebのアーキテクチャ<sup><a href="#footnote_7_592" id="identifier_7_592" class="footnote-link footnote-identifier-link" title="RESTful">8</a></sup> で構築する」有用性、そこを忘れず軸にして進めたいと思います。

「クライアントやサーバーサイドがどんなに複雑に高度になってもHTTPという接続技術のアーキテクチャが基本的に変わらず機能し続けてきた」 &#8211; これにはまだまだ学べる事があると思っています。

<ol class="footnotes">
  <li id="footnote_0_592" class="footnote">
    例えばidを受け取ってそのIDのユーザーリソースとリンクされたその友達リソースを取得しHTML viiewリソースにリンクする「ページリソース」を考えてみます。ページはユーザーや友達リソースの値や実装には関心を持ちません。リソースの値の取得・セットではなく、接続性だけが記述してあります。そのように高度に疎結合なページではアプリケーションを超えた利用が可能です。写真サイトでもブログサイトでも「ユーザーと友達を表示する」というリソースの接続性が変わらないためです。写真サイトのユーザーページリソースをブログサイトのユーザーページが利用する事ができます。 [<a href="#identifier_0_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_592" class="footnote">
    クローラーがインデックスをつくるように [<a href="#identifier_1_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_2_592" class="footnote">
    つまり、遅延読み込みだけでなく、遅延作成、遅延更新といった遅延結合が可能になります。 [<a href="#identifier_2_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_3_592" class="footnote">
    &#8220;この種のアーキテクチャを採用することで得られるメリットとしてもう１つ強調しておきたいものがあります。それは異なるチーム間で作業負荷を分担するのがきわめて簡単であるということです。これはチーム間に時差がある場合に特に言うことができます。ドメインロジックは正しくなければならないものです。これは単価の高い開発者を投入したいと思う場所でしょう。つまり、業務を理解し、正しいコーディングプラクティスを理解した開発者をということです。言っていることは分かりますよね？しかし、参照部分はそれほど重要ではありません。もちろん正しい必要はあるのですが、価値がある場所ではなく、素早く作って１、２年のうちに作り替えることができます。つまり、単価の低い開発者に作ってもらうことができるものだということです。ドメインに関する知識が多く求められることもなく、本当に重要なのは、GUIがどのように機能し、どのようなコマンドが使え、どのようなイベントが要求されるかということだけです。&#8221; &#8211; Greg Young流CQRS &#8211; Mark Nijhofより引用 [<a href="#identifier_3_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_4_592" class="footnote">
    <a href="http://groups.google.com/group/dddcqrs/browse_thread/thread/dd59a59a5495d803/9acb9d6ebf67b24b?lnk=gst&#038;q=rest#9acb9d6ebf67b24b"> DDD/CQRS REST and CQRS </a> [<a href="#identifier_4_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_5_592" class="footnote">
    今までもそうでしたが [<a href="#identifier_5_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_6_592" class="footnote">
    その過程でよりよい実装アイデアがでるかもしれませんが [<a href="#identifier_6_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_7_592" class="footnote">
    RESTful [<a href="#identifier_7_592" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: <https://gist.github.com/1111850>
 [2]: </blog/2011/07/bear1-to-saturday/>
 [3]: </images/wp-content/uploads/2011/07/cqrs_architecture.jpg>
 [4]: <http://d.hatena.ne.jp/digitalsoul/20100712/1278886009>
