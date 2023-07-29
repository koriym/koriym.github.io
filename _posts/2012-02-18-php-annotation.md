---
title: PHPでアノテーション
author: admin
layout: post
permalink: /2012/02/php-annotation/
custom_permalink:
  - 2012/02/php-annotation/
categories: articles
tags:
  - 未分類
---
<div style="float: right; margin-left: 10px;">
  <a href="https://twitter.com/share" class="twitter-share-button" data-count="vertical" data-url="/blog/2012/02/php%E3%81%A7%E3%82%A2%E3%83%8E%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3/">Tweet</a>
</div>

## アノテーションとは

> プログラミングでは、コード中に登場する要素(クラス、メソッドなど)に対して、それ自体に関する情報(メタデータ)を注記できる仕組みのことをアノテーションという。「このメソッドはテスト用である」「ここでコンパイラは警告を出してはならない」「このメソッドはオーバーライドである」などの情報を付記し、コンパイル時や実行時に参照させることができる。

<p style="text-align: right;">
  <a href="http://e-words.jp/w/E382A2E3838EE38386E383BCE382B7E383A7E383B3.html">IT用語辞典</a>


このように説明されるアノテーションですが、その源流を調べてると次の文章に出会いました。

> アノテーションとは、JDK 1.5で新たに追加される言語仕様であり、Javaコード上でメタデータ（コードそのものではなくコードに関する付加情報）を記述可能にする。これは、マイクロソフトC#における属性（attribute）に相当するシンタックスで、アノテーションはそれを後追いした仕様といえる。

[2004年の@ITnoの記事][1]です。

つまりJavaのアノテーションはC#のアトリビュートに強く影響を受けたものみたいです。  
そのことについてC#の作者、[アンダース・ヘルスバーグ][2]氏のインタビューが@ITの[C#への期待。アンダースからの返答][3]という記事の中で見つかりました。

> ■Java言語の進化（例：Annotationなど）についてどのように考えているか？
> 
> アノテーション（Annotation）に関しては、.NETの属性（Attribute）のJavaバージョンといえると思うが、このように.NETで実装してきていることを、やはりJavaでも行ってきているという印象だ。実際にJavaの最新バージョン5.0に搭載された新機能の中で、（先ほどのアノテーションも含めて）.NETに触発されて導入されたと思われるものがいくつもある。

その.Netの[属性ページ][4]ではこのように説明されています。

> 属性は、プログラムにメタデータを追加します。 メタデータは、プログラム内で定義されている型に関する情報です。 すべての .NET アセンブリに、指定した一連のメタデータが含まれ、そこにはそのアセンブリ内で定義されている型および型のメンバーが記述されています。 カスタム属性を追加すると、必要な任意の追加情報を指定できます。
> 
> アセンブリ全体、モジュール全体、クラスやプロパティなどの小さいプログラム要素に、1 つ以上の属性を適用できます。
> 
> 属性は、メソッドやプロパティの場合と同じ方法で引数を受け取ることができます。
> 
> プログラムは、リフレクションを使用することにより、そのプログラム専用のメタデータや他のプログラム内のメタデータを調べることができます。

Javaの[注釈][5]ではこのように説明されています。

> 注釈はプログラムのセマンティクスに直接影響しませんが、ツールやライブラリがプログラムを扱う方法に影響します。そのため、実行中のプログラムのセマンティクスに影響する場合があります。注釈はソースファイル、クラスファイル、または実行時にリフレクションとして読み取ることができます。

## PHPでのアノテーション

PHPはアノテーションはネイティブサポートさていませんが、&#8221;Status: Under Discussion”のアノテーション提案があります。

*   [Annotations in DocBlock][6]

※[Class Metadata][7]という前の提案は否決されたようです

アノテーションの定義

```php
class ReflectionAnnotation
{
    private $value;

    public function __construct(\stdClass $value)
    {
        $this->value = $value;
    }

    public function getValue()
    {
        return $this->value;
    }
}
```

アノテーションの表記

```php
/**
 * Foo class.
 *
 * @Entity {"repositoryClass": "FooRepository"}
 * @Table  {"name": "foos"}
 *
 * @author "Guilherme Blanco"
 */
class Foo
{
  // …
}
```

アノテーションの利用

```php
$reflClass = new \ReflectionClass(‘Foo’);
var_dump($reflClass->getAnnotations());
```

