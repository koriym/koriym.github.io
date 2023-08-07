---
title: timeアノテーション
author: admin
layout: post
permalink: /2013/04/time_annotation/
categories: blog
tags:
  - BEAR
  - AOP
  - DI
  - PHPメンターズ
  - ドメインクロック
  - ビルディングブロック
  - ランタイムインジェクション
---

PHPメンターズのブログで[時計オブジェクト（ドメインクロック）を導入してテスト容易性と意図性を高める][1]という記事が掲載されました。

この記事のように、現在時刻をアプリケーションでどう扱うかをBEAR.SundayのSandboxアプリケーションで見てみます。

## @Timeアノテーション

現在時刻文字列を扱いたいクラスにはpublicのtimeプロパティを追加しメソッドに**BEAR\Sunday\Annotation\Time**アノテーションを注記（アノテート）します。

{% highlight php %}
use BEAR\Sunday\Annotation\Time;
    /**
     * Current time string
     *
     * @string
     */
    public $time;
    /**
     * @Time
     */
    public function onPut($id, $title, $body)
    {
        $this->time; // 2013-04-03 19:37:40
    }
{% endhighlight %}

すると**このメソッドがコールされたタイミングで**timeプロパティに現在時刻が代入されるようになます。メソッド内ではそのプロパティを利用するだけです。

テストコードでは以下のようにpublicプロパティに値を代入します。<sup><a href="#footnote_0_1834" id="identifier_0_1834" class="footnote-link footnote-identifier-link" title="セッターメソッドを用意してもいいでしょう">1</a></sup> テストは容易に行う事ができます。

{% highlight php %}
 $user = new User;
 $user->time = "2013-04-03 19:37:40";
{% endhighlight %}

## DI ? AOP ?

働きとしてはプロパティに依存が代入される**プロパティ・インジェクション**なのですが<sup><a href="#footnote_1_1834" id="identifier_1_1834" class="footnote-link footnote-identifier-link" title="Ray.Diではプロパティ・インジェクションをサポートしていません">2</a></sup> 、BEAR.SundayではこれをRay.Aopで行っています。メソッドに束縛されたインターセプターが現在時刻をインジェクション（外部から代入）しています。

## TimeStamper

BEAR\Package\Module\Database\Dbal\DbalModuleモジュールでDoctrin DBALモジュールを利用するためにDIとAOPの設定を行っていますが、このモジュール内で@Timeとアノテートされたメソッドと現在時刻を代入するインターセプターが束縛されています。

{% highlight php %}
$this->bindInterceptor(
    $this->matcher->any(), // どのクラスでも
    $this->matcher->annotatedWith('BEAR\Sunday\Annotation\Time'), //@Timeとアノテートされてるメソッドに
    [new TimeStamper] // TimeStamperを束縛
);
{% endhighlight %}

TimeStamperは元メソッドのtimeプロパティに現在時刻をセットするだけの単純なインターセプターです。

{% highlight php %}
public function invoke(MethodInvocation $invocation)
{
    $object = $invocation->getThis(); // 元オブジェクト
    $object->time = date("Y-m-d H:i:s", time());  // 現在時刻代入
    return $invocation->proceed(); // 元メソッド実行して返す
}
{% endhighlight %}

## おわりに

この記事では現在のSandboxアプリケーションで行っている現在時刻文字列を取り扱いましたがこれを\DateTimeにすれば[時計オブジェクト（ドメインクロック）を導入してテスト容易性と意図性を高める][1]記事の中のドメインクロックと同じになると思います。

現在時刻を必要とするメソッドに@Timeと注記しその依存を利用する事でテスト容易性（testability）とコードの意図性（intentionality）が実現されています。

また時刻を用意するという関心を１つのメソッドに集約することでフォーマットの一括変更が可能になるのと同時に、その適用を任意に束縛して決定しているのでアプリケーションコンテキストやメソッド・クラス名に応じて変更する柔軟性も確保しています。

<ol class="footnotes">
  <li id="footnote_0_1834" class="footnote">
    セッターメソッドを用意してもいいでしょう [<a href="#identifier_0_1834" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_1834" class="footnote">
    Ray.Diではプロパティ・インジェクションをサポートしていません [<a href="#identifier_1_1834" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: <http://phpmentors.jp/post/46982737824>
