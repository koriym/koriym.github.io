---
title: 'Ray.Tutorial &#8211; introduction'
author: admin
layout: post
permalink: /ray-tutorial-introduction
custom_permalink:
  - ray-tutorial-introduction
categories:
  - BEAR
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/ray-tutorial-introduction">Tweet</a>
</div>

# Introduction

BEAR.SundayのDIとAOP(Ray.Di)を理解するためのチュートリアルです。

最初に題材としてTodoクラスを作りました。$todo文字列を受け取ってデータベースに格納するだけのクラスです。

{% codeblock lang:php %}
<?php
class Todo
{
    /**
     * @param $todo
     */
    public function add($todo)
    {
        $pdo = new PDO('mysql:dbname=test;host=localhost');
        $stmt = $pdo->prepare('INSERT INTO TODO (todo) VALUES (:todo)');
        $stmt->bindParam(':todo', $todo);
        $stmt->execute();
    }
}
$todo = new Todo;
$todo->add('Pay bills');
{% endcodeblock %}

## システムの可変点

このプログラムはちゃんと動きますが、再利用性はどうでしょうか？  
データベースの接続情報がプログラムに直接記述、**ハードコーディング**されてあるのは問題です。

他の部分は運用環境に変更があっても変わりませんが、DBの接続情報は変わります。  
このようにプログラムには変更の可能性が高い場所とそうでも無い場所があります。

### 定数を使う

初期のシステムではこのようなシステムで変更部分のある情報を定数を使う事で解決していました。プログラムの初期化(bootstrap)ではdefineが並んだファイルを読み込みます。

{% codeblock lang:php %}
define('PDO_DSN', 'mysql:dbname=test;host=localhost');
{% endcodeblock %}

利用部分ではその情報を使います。

{% codeblock lang:php %}
$pdo = new PDO(PDO_DSN);
{% endcodeblock %}

ハードコーディングされていた箇所は取り除かれ、コードはよりクリーンになりました！

定数ファイルをみると、そのシステムでの変更部分が集約されていて変更可能な箇所を一覧することもできます。可変点は集約され、DBの接続情報に変更があっても利用コード全体を調べる必要がなくなりました。

しかしdefineはスカラー値（float、string、boolean）しか定義できません。また **グローバル**定数なのでシステムのどの部分からもアクセスができます。

### Configureクラスを使う

設定値をより柔軟に取り扱うためにConfigureクラスの導入を考えてみます。

Configureクラスは設定値の入れ物（コンテナ）を用意します。bootstrapでプログラムに必要な設定情報を設定ファイル(ini/yaml/php配列)を読み込んだりコードで直接代入したりして準備しておきます。

{% codeblock lang:php %}
$connection = Configure::read('pdo');
$pdo = new PDO($connection['dsn'], $connection['user'], $connection['password']);
{% endcodeblock %}

利用するときにはそのConfigureクラスとセットに使ったキーを使ってその値を取り出します。これで設定に配列も扱えるようになりました。設定の代入も多様な方法で行えます。

しかし一方で、このアプリケーションは依然として**コード中のどこからでも同一の値にアクセスできるグローバルスコープの設定値**を持っています。&#8221;コントローラだろうがモデルだろうがビューだろうがアプリケーション内のおおよそ全ての場所&#8221;から利用可能です。

### グローバル変数$_GLOBALSを使う

{% codeblock lang:php %}
$connection = $_GLOBAL['MYAPP']['pdo'];
$pdo = new PDO($connection['dsn'], $connection['user'], $connection['password']);
{% endcodeblock %}

グローバル変数に抵抗がありますか？ グローバルスコープでどこからも参照できる変数という意味では、グローバル変数もConfigureクラスもあまり変わりません。実際CakePHPではこのような注意書きがあります。

> 何でも保存でき、コード内のあらゆる場所で使用できるので、CakePHPのMVCパターンを崩してしまう誘惑には注意しましょう。 

グローバルスコープの変数、特にごく単純なものなら$_GLOBALSを使うのは自然です。ただ競合しないようにpresudo-namespace（prefixを使ったなんちゃって名前空間）を使うのがいいと思います。PEARでも使用例がいくつもあります。