PHPの言語としてのサポートにが無いのにも関わらず、現在多くのライブラリ・フレームワークでアノテーションがサポートされています。なぜアノテーションを必要と考えるのでしょうか？提案者はこのように説明しています。

> ### <a id="why_do_we_need_class_metadata" name="why_do_we_need_class_metadata">Why do we need Class Metadata?</a>


<quote>
    Frameworks in general rely on metadata information in order to correctly work. They can use it for many purposes:

        phpUnit Providing meta functionality for test cases, examples: @dataProvider for test data iteration, @expectedException for catching exceptions, etc.
        Doctrine For Object-Relational mapping, examples: @Entity, @OneToOne, @Id, etc.
        Zend Framework Server classes Used to automate mappings for XML-RPC, SOAP, etc.
        FLOW3 for dependency injection and validation
        Symfony2 for routing rules
        Others One clear thing that comes to my mind is Validation, Functional Behavior injection (which could take advantage of Traits), etc. Also, any Framework could take advantage of it somehow.

    So, any meta mapping injection could be easily achieved via the implementation of a centralized Annotations support.

    The .NET framework uses Data Annotation: http://www.asp.net/mvc/tutorials/validation-with-the-data-annotation-validators-cs

    An advantage here is the .net framework will process some annotations and inject behavior into the compiled source code.

    It's important to note that annotations exist in java and .net but many strong use cases exist in these languages to provide hints to the compiler (@NotNull).

    These types of use cases (hints to the Zend lexer/parser or other PHP implementations) are not presented in this RFC.
</quote>

他に自分が知ってる範囲では、Java Beanの影響を強く受けた[DIng][8]やRESTful PHP frameworkの[Recess][9] や[Zend Framwork2のDi][10]でもアノテーションが使われています。

ネイティブサポートがないという事はPHPでDocCommentの部分<sup><a href="#footnote_0_1148" id="identifier_0_1148" class="footnote-link footnote-identifier-link" title="リフレクションで取得できます">1</a></sup> をPHPでパースしなくてはならなく、速度的にも不利なところがあるのですが、このようにPHPでもフレームワーク・ライブラリを中心に使われるようになってきているようです。デ・ファクトと言えるようなライブラリがないのか、各ライブラリが独自でパースしてるものが多く、GuiceクローンのアノテーションベースのDIコンテナ [Ray.Di][11] を実装したときも最初はそうしていました。

## Doctrine\Commons\Annotations

ORMで有名なDoctrineですが、ORMの他にもプロジェクトがいくつか登録されていてライブラリとしてdoctrine ORM使用しているものを単体使用できるようになっています。<sup><a href="#footnote_1_1148" id="identifier_1_1148" class="footnote-link footnote-identifier-link" title=" (Zend Frameworkのように）このようにライブラリ・ファーストとして部分をライブラリとして単体使用できるのが、最新フレームワークの特徴だと思います">2</a></sup>

[Docotrine Commons][12]というライブラリがあります。

> Common
> 
> The Doctrine Common project is a library that provides extensions to core PHP functionality.

&#8220;PHPの機能を拡張するライブラリです&#8221;という説明で、Doctrineが使っているAutoloaderやCacheもあるのですが、注目は[Doctrine Annotations][13]<sup><a href="#footnote_2_1148" id="identifier_2_1148" class="footnote-link footnote-identifier-link" title=" このドキュメントは2.1のものです ">3</a></sup> です。

RFC提案されてるようにアノテーションを扱います。

アノテーションの定義

@Annotation、@Targetは Doctrineが使用するアノテーションのアノテーションです。  
このアノテーションはクラスとメソッドにアノテートすることができます。

```php
/**
 * Inject
 *
 * @Annotation
 * @Target("CLASS")
 * @Target("METHOD")
 *
 * @package    Ray.Di
 * @subpackage Annotation
 */
final class Inject implements Annotation
{
}
```

setDbメソッドを@Injectとアノテートしました。

```php
    /**
     * @Inject
     *
     * @param DbInterface $db
     */
    public function setDb(DbInterface $db)
    {
        $this->db = $db;
    }
```

アノテーションの利用

```php
$reflMethod = new ReflectionMethod(‘MyCompany\Entity\User’, ‘setDb’);
$methodAnnotations = $reader->getMethodAnnotations($reflMethod);
```

これで全てのアノテーションが取得できます。  
あるいは以下のようにして特定アノテーションの値が取得できます。

