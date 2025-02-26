---
title: BEAR1 to Saturday (2003, 2011)
author: admin
layout: post
permalink: /2011/07/bear1-to-saturday/
categories: ["blog"]
tags:
  - BEAR
  - フレームワーク
---

### Sundayへ 

元々頭にあったBEARのラフなロードマップとしてv0.8までに機能的な実装を終え、v0.9でテストとパフォーマンスチューニング、リファクタリングを行いv1.0に繋げるというものでした。着々と続けてたのですが、（特にPHP 5.3に合わせた）PHPでの開発手法やwebアプリケーションフレームワーク世界の大きな潮流の変化を感じ、フルスクラッチで再構築する事を決めました。それをBEAR2にするのか、BEAR v1.0にするのか今はまだ未定ですが、そのコード名を”Sunday”にしました。（同時に現状今サービスしてるPHP5.2+のものが&#8221;Saturday&#8221;になりました。）

これを機会にこれまでを一度振り返って記録します。

### BEAR1 

最初のBEARの開発と利用は2003年12月です。自分でもびっくりするほど古いのですが、日本でMojaviが2004年後半から注目を集めたという事ですのでその１年前ぐらいです。 

構成としてはページコントローラのシンプルなMVCのようなものでした。ページクラスをテンプレートパターンで必要メソッドを記述してメインクラスがページクラスをイベントに応じてコールし、ページの結果をHTMLとして出力する&#8230;そういう基本は今のBEARと同じです。