しかし、defineもグローバル変数もConfigure専用クラスもグローバルスコープでどこでも参照できる点には代わりがありません。

### BEARでは

前のバージョンのBEAR.Satudayではグローバルdefineが２つ（時間とアプリケーションパス）ありましたがBEAR.Sundayではありません。またConfigureクラスのようなどのクラスからも参照できるグローバルスコープの設定値専用の変数コンテナはありません。

## インスタンスの管理を考える

次にインスタンスの管理を考えてみます。本来PDOオブジェクトはメソッド内で毎回newして新しいインスタンスを作る必要はありません。一度生成すればそのオブジェクトを再利用したいところです。

そこで、メソッドの生成・管理をメソッドに任せる事にします。「シングルトン」です。

{% codeblock lang:php %}
    private $instance;

    public static function getInstance()
    {
        if (is_null(self::$instance)) {
            self::$instance = new self;
        }
        return self::$instance;
    }
{% endcodeblock %}

このようメソッドを各クラスに持って以下のように取得します。

{% codeblock lang:php %}
$pdo = Db::getInstance();
{% endcodeblock %}
newでインスタンス生成が行われるのは一度だけで、次回以降は生成済みのインスタンスが渡されるだけです。

しかし、このようなシングルトンのコードはテストに向かない保守性の低いコードになってしまいます。**コード中のどこからでも同一のインスタンスにアクセスするグローバルスコープのオブジェクト**になっているからです。

オブジェクトの生成・管理がまとまった仕事であるなら、専用のクラスを持つのは自然な話です。<sup><a href="#footnote_0_2022" id="identifier_0_2022" class="footnote-link footnote-identifier-link" title="BEAR.Saturdayでは BEAR::Dependency">1</a></sup>  
例えばその専用クラスは以下のように使われます。

{% codeblock lang:php %}
// Global registry
$pdo = ServiceContainer::get('pdo');
...
// Contextual dependency lookup (CDL)
$pdo = $this->app['pdo'];
{% endcodeblock %}

bootstrapでは何らかの方法でオブジェクトの生成の準備を完了させておき、取り出し&#8217;キー&#8217;と共にオブジェクトが取り出せる準備をしておきます。

利用する方は、これがシングルトンで渡されるかどうかを指定しません。またコンストラクタに初期値も渡しません。利用側ではオブジェクトをどう生成するかに関心を持たずに単に取り出し用のキー名指定するだけで利用できます。

#### pros

ここでは利用だけに注目しましょう。オブジェクトの生成方法ががどんなに複雑になっても、インスタンスの管理方法が変わっても、取得の方法に変化がありません。これを利用するクラスは保守性の高いコードになりやすいでしょう。

#### cons

一方、このコードだけを見ても$pdo変数は何のオブジェクトで何ができるのが分かりません。ServiceContainer::getのphpdocの@returnを見ても分かりません。ServiceContainerクラスの働きを理解して、何がどう&#8217;pdo&#8217;にセットされているか、コードかドキュメントから知る必要があります。Todoクラスの実行にはServiceContainerクラスが必要になりました。ユニットテストの時もServiceContainerクラスが必要です。クラス間の依存を減らす為に一つ依存が増えました。

## 依存性の注入

これまで、オブジェクトをどうやって作り、どうやって管理するか、というオブジェクトの生成と管理の視点でコードを見て来ました。様々なやり方を検討してきましが、いずれの方法も **オブジェクトを生成するか、または他のクラスを使って取得**していました。(Dependency Lookup) これから見るのは依存性の注入と呼ばれるパターンで、依存オブジェクトの取得は完全に受け身になります。

{% codeblock lang:php %}
<?php
class Todo
{
    /**
     * @var PDO
     */
    private $pdo;

    /**
     * @param PDO $pdo
     */
    public function __construct(PDO $pdo)
    {
        $this->pdo = $pdo;
    }

