---
title: Ray.Di on Aura.Di
author: admin
layout: post
permalink: /2011/10/ray-di-on-aura-di/
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2011/10/ray-di-on-aura-di/">Tweet</a>
</div>

## Aura.Di

Ray.DiはAura.Diを使用しています。[Aura][1]はPHP5.3用フレームワークで、Paul M.Jones.氏がリードのPHP5.2用フレームワークSolarPHPの現在のメジャーバージョンです。有名なフレームワークでは無いかもしれませんが、ライブラリファースト、コンパクトでクリーンなコード、100%テストカバレッジ等、リファレンスとすべき多くの点があるのではと思います。

Ray.Diは基本的にはアノテーションベースのDIコンテナですが、アノテーションを全く使わないAura.Diの上に構築されています。なのでどちらの方法でも依存性の注入を行う事ができます。前回の記事ではアノテーションを使った方法だけ紹介しましたが、この記事では両方の方法を紹介してそれぞれ比較したいと思います。

まずはそのどちらも使えるインジェクターの生成からです。

## インジェクターの生成

Containerクラスのインスタンスと、インターフェイスとクラスを紐付けるモジュールの二つを引き数に取ります。ContainerクラスにはForge、ForgeにはConfig、ConfigにはAnnotationインスタンスが必要です。

{% codeblock lang:php %}
$di = new Injector(new Container(new Forge(new Config(new Annotation))), new AppModule);
{% endcodeblock %}
あるいは、

instance.php  
{% codeblock lang:php %}
require_once  '/path/to/Ray.Di/src.php';
return new Injector(new Container(new Forge(new Config(new Annotation))), new AppModule);
{% endcodeblock %}

このようにincludeを使って

{% codeblock lang:php %}
$di = include '/path/to/scripts/instance.php';
{% endcodeblock %}

インスタンスをスクリプトから代入します。

### クリーンな依存関係

使用される全てのクラスがインターフェイスを持ち、それぞれ必要とされるクラスのコンストラクタで受け取っています。固定化されたクラス関係は存在せずクラスの依存関係はクリーンで、ユーザー作成のコンポーネントとも入れ替え可能です。DIコンテナが扱うクラスだけでなく、DIコンテナそのものも実装（実クラス）ではなく、インターフェイスでつながれています。<sup><a href="#footnote_0_950" id="identifier_0_950" class="footnote-link footnote-identifier-link" title="InjectorとAnnotation以外は全てAuraのコンポーネントです。Configだけ一部機能追加してますが他のクラスはAura.Diそのままです。">1</a></sup>

## コンストラクタ・インジェクトション

Ray.Diはコンストラクターインジェクションとセッターインジェクション（メソッドを使ったインジェクション）をサポートします。<sup><a href="#footnote_1_950" id="identifier_1_950" class="footnote-link footnote-identifier-link" title="現在プロパティインジェクションは実装されていません">2</a></sup>。3rdパーティのものや既存のライブラリ等、アノテーションが使えない場合の方法と使う方法を別にして紹介します。

### ターゲットクラス

ターゲットになるクラスです。ListerクラスのコンストラクタにFindインターフェイスを実装したインスタンス（Finder)を渡す必要があります。  
{% codeblock lang:php %}
namespace MovieApp {
    class Lister {
        public $finder;
        public function __construct(Find $finder){
            $this->finder = $finder;
        }
    }
    class Finder implements Find {}
    interface Find{}
}
{% endcodeblock %}

### アノテーションを使わないコンストラクタ・インジェクション

#### イーガーセット

{% codeblock lang:php %}
    $di = include __DIR__ . '/scripts/instance.php';
    $di->getContainer()->params['MovieApp\Lister'] = array(
       'finder' => new MovieApp\Finder
    );
    $lister = $di->getInstance('MovieApp\Lister');
{% endcodeblock %}

params[クラス名]として、ネームドパラメーター<sup><a href="#footnote_2_950" id="identifier_2_950" class="footnote-link footnote-identifier-link" title="引き数を順番ではなく変数名で指定">3</a></sup> で引き数を指定します。この準備は通常アプリケーションのブート時等に1度だけ行います。getInstance()時にはコンストラクタ引き数を指定していませんが、&#8221;予約&#8221;した方法で引き数が渡されインスタンスが生成されます。

#### レイジーセット

{% codeblock lang:php %}
    $di = include __DIR__ . '/scripts/instance.php';
    $di->getContainer()->params['MovieApp\Lister'] = array(
        'finder' => $di->getContainer()->lazyNew('MovieApp\Finder')
    );
    $lister = $di->getInstance('MovieApp\Lister');
{% endcodeblock %}
イーガーセットでは準備の段階で引き数に必要なインスタンスを生成しましたが、もしかしたら使わないかも、あるいは準備時にはまだインスタンスが確定できないものはlazyNewというメソッドを使ったレイジーセットが行えます。インスタンスの代わりにインスタンスの生成方法をセットしておいてgetInstance()時に遅延実行されコンストラクタ引き数として渡されます。引き数１つめにクラス名、２つ目に引き数をネームドパラメーターで指定します。