```php
$methodAnnotation = $reader->getMethodAnnotation($reflMethod, $annotation);
```

Doctrine\Common\Annotations\AnnotationReader が標準的なアノテーションリーダーです。 (Symfony2では標準でサービスコンテナに登録されてるようです)。
他にはそのAnnotationReaderを利用するCachedReader、use文による名前解決を行わない、より簡素な SimpleAnnotationReaderがあります。

またReaderを使わずパースライブラリとして使いたいなら、doc内のアノテーションを行うDoctrine\Common\Annotations\DocParser や PHPスクリプトを解析してのuse文の名前解決を行う Doctrine\Common\Annotations\PhpParser も有用だと思います。use文の名前解決については Symfony2のブログ [Symfony2: アノテーションが改善されました][14]も参考になると思います。<sup><a href="#footnote_3_1148" id="identifier_3_1148" class="footnote-link footnote-identifier-link" title=" Fabienさんの記事で、@masakielasticさん翻訳の記事です ">4</a></sup> 

※他には[addendum][15] , [php-annotations][16]というライブラリもあります。

## Conclusion

PHPではアノテーションのネイティブサポートはなく、以前は使用はあまり一般的ではありませんでした。しかしPHPUnitでもすっかりおなじみのようにメタプログラミングを実現するためのツールとしてPHP界でも認知されつつあります。自作のライブラリやアプリケーションでも Doctrine Annoattion を用いれば利用の敷居は下がります。速度的な懸念も CachedReaderを使用したり、Configrationに用いたりすることで問題にならない場合も多いでしょう。

ではどのように使うのが良いのでしょうか？広く使われてる割にはなかなかベストプラクティス系の記事が見つかりませんが、１つ見つけました。興味ある方は一読すれば参考になると思います。

[Annotations Gotchas and Best Practices  
][17]

Ray.DiでもこのDoctrine Annotationを採用しリファクタリングを行いました。

<ol class="footnotes">
<li id="footnote_0_1148" class="footnote">
リフレクションで取得できます [<a href="#identifier_0_1148" class="footnote-link footnote-back-link">&#8617;</a>]
</li>
<li id="footnote_1_1148" class="footnote">
(Zend Frameworkのように）このようにライブラリ・ファーストとして部分をライブラリとして単体使用できるのが、最新フレームワークの特徴だと思います [<a href="#identifier_1_1148" class="footnote-link footnote-back-link">&#8617;</a>]
</li>
<li id="footnote_2_1148" class="footnote">
このドキュメントは2.1のものです [<a href="#identifier_2_1148" class="footnote-link footnote-back-link">&#8617;</a>]
</li>
<li id="footnote_3_1148" class="footnote">
Fabienさんの記事で、@masakielasticさん翻訳の記事です [<a href="#identifier_3_1148" class="footnote-link footnote-back-link">&#8617;</a>]
</li>
</ol>

 [1]: http://www.atmarkit.co.jp/fjava/kaisetsu/j2eewatch02/j2eewatch02.html
 [2]: http://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%B3%E3%83%80%E3%83%BC%E3%82%B9%E3%83%BB%E3%83%98%E3%83%AB%E3%82%B9%E3%83%90%E3%83%BC%E3%82%B0
 [3]: http://www.atmarkit.co.jp/fdotnet/insiderseye/20060215cscommunity/cscommunity_01.html
 [4]: http://msdn.microsoft.com/ja-jp/library/z0w1kczw.aspx
 [5]: http://java.sun.com/j2se/1.5.0/ja/docs/ja/guide/language/annotations.html
 [6]: https://wiki.php.net/rfc/annotations-in-docblock
 [7]: https://wiki.php.net/rfc/annotations
 [8]: http://marcelog.github.com/Ding/
 [9]: http://www.recessframework.org/
 [10]: https://github.com/ralphschindler/zf2-di-use-cases/blob/master/09-runtime-setter-injection-with-annotation.php
 [11]: https://github.com/koriym/Ray.Di
 [12]: http://www.doctrine-project.org/projects/common
 [13]: http://docs.doctrine-project.org/projects/doctrine-common/en/latest/reference/annotations.html
 [14]: http://www.symfony.gr.jp/blog/20110523-symfony2-annotations-gets-better
 [15]: http://code.google.com/p/addendum/
 [16]: http://code.google.com/p/php-annotations/
 [17]: http://willcode4beer.com/design.jsp?set=annotations_gotchas_best_practices