    /**
     * @param string $todo things to do
     */
    public function add($todo)
    {
        $stmt = $this->pdo->prepare('INSERT INTO TODO (todo) VALUES (:todo)');
        $stmt->bindParam(':todo', $todo);
        $stmt->execute();
    }
}
$pdo = new PDO('mysql:dbname=test;host=localhost');
$todo = new Todo($pdo);
$todo->add('Get laundry');
{% endcodeblock %}

内部で必要なオブジェクトを**ハードコード**して生成/取得するのではなくて、クラスの外から依存が代入されています。**DBオブジェクトがDBの接続情報文字列を可変点と考えたように、DBオブジェクト利用クラスにとってDBオブジェクトが可変点**と考えます。

これが依存性の注入(dependency injection=DI)です。

「利用するインスタンスを外部から渡す」- DIの本質的なところはこれだけです！

ファウラーの「[Inversion of Control コンテナと Dependency Injection パターン][1]」を読んだ人はえ？っと思うのではないでしょうか。<sup><a href="#footnote_1_2022" id="identifier_1_2022" class="footnote-link footnote-identifier-link" title="かつての自分です">2</a></sup>

それを揶揄した記事もあります。

> [&#8220;Dependency Injection&#8221; is a 25-dollar term for a 5-cent concept.][2]  
> Dependency injection means giving an object its instance variables. Really. That&#8217;s it.

依存性の注入はwikiではこのように説明されています。

> 依存性の注入（いぞんせいのちゅうにゅう、英: dependency injection）とは、コンポーネント間の依存関係をプログラムのソースコードから排除し、外部の設定ファイルなどで注入できるようにするソフトウェアパターンである。
> 
> 依存性の注入を利用したプログラムを作成する場合、コンポーネント間の関係はインターフェースを用いて記述し、具体的なコンポーネントを指定しない。具体的にどのコンポーネントを利用するかは別のコンポーネントや外部ファイル等を利用することで、コンポーネント間の依存関係を薄くすることができる。 

このwikiの説明はパターンの説明というよりもその実際の説明により過ぎてるように思います。英語版はもっと明快です。

> Dependency injection is a software design pattern that allows the removal of hard-coded dependencies and makes it possible to change them, whether at run-time or compile-time.[1] 

> 依存性の注入とはランタイムやコンパイルタイムでハードコードされた依存を取り除き変更可能にするためのソフトウエアデザインパターンの一つ

上記のサンプルは設定ファイルもインターフェイスも出て来ませんが、DIを適用したコードです。英語版wikiの説明のよう**ハードコードされた依存は取り除かれ、変更可能**になっています。

### 再びシングルトン

同じオブジェクトを再利用するシングルトンもやってみましょう。

{% codeblock lang:php %}
$pdo = new PDO('mysql:dbname=test;host=localhost');
$todo1 = new Todo($pdo);
$todo2 = new Todo($pdo);
...
{% endcodeblock %}

同じオブジェクトを渡す事で、それぞれ別の利用クラスが同じ依存インスタンス($pdo)を使っています。依存クラスは利用クラスの外側で集中して管理されていて、PDOインスタンスの生成は一度だけです！

## 問題を違う場所に移しただけ？

&#8230;と、ここまで見て、確かにTodoクラスから依存が取り除かれコードはすっきりしました。テストもより簡単になったでしょう。

その代わり依存のややここしいところはオブジェクトの生成部分に依然あるし、設定値もハードコーディングされています。オブジェクトの利用から問題を取り除いた代わりに、オブジェクトの生成の部分が問題になったように見えないでしょうか。つまり依存の問題を解決したというより問題をある場所から違う場所に移しただけのように見えないでしょうか。

これらをRay.Di DI frameworkではどういう風に解決してるか、次回から見て行きます。

&#8230;続く

<ol class="footnotes">
  <li id="footnote_0_2022" class="footnote">
    BEAR.Saturdayでは BEAR::Dependency [<a href="#identifier_0_2022" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_2022" class="footnote">
    かつての自分です [<a href="#identifier_1_2022" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://kakutani.com/trans/fowler/injection.html
 [2]: http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html