クラス同様、コンストラクタインジェクションの**設定も親クラスから小クラスに継承されます**。つまり、Finderクラスを継承した子クラスの取得時にも適用されます。またgetInstance()の第二引き数でインスタンス取得時に、設定した引き数を指定したパラメーターだけ上書きすることができます。<sup><a href="#footnote_3_950" id="identifier_3_950" class="footnote-link footnote-identifier-link" title="$host, $id, $passを引き数に取るようなコンストラクタでgetInstance($class, array(&lsquo;host&rsquo; => $host);と指定すると$id, $passはデフォルトの値で$hostだけを指定できます。">4</a></sup>

### アノテーションを使うコンストラクタ・インジェクション

ターゲットのメソッドに**@Inject**アノテーションでマークします。Ray.Diにインスタンスを代入しなければならない事が伝わります。  
{% codeblock lang:php %}
namespace MovieApp {
    class Lister {
        public $finder;
        /**
         * @Inject
         */
        public function __construct(Find $finder){
            $this->finder = $finder;
        }
    }
    class Finder implements Find {}
    interface Find{}
}
{% endcodeblock %}
AbstractModuleを継承したモジュールのconfigureメソッド内でインターフェイスと実クラスを指定します。AbstractModuleにはインターフェイスとクラスを結ぶ様々なメソッドがあり、英語表現のようなDSL<sup><a href="#footnote_4_950" id="identifier_4_950" class="footnote-link footnote-identifier-link" title="Guiceでこのように表現されてました">5</a></sup> でインターフェイスとクラスを紐づけます。

{% codeblock lang:php %}
    class Module extends \Ray\Di\AbstractModule
    {
        public function configure()
        {
            $this->bind('MovieApp\Find)->to('MovieApp\Finder')->in(Scope::SINGLETON);
        }
    }
{% endcodeblock %}
前回の記事ではインスタンスを直接してしましたが、この例では実クラスを指定してin()でそのクラスはシングルトンスコープで利用されるように指定しています。二回目以降の注入には同じインスタンスが再利用されます。  
{% codeblock lang:php %}
    $di->setModule(new Module);
    $lister = $di->getInstance('MovieApp\Lister');
{% endcodeblock %}
そのモジュールをセットしたインジェクターでインスタンスを取得します。

### Conclusion

アノテーションを使用しないでクラス名やメソッド名を指定してそこの何を入れるかを指定する方法と、アノテーションを使ってインジェクトするポイントを指定しインターフェイスとクラスをワイアリングする方法と、依存オブジェクトの２つの指定の方法、Ray.Diはそのどちらも可能という事を見てきました。前者はXMLやYAMLファイルなどのスタティックな設定を持つことが多く、Symfomy2やFlow3、Ding等はこの方式です。**何処で注入するかと場所に注目**して指定する方法と、**何が注入されるかに注目**する指定する方法、の２つとも言えないでしょうか。<sup><a href="#footnote_5_950" id="identifier_5_950" class="footnote-link footnote-identifier-link" title="個人的には前者はコンテナやコンパイルなど実装の都合から生まれた方法で、後者はインターフェイス指向をより意識した方法ではないかと思うのですがどうでしょうか">6</a></sup>

* サンプル <https://github.com/koriym/Ray.Di/tree/annotation/doc>

<ol class="footnotes">
  <li id="footnote_0_950" class="footnote">
    InjectorとAnnotation以外は全てAuraのコンポーネントです。Configだけ一部機能追加してますが他のクラスはAura.Diそのままです。 [<a href="#identifier_0_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_950" class="footnote">
    現在プロパティインジェクションは実装されていません [<a href="#identifier_1_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_2_950" class="footnote">
    引き数を順番ではなく変数名で指定 [<a href="#identifier_2_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_3_950" class="footnote">
    $host, $id, $passを引き数に取るようなコンストラクタでgetInstance($class, array(&#8216;host&#8217; => $host);と指定すると$id, $passはデフォルトの値で$hostだけを指定できます。 [<a href="#identifier_3_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_4_950" class="footnote">
    Guiceでこのように表現されてました [<a href="#identifier_4_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_5_950" class="footnote">
    個人的には前者はコンテナやコンパイルなど実装の都合から生まれた方法で、後者はインターフェイス指向をより意識した方法ではないかと思うのですがどうでしょうか [<a href="#identifier_5_950" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://auraphp.github.com/