つまりメイン（クライアント）とページ（サービス）の分離が設計の中心です。モデルはクラス設計されてて、そのメソッド内（例えばUser::postEntry()）でPEAR::DBを使ってSQLを発行してました。テンプレートエンジンにはSmarty、ページャーにPEAR::Pagerなどを用い、認証にTCI(電話認証）を使う今で言う携帯専用のSNSです。

これはインスパイアされたり、お手本にしたものがありません。何も知りませんでした。フレームワークという言葉もMVCもこれをつくったずっと後で知りました。複数人数での開発と、webの処理フローの単純さから、こういうのがいいんじゃないかと思って作ったのがこの時のBEARです。 <sup><a href="#footnote_0_394" id="identifier_0_394" class="footnote-link footnote-identifier-link" title="PEARコンポーネントのグルー（当時言葉知りませんでしたが）が必要だとは思っていました。BEARの名前の由来の一つでもあります。">1</a></sup>

なので自分のWebプログラミングのごく初期からフレームワークをつくってたということになります。

### 旧Bear

2005年5月19日にAUから携帯専用ブログDuo Blogがリリースされます<sup><a href="#footnote_1_394" id="identifier_1_394" class="footnote-link footnote-identifier-link" title="テレビCMでは仲間由紀恵がブログもできるよ！とCMしていました">2</a></sup> これに用いたのが&#8221;Bear&#8221;です。しかしこの時のBear<sup><a href="#footnote_2_394" id="identifier_2_394" class="footnote-link footnote-identifier-link" title="数年ぶりのPHP、Webプログラミング">3</a></sup> の反省は大きくリリース後に大改造を行い、アーキテクチャ的に今のに近い形にはなりました。まだ時代はPHP4です。

オブジェクトモデルを廃止して、自由にメソッドを持てないようにしました（統一インターフェイス）、その時のメソッドはたった２つ。「参照」と「アクション」だけでした。<sup><a href="#footnote_3_394" id="identifier_3_394" class="footnote-link footnote-identifier-link" title="これはモデルがあり、つまりDBの「ビュー」と「ストアドプロシージャ」です。PC用でASPのレガシープロジェクトをPHPでモバイル用に作り替えたときに、この構成の疎結合性が大変有効に機能するのに注目しました">4</a></sup>リクエストは状態を持たず（ステートレスリクエスト）、機能毎に名前があり（アドレス可能性）、処理は処理を呼ぶ事ができ（レイヤード）処理を直接は呼ばないでそのモデルアクセスクラスが間接的に呼ぶようになっていて機能名と引き数と状態を持たないので簡単にキャッシュがつくれました(ULC$SS=Uniform-Layered-Client-Cache-Stateless-Server) 。今考えるとリソース指向設計(ROA)の特徴の多くを持っていたのですが、まだそのときそういう言葉や概念を知らず、手探りで良い設計を求めていました。

つまりモデル部分を関数ファイルにしたのです。URIは要は関数名です。関数はステートレスです。キャッシュ化もレイヤー化も簡単です。独立性もあり、利用クライアント対してに内部技術の暴露はありません。時代はPHPもOOPという感じでしたが、このモデル部分に関しては完全に逆を行きクラスから関数にしました。

単純ですがこのアーキテクチャは機能しました。スコープは制限され、モジュラー性は高まり、複数人での作業もスムーズになりました。学習、コーディング、メンテ、CPU、コストが低下しパフォーマンスもあがったように思えました。携帯用で始めましたが、PCや途中でやってきたAJAXブームにも(prototype.jsで）対応しました。ROR / CakePHPの大波も来たのですがマイペースで開発を続けました。

### BEAR meets REST.

ある日、山本陽平さんの[REST 入門 ][1]を知りました。<sup><a href="#footnote_4_394" id="identifier_4_394" class="footnote-link footnote-identifier-link" title="2005年4月の新しいとはいえない記事ですがこの時まで知りませんでした。">5</a></sup>

自分が考えてた事、経験を通じて知った事、BEARの基本設計部分ですがその全てに名前付き解説がされてる事を知ります。これはとてつもない衝撃でした。BEARはRESTfulとしてつくってのではなくRESTfulだったのです。良いのでは信じてた設計のほとんどはHTTPで実装されてたものでした。自分では解決できなかった問題の解答もすでにありました。HTTP/RESTのと偉大さを改めて知り、そろそろと考えていたPHP5対応に明確な指針が生まれました。「[リソース指向アーキテクチャ][2]」です。

つまり、BEARは従来のMVCのアンチテーゼや、REST賛美から生まれた訳ではありません。アプリケーション要求と開発メンテ効率の最大化を求め、それを実践していて気がついたらRESTfulだったのです。

### Saturday

#### デザイン

他のフレームワークに詳しい人や従来使ってた人に色々ヒアリングを行って追加機能も決めて行きました。例えば[ツーステップビュー][3]などです。しかし不思議な事に他のフレームワークでは当たり前のフロントコントローラーやActive Record / ORMを検討してほしいという人はその時あまりいませんでした。それでもそれらを否定するものではなく、後から簡単にアドオンできるという目処があり標準で不採用は簡単に決まりました。

またリソースアクセスにhttpと同じようなURI<sup><a href="#footnote_5_394" id="identifier_5_394" class="footnote-link footnote-identifier-link" title="迷ったURIテンプレートの採用は見送りました">6</a></sup>を使う事にしました。これにより処理系をスキーマとして切り替える事ができ、今まで固定されていた「関数」というただ一つのモデル・アーキテクチャの依存から抜けることができました。これは過去に作成した関数リソースを無駄にしないだけでなく、別アーキテクチャのレガシーモデルも同じように扱えることを意味していました。

URIでモデルとコントローラを接続するのは単にCとMにAというラッパーを差し込もうというのとは別の話です。URIスキーマのスキーマは本当の意味でのスキーマ（式型、図式、形式、シェーマ=ドイツ語）になり、接続に異なったソフトウエアパラダイムを持ち込む事が可能になりました。 

Active Recordなどコンポーネントの利用の簡単さと機能の豊富さを語る時代ですが、むしろ、その接続と入れ替え可能性に注力しようという方針も立てました。SQLを記述するのがいいか、ORMがいいのかではなく、それらが選択可能で等価的であるという事が重要だと考えました。RESTfulがそれを実現します。

#### 標準を選択

コンポーネントはなるべく標準的なもの広く使われているようなものをを選択するように考えました。迷ったのは開発終了のPEAR::DBに変わりにPDOを使うかPEAR::MDB2を使うかです。より標準的なのはどちらでしょうか？エクステンションであるPDOにも思えます。

結局、より抽象化された拡張機能とPEAR::Pagerと相性の良さそうなMDB2にしたのですが<sup><a href="#footnote_6_394" id="identifier_6_394" class="footnote-link footnote-identifier-link" title="今ならDoctorine DBALでしょうか">7</a></sup>　しかし重要なのはアプリケーション開発者がMDB2不使用を決めた場合に、それがマイナスにならないという事です。その場合はリソースのMDB2の依存はゼロにならないと行けません。

コード規約、ファイル配置、命名規則、それら全てをより標準的なものを採用しようという方針もたてました。これは難しくありません、（規約などの）フレームワークとしてPEARには圧倒的なプレゼンスがあり、Zendも基本的にはそれを踏襲していました。SolarPHPもそうでそれらに習うだけです。<sup><a href="#footnote_7_394" id="identifier_7_394" class="footnote-link footnote-identifier-link" title="その点何故SymfonyやCakePHPがああいう俺俺規則なのかはさっぱり分かりませんでした。">8</a></sup> JSも標準で用意するbear,jsをprotyotype.jsベースからjQueryベースにしました。

#### DI

RESTと並んで初期に注力したのはDIです。今でもそうですが、PHPに要らないという意見が多くありまた色々な実装が出ては定着してないという現実もありました。しかし、最大の問題は「自分がよく分かってない」という事でした。（笑）<sup><a href="#footnote_8_394" id="identifier_8_394" class="footnote-link footnote-identifier-link" title="多くの解説記事は特定のDI実装と強く結ばれた話ばかりで、真の本質的なところや使用すべき用語がなかなか分かりませんでした。分離の原則を実現する技術の解説なのに皮肉な話です">9</a></sup>

PHPでは標準的でもなく、自分が使った事もなく、要求もされてないソフトウエアパラダイムを採用に引かれたのはもちろん理由があります。旧Bearであった問題の解決と、方針として立てた「入れ替え可能性」です。

ライブラリを整備していくと、様々な要求があります。例えばiPhoneのページ用意されてなかったら、表示するのは携帯用でしょうか? PC用でしょうか？そういう一つ一つの設定、ロジックに様々な要求がある場合にグローバルdifineや、configファイルをどんどん増やして行ってユーザーが選択可能にしていくのがway to goでしょうか？フレームワークコードにバグがあれば、あるいは機能追加したければユーザーはどういう行動をとる事ができるのがベストでしょうか？

インスタンス管理にも不満がありました。DRYといいながら同じようなシングルトンコードが色々なクラスに存在します。

コンストラクタの引き数のデフォルトはどうやって決めたら良いでしょうか？クラスに直接かけば値がハードコードされてます。グローバルdefineを山のように書いてたのが旧Bearです。クラスを生成するとき > アプリケーションのデフォルト > フレームワークのデフォルトと優先順位をもって初期値を決めたい時に、旧来のnew演算子や各メソッドが実装するsingleton/factoryパターンでは限界がありました。<sup><a href="#footnote_9_394" id="identifier_9_394" class="footnote-link footnote-identifier-link" title="New Considered Harmfulという記事を読んだのもその頃です。">10</a></sup>

またどういうDI実装がいいのかというのも大きな問題でした。旧来のやり方の弊害はJavaでも語られていました。JavaではXMLは「アジャイル」ですがPHPでは「コードの方がアジャイル」とも言えます。欲しいのは形式としての正しさよりその効果です。

#### Solar::dependency() 

そこで大きく惹かれたのはPHP5専用フレームワークのSolarPHPの[Dependency Injection][4]です。ただし、そのままではちょっとmagicすぎるという印象を持ち、そこでインジェクターメソッドというコンストラクトの後に呼ばれる注入メソッド（実際は初期化メソッド）を生成時に必ず呼ばれるようにし、そこで利用オブジェクトをサービスロケータ経由でPull（Dependency Pull)する仕組みのものを設計しました。そのメソッドは外部から選択可能です。生成時に呼ばれるという点ではコンストラクトインジェクションに近いものですが、選択可能であるという点でそのデメリットを補い、コンテナを不要にできるのではと考えました。

「いや、しかしクラス自身が依存オブジェクトをとってきてこれって注入..?」「外部インジェクターが使えるし&#8230;」色々思いましたが、しかし重要なのは外部から依存（利用）オブジェクトをコントロールできるのか？という事です。利用メソッドから見れば、どっかの誰かが準備してくれて、利用オブジェクトは準備されてます。それがコントロールできるのが重要です。言葉の厳密性に多少目をつぶりSolarPHPのDIの模倣し、より単純にしてDIが解決することを別のより簡単な手段で解決しようと試みました。

それともう一点、初学者がスムーズにたとえば模倣から入れるかということでした。

学習コストの低減は大きなテーマです。そこで「制御の反転って知ってますか？」「依存性の注入とはですねえ、DIコンテナってのがありまして」というようにパラダイムの説明抜きには入れなうような事態に抵抗がありました。「BEAR::dependencyってnewみたいなもんなんですよ、クラス名と初期値でオブジェクトが得られます。」という風に旧来のnew演算子からスムーズに入れそうな気がした事も採用動機になりました。

ほとんどをSolar::depedncy()から学びました。リスペクトもありメソッド名を変えずにそのままにしてます。最初に恩恵を受けたのはBEAR自身です。BEARではこの技術の適用範囲に制限を持ちませんでした。後にデバック用やテスト用の別コンポーネントを持つときにも大きな効用を発揮しました。

現在発表されているPHP5.3+用のフレームワークの多くにDI <sup><a href="#footnote_10_394" id="identifier_10_394" class="footnote-link footnote-identifier-link" title="またはDIの解決しようとする問題を解決する技術">11</a></sup>が導入されていて、この方針の方向性にある程度の確信が生まれます。また、DIの他にも以下のPHP5.3+フレームワークの多く（またはいくつか）で見られる特徴もありました。

*   オートローダーOnly (PSR-0)
*   アノテーション
*   非フルスタック指向
*   非スタティックコール指向
*   ユニバーサルコンストラクタ
*   グローバルdefineの原則廃止
*   ミニマムレイテンシー（速度）の重視
*   遅延評価（レイジーロード）

#### リソースオブジェクト

BEARで様々につかわれてるDTO（データー転送用オブジェクト）がRO（リソースオブジェクト）です。このDTOはcode,header, bodyといったリソースの値を保持するのも大きな役割ですが、リクエストに対する統一インターフェイスとリンク機能を備えてるのが特徴です。リソース内部で何が起ころうともクライアントには同じフォーマットのものが帰ってくるのはHTTPをモデルにしています。ユーザーのURI入力間違いだろうと、内部のサーバーエラーであろうとwebサーバーは同じフォーマット(code, header, body)のデータを処理するだけで、そのリクエスト状態に関心を持つことはありません。

新しく最もROA的に思えたの機能がリンクです。Aタグの無いWWW世界を想像してみてください。あるいは情報と情報の関係性を利用者側ですべて把握する必要がある情報システム。それらに命を吹き込むのがリンクです。その点、今のBEARのリンク実装はまだ大きなイノベーションの余地があると考えています。

### Saturday公開

2008年7月31日に初めてBEARを外部に公開します。PHP5.2対応の初のリリースです。  
最初の動機はごく単純で、社内リポジトリ/wikiだと外注さんが見れないというものでした。

### そしてSundayへ

ここまでが簡単にまとめた「BEARの今まで」です。  
これまでの成果と反省をしROAを再確認しつつSundayに繋げたいと思っています。

そのために現在起こっているWebアプリケーションの開発手法の変化、アプリケーション要求、クライアントとアウトプットの多様化、PHPの言語仕様等、ソフトウエア設計の外側の世界もバランスのとれた態度を示さなければなりません。解決としてのRESTfulがその解答になると思っています。

<ol class="footnotes">
  <li id="footnote_0_394" class="footnote">
    PEARコンポーネントのグルー（当時言葉知りませんでしたが）が必要だとは思っていました。BEARの名前の由来の一つでもあります。 [<a href="#identifier_0_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_394" class="footnote">
    テレビCMでは仲間由紀恵がブログもできるよ！とCMしていました [<a href="#identifier_1_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_2_394" class="footnote">
    数年ぶりのPHP、Webプログラミング [<a href="#identifier_2_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_3_394" class="footnote">
    これはモデルがあり、つまりDBの「ビュー」と「ストアドプロシージャ」です。PC用でASPのレガシープロジェクトをPHPでモバイル用に作り替えたときに、この構成の疎結合性が大変有効に機能するのに注目しました [<a href="#identifier_3_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_4_394" class="footnote">
    2005年4月の新しいとはいえない記事ですがこの時まで知りませんでした。 [<a href="#identifier_4_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_5_394" class="footnote">
    迷ったURIテンプレートの採用は見送りました [<a href="#identifier_5_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_6_394" class="footnote">
    今ならDoctorine DBALでしょうか [<a href="#identifier_6_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_7_394" class="footnote">
    その点何故SymfonyやCakePHPがああいう俺俺規則なのかはさっぱり分かりませんでした。 [<a href="#identifier_7_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_8_394" class="footnote">
    多くの解説記事は特定のDI実装と強く結ばれた話ばかりで、真の本質的なところや使用すべき用語がなかなか分かりませんでした。分離の原則を実現する技術の解説なのに皮肉な話です [<a href="#identifier_8_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_9_394" class="footnote">
    <a href="http://c2.com/cgi/wiki?NewConsideredHarmful">New Considered Harmful</a>という記事を読んだのもその頃です。 [<a href="#identifier_9_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_10_394" class="footnote">
    またはDIの解決しようとする問題を解決する技術 [<a href="#identifier_10_394" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: <http://yohei-y.blogspot.com/2005/04/rest_23.html>
 [2]: <http://en.wikipedia.org/wiki/Resource-oriented_architecture>
 [3]: <http://capsctrl.que.jp/kdmsnr/wiki/PofEAA/?TwoStepView>
 [4]: <http://solarphp.org/manual>:solar:dependency_injection
