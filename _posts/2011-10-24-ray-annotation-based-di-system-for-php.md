---
title: 'Ray - Annotation based Dependency Injection system for PHP'
author: admin
layout: post
permalink: /2011/10/ray-annotation-based-di-system-for-php/
---

# Ray.Di

Ray.Di は DI (Dependency Injection: 依存性注入) のためのフレームワークです。[Google Guice][1]にインスパイアされ、[Aura.Di][2]を利用したPHP用DIコンテナです。メソッドインターセプターによるアスペクト指向プログラミングをサポートします。

この記事は初学者向けのDIやAOPの解説は含みませんが、１サンプルを通じてなるべく分かりやすく全体構成を説明したいと思います。

### ターゲットオブジェクト

インジェクト対象となるメソッドに**@Inject**とマークします。**@PostConstuct**はインスタンスコンストラクトされ後の初期化メソッドを表します。@Transactional, @Templateはユーザーが定義した[アスペクト指向プログラミング][3]のためのアノテーションで、**@Aspect**と共に用い、そのメソッドが[インターセプト][4]される事を指定します。

{% highlight php %}
/**
 * @Aspect
 */
class User
{
    private $db;
    /**
     * @Inject
     * @Named("pdo=pdo_user")
     */
    public function __construct(\PDO $pdo)
    {
        $this->db = $pdo;
    }
    /**
     * @PostConstruct
     */
    public function init()
    {
        // if not exist...
        $this->db->query("CREATE TABLE User (Id INTEGER PRIMARY KEY, Name TEXT, Age INTEGER)");
    }
    /**
     * @Transactional
     */
    public function createUser($name, $age)
    {
        $sth = $this->db->prepare("INSERT INTO User (Name, Age) VALUES (:name, :age)");
        $sth->execute(array(':name' => $name, ':age' => $age));
    }
    /**
     * @Template
     */
    public function readUsers()
    {
        $sth = $this->db->query("SELECT name, age FROM User");
        $result = $sth->fetchAll(\PDO::FETCH_ASSOC);
        return $result;
    }
}
{% endhighlight %}

## モジュール

モジュールではインターフェイスと実クラスやインスタンス、ファクトリを紐づけるコードを記述します。ユーザー定義の[アノテーション][5]はインターセプターと紐づけます。インターセプターはネスト可能でこの例では@TransactionalとマークされたメソッドはTimerとTransactionの機能がネストされて適用されます。

{% highlight php %}
class UserModule extends AbstractModule
{
    protected function configure()
    {
        $pdo = new \PDO('sqlite::memory:', null, null);
        $this->bind('PDO')->annotatedWith('pdo_user')->toInstance($pdo);
        $this->registerInterceptAnnotation('Transactional', array(new Timer, new Transaction));
        $this->registerInterceptAnnotation('Template', array(new Template));
    }
}
{% endhighlight %}

## インターセプター

メソッド実行に割り込み、元メソッドの前後の処理をコーディングします。このタイマーインターセプターでは タイマーのスタートとストップの間に**$invocation->proceed();**として元のメソッドが実行されています。 **array(new Timer, new Transaction)**と指定することで、タイマースタート、トランザクションスタート、元メソッド実行、トランザクションコミット、タイマーストップと処理がネストされインターセプターに挟まれたその中心で元メソッドが実行されます。

[<img alt="" src="http://www.atmarkit.co.jp/fjava/rensai3/jaee5mgrtn01/fig1.gif" title="インターセプター" class="alignnone" width="490" height="300" />][6]  
[＠IT総合トップ > ＠IT CORE > Java Solution > Java EE 5マイグレーションプラクティス（1][6]）より

[<img alt="" src="http://3.bp.blogspot.com/-aYpZ8uR26To/TZ9SqqPHzMI/AAAAAAAABOk/XQmeIefqhKU/s1600/Unity%2BInterception%2BPipeline.png" title="Unity" class="alignnone" width="700" height="255" />][7]  
[Manjesh&#8217;s Blog Aspect Oriented Programming and Unity 2.0 ][7]より

#### タイマーインターセプター

{% highlight php %}
/**
 * Timer interceptor
 */
class Timer implements MethodInterceptor
{
    public function invoke(MethodInvocation $invocation)
    {
        echo "Timer start\n";
        $mtime = microtime(true);
        $invocation->proceed();
        $time = microtime(true) - $mtime;
        echo "Timer stop:[" . sprintf('%01.7f', $time) . "] sec\n\n";
    }
}
{% endhighlight %}

#### トランザクションインターセプター

リフレクションを使い元オブジェクトのプライベートプロパティのPDOオブジェクトを操作してトランザクションを実現しています。

{% highlight php %}
/**
 * Transaction interceptor
 */
class Transaction implements MethodInterceptor
{
    public function invoke(MethodInvocation $invocation)
    {
        $object = $invocation->getThis();
        $ref = new \ReflectionProperty($object, 'db');
        $ref->setAccessible(true);
        $db = $ref->getValue($object);
        $db->beginTransaction();
        try {
            echo "begin Transaction" . json_encode($invocation->getArguments()) . "\n";
            $invocation->proceed();
            $db->commit();
            echo "commit\n";
        } catch (\Exception $e) {
            $db->roleback();
        }
    }
}
{% endhighlight %}

#### テンプレートインターセプター

連想配列をフォーマットされた文字列に変換しています。  
{% highlight php %}
/**
* Template interceptor
*/
class Template implements MethodInterceptor
{
    public function invoke(MethodInvocation $invocation)
    {
        $view = '';
        $result = $invocation->proceed();
        foreach ($result as $row) {
            $view .= "Name:{$row['Name']}\tAge:{$row['Age']}\n";
        }
        return $view;
    }
}
{% endhighlight %}

### インジェクター

インジェクターを生成し、モジュールをセットして対象のインスタンスを取得します。インスタンス取得時にオブジェクトグラフ（必要オブジェクトのリレーション）が解決され依存するオブジェクトが全て生成（またはレイジーロード可能なオブジェクトを取り出す機能のみを持ったオブジェクトプロバイダー）がセットされ対象インスタンスが生成されます。

モジュールは通常のwebアプリケーションならbootstrapで１回だけ作成します。**@Aspect**とマークされメソッドインターセプトされるオブジェクトは、**Weaveオブジェクト**というメソッドがインターセプトされ代理実行されるプロキシーオブジェクトに変わります。元のオブジェクトのメソッドを受付け、元のオブジェクトのように振る舞う代理オブジェクトです。

{% highlight php %}
$injector = include 'path/to/scripts/instance.php';
$injector->setModule(new UserModule);
$user = $injector->getInstance('Ray\Di\Sample\User');
/* @var $user \Ray\Di\Sample\User */
$user->createUser('Koriym', rand(18,35));
$user->createUser('Bear', rand(18,35));
$user->createUser('Yoshi', rand(18,35));
$users = $user->readUsers();
var_export($users);
{% endhighlight %}

#### 実行結果

`
Timer start
begin Transaction["Koriym",33]
commit
Timer stop:[0.0001919] sec
Timer start
begin Transaction["Bear",32]
commit
Timer stop:[0.0001190] sec
Timer start
begin Transaction["Yoshi",27]
commit
Timer stop:[0.0001149] sec
Name:Koriym Age:19
Name:Bear Age:28
Name:Yoshi Age:18
`

#### オリジナル実行

オリジナルのメソッドをそのまま実行した場合する場合のコードと結果です。ターゲットクラスに依存技術がなく、プレーンな形で実行とテストが可能です。  
{% highlight php %}
$pdo = new \PDO('sqlite::memory:', null, null);
$user = new \Ray\Di\Sample\User($pdo);
$user->init();
$user->createUser('Koriym', rand(18,35));
$user->createUser('Bear', rand(18,35));
$user->createUser('Yoshi', rand(18,35));
$users = $user->readUsers();
var_export($users);
array (
  0 =>
  array (
    'Name' => 'Koriym',
    'Age' => '33',
  ),
  1 =>
  array (
    'Name' => 'Bear',
    'Age' => '20',
  ),
  2 =>
  array (
    'Name' => 'Yoshi',
    'Age' => '27',
  ),
)
{% endhighlight %}

### Conclusion

依存オブジェクトが注入される元のクラスには特定のベースクラスの継承や、DIコンテナ等特定の技術に対する依存がありません。利用するオブジェクトや値は全て外部から入力されます。インターフェイスやアノテーションを、クラスやインスタンスまたはファクトトリークラスと結びつけたモジュールを用いて、インジェクターが必要とするオブジェクトを注入 <sup><a href="#footnote_0_904" id="identifier_0_904" class="footnote-link footnote-identifier-link" title="外部から代入">1</a></sup> します。

アノテーションでマークされたメソッドはインターセプトされるメソッドと解釈され、代理オブジェクトによってそのメソッドが代理実行されます。各処理を横断的に共有する関心の実行に役立つと同時に、メソッド内の処理を本質的なものと付帯的なもの、それぞれドメインロジック（ビジネスルール）、アプリケーションロジック（認証やロギング）と分離する事にも役立ちます。オブジェクト指向プログラミングの大原則に関心事の分離があるとするとアスペクト指向プログラミングはそれを補完する横断的関心事の分離に他なりません。

この記事ではRayの基本的な使用法の紹介だけにとどめ、DIやAOPの効用や用語、概念の詳しい解説は行いませんでした。またパフォーマンスやこの技術が向いている問題領域、不向きな領域、アプリケーションでの可能性や、現在ある課題にも触れていません。プレビューリリースとして基本機能を簡単にご紹介しました。

### DIに関するより良い議論

先日行われたZendConでZend FrameworkチームのエンジニアのRalph Schlenderさんがzf2のZend Diについてスライドを公開しています。zf2のDIだけでなく、特に前半DIについて語られています。素晴らしい内容で、共感する内容も多いです。紹介します。

<div style="width:425px" id="__ss_9776384">
  <strong style="display:block;margin:12px 0 4px"><a href="http://www.slideshare.net/ralphschindler/zend-di-in-zf-20" title="Zend Di in ZF 2.0" target="_blank">Zend Di in ZF 2.0</a></strong> <div style="padding:5px 0 12px">
    View more <a href="http://www.slideshare.net/" target="_blank">presentations</a> from <a href="http://www.slideshare.net/ralphschindler" target="_blank">Ralph Schindler</a>
  </div>
</div>

### Try it

ここで紹介したサンプルアプリはこの[テスト][8]で簡単に実行することができます。ご協力頂ければ大変ありがたいです。現在は簡単な英文マニュアルがあるだけですが[koriym/Aura.Di][9]で公開しています。

<ol class="footnotes">
  <li id="footnote_0_904" class="footnote">
    外部から代入 [<a href="#identifier_0_904" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: http://code.google.com/p/google-guice/
 [2]: http://auraphp.github.com/Aura.Di/
 [3]: http://ja.wikipedia.org/wiki/%E3%82%A2%E3%82%B9%E3%83%9A%E3%82%AF%E3%83%88%E6%8C%87%E5%90%91%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0
 [4]: http://codezine.jp/article/detail/3264
 [5]: http://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%8E%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3
 [6]: http://www.atmarkit.co.jp/fjava/rensai3/jaee5mgrtn01/jaee5mgrtn01_2.html
 [7]: http://manjeshgowda.blogspot.com/2011/04/aspect-oriented-programming-and-unity.html
 [8]: https://gist.github.com/1307280
 [9]: https://github.com/koriym/Ray.Di
