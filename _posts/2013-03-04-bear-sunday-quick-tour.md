---
title: BEAR.Sunday Quick Tour
author: admin
layout: post
permalink: /bear-sunday-quick-tour
custom_permalink:
  - bear-sunday-quick-tour
categories: articles
tags:
  - BEAR
  - PHP
  - Install
  - php
  - QuickTour
  - スケルトン
---

簡単にBEAR.Sundayを体験できるチュートリアルを用意しました。それぞれのセクションの目安の時間も記してみました。良かったら手を動かしてお付き合いください。

## 準備

PHP 5.4がインストールされていればOKです。  
```
$ php -v
PHP 5.4.11 (cli) (built: Jan 27 2013 22:00:35)
Copyright (c) 1997-2013 The PHP Group
```

もしPHP5.4がインストールされてなくてもOSXならこれだけでインストールできます。<sup><a href="#footnote_0_1693" id="identifier_0_1693" class="footnote-link footnote-identifier-link" title="詳しくは php-osx.liip.ch を">1</a></sup>  
```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4
```

## BEAR.Sundayのインストール

<i class="icon-time"></i> 5-? min

コンソールでインストールします。

```
curl -s http://install.bear-project.net/ | sh -s ./bear
```

または（curlが無い場合など）  
```
$ php -r "eval('?>'.file_get_contents('https://getcomposer.org/installer'));"
$ php composer.phar create-project -s dev --dev bear/package ./bear
```

## アプリケーションの作成 

<i class="icon-time"></i> 1 min

Helloアプリケーションを作成します。  
```
$ cd bear
$ php bin/new_app.php Hello
```

## コンソールでのアプリケーション実行

<i class="icon-time"></i> 3 min

web.phpを使いconsoleでHTMLの出力が確認できます。GETメソッドで/（ルート）をアクセスするには以下のようにタイプします。

```
$ cd apps/Hello/public
$ php web.php get /
```

三番目の引き数でアプリケーションの実行コンテキスト（モード）を切り替える事ができます。

プロダクション  
```
php web.php get / prod
```  
{% highlight php %}
200 OK
cache-control: ["no-cache"]
date: ["Mon, 04 Mar 2013 12:29:49 GMT"]
[BODY]
< !DOCTYPE html>
<html lang="ja">

<body>
<div class="container">
<h1>Hello BEAR.Sunday</h1>
</div>
</body>
</html>
{% endhighlight %}

API  
```
php web.php get / api
```

{% highlight php %}
200 OK
content-type: ["application\/hal+json; charset=UTF-8"]
cache-control: ["no-cache"]
date: ["Mon, 04 Mar 2013 12:31:17 GMT"]
[BODY]
{
    "greeting": "Hello BEAR.Sunday",
    "_links": {
        "self": {
            "href": "page://self/index"
        }
    }
}
{% endhighlight %}

利用できないメソッドには405(Method Not Allowed)が返って来ます。  
```
php web.php delete /
```  
{% highlight php %}
405 Method Not Allowed
x-exception-class: ["BEAR\\Resource\\Exception\\MethodNotAllowed"]
x-exception-message: ["Hello\\Resource\\Page\\Index::onDelete()"]
x-exception-code-file-line: ["(405) \/Users\/akihito\/work\/bear\/vendor\/bear\/resource\/src\/BEAR\/Resource\/DevInvoker.php:59"]
x-exception-previous: ["-"]
x-exception-id: ["e500-b14ad"]
x-exception-id-file: ["\/Users\/akihito\/work\/bear\/apps\/Hello\/data\/log\/e500-b14ad.log"]
cache-control: ["no-cache"]
date: ["Mon, 04 Mar 2013 12:47:11 GMT"]
[BODY]
The requested method is not allowed for this URI.
KumaAir:public akihito$
{% endhighlight %}
404も試してみましょう。

## Webでのアプリケーション実行 (5 min)

同じweb.phpを使ってBuilt-in Web serverを立ち上げます。ポート番号は自由です。  
```
php -S localhost:8088 web.php
```

挨拶が表示されました。devモードではツールバーと共に表示されます。  
[<img src="/images/wp-content/uploads/2013/03/1582b4c4f63329511aff544dd1ebef7e.png" alt="スクリーンショット 2013-03-04 21.53.49" class="alignleft size-full wp-image-1705" />][1]<br clear="all" />

この挨拶には<b class="button">page://self/index</b>という名前(URI)がついています。このURIのついた情報は「リソース」と呼ばれます。

灰色のバックグランドで表示されてるのはこのリソースはキャッシュを利用しないことを表します<sup><a href="#footnote_1_1693" id="identifier_1_1693" class="footnote-link footnote-identifier-link" title="緑/赤=Read/Write">2</a></sup> URIの隣にツールバーが並び、情報の確認やコードの編集ができるようになっています。破線はそのリソースの表示範囲を表します。

## オンライン編集

リソースのコードとビューテンプレートはwebブラウザ上で編集することができます。  
[<img src="/images/wp-content/uploads/2013/03/fcb9351e421ea4a9831bb99e3340bc67.png" alt="スクリーンショット 2013-03-04 22.13.22" class="alignleft size-full wp-image-1714" />][2]<br clear="all" />  
保存にはショートカットキーも使えます。(⌘W / Ctl+W)

## シンタックスエラー

<i class="icon-time"></i> 2 min

ClassのCを誤って消してしまいました。シンタックスエラーになりますがエラーある状態でアプリケーションを実行するとエラーメッセージの表示されたエディターが表示されます。

[<img src="/images/wp-content/uploads/2013/03/9f9ffde325710fd6e898bf8086c17f37.png" alt="スクリーンショット 2013-03-04 22.27.37" class="alignleft size-full wp-image-1716" />][3]<br clear="all" />  
その場で修正して、「保存」「再読み込み」ですぐに復帰します。ケアレスミスによる思考の中断を最小限にします。

## クエリーを読む

<i class="icon-time"></i> 5 min

HTTPのGETメソッドではonGetメソッドがアクセスされます。  
{% highlight php %}
public function onGet($name = 'BEAR.Sunday')
{% endhighlight %}

このメソッドは 
<pre>$_GET</pre>

と同様に機能します。$_GET['name']を$nameとして受け取っています。 
<pre>?name=YourName</pre>

としてアクセスしてみたり、以下のようにyearクエリーも受け取ってみましょう

{% highlight php %}
    public function onGet($name = 'BEAR.Sunday', $year=2013)
    {
        $this['greeting'] = 'Hello ' . $name . ", It's {$year}";
        return $this;
    }
{% endhighlight %}

[<img src="/images/wp-content/uploads/2013/03/271d16f1b59a267b7e7b571c9ee77c84.png" alt="スクリーンショット 2013-03-04 22.43.18" class="alignleft size-full wp-image-1718" />][4]<br clear="all" />

*メソッドは**$_GET**クエリーと同じく名前で指定されるので（名前付き引き数＝named parameter)通常のPHPの引き数のように引き数の順番では変数名で指定されます。（順番は関係ありません）$_GETのクエリーは取得するのではなく、PHPのメソッドと融合して与えられている事にも注目してください。*

## テンプレート

<i class="icon-time"></i> 1 min

リソースクラスファイルの拡張子をtplあるいはtwigに変えたものがそのリソースの表現に用いられるビューテンプレートになります。

[<img src="/images/wp-content/uploads/2013/03/7aa52204264f594c4f4a7fdfb5fa4cca.png" alt="スクリーンショット 2013-03-04 22.55.42" class="alignleft size-full wp-image-1723" />][5]<br clear="all" />

## /dev/ツール

<i class="icon-time"></i> 3 min

URLに/dev/と入力するとデバックツールが表示されます

[<img src="/images/wp-content/uploads/2013/03/55d203fe83ff32fa33156f66650ac8e1.png" alt="スクリーンショット 2013-03-04 22.58.27" class="alignleft size-full wp-image-1724" />][6] 
### リソース一覧 

/dev/resource/ ではリソースを一覧したり新規のリソースをつくることができます。

[<img src="/images/wp-content/uploads/2013/03/e4249349b0ca4c6ad94182c21b6b6c69.png" alt="スクリーンショット 2013-03-04 22.59.31" class="alignleft size-full wp-image-1725" />][7]<br clear="all" />

つくったばかりのHelloアプリケーションは**page://self/index**というページリソースが１つ。利用可能なメソッドはGETだけという事が分かります。リソース（モデル）は、アプリケーションの核心です。アプリケーション管理者はそのアプリケーションにいくつ、どんなリソースがあるかをこのリソース画面で知る事ができます。

## What&#8217;s next ?

<i class="icon-time"></i> 1 week

[Web+DB Press記事][8]のリソースを作ってみるのはどうでしょうか。[&#8220;はじめて&#8221;シリーズ][9]はソフトウエアやインターネット技術の概念を同時に学ぶ事ができます。[ブログチュートリアル][10]は既存のFWとの実装の比較になります。[apps/Sandboxアプリケーション][11]はDB/ページングなど実用的なサンプルも含みます。特にRESTに興味のある方は定番となってる[REST Bucks][12]をBEAR.Sundayで実装した[HATEOAS実装][13]もあります。

<ol class="footnotes">
  <li id="footnote_0_1693" class="footnote">
    詳しくは php-osx.liip.ch を [<a href="#identifier_0_1693" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
  <li id="footnote_1_1693" class="footnote">
    緑/赤=Read/Write [<a href="#identifier_1_1693" class="footnote-link footnote-back-link">&#8617;</a>]
  </li>
</ol>

 [1]: /images/wp-content/uploads/2013/03/1582b4c4f63329511aff544dd1ebef7e.png
 [2]: /images/wp-content/uploads/2013/03/fcb9351e421ea4a9831bb99e3340bc67.png
 [3]: /images/wp-content/uploads/2013/03/9f9ffde325710fd6e898bf8086c17f37.png
 [4]: /images/wp-content/uploads/2013/03/271d16f1b59a267b7e7b571c9ee77c84.png
 [5]: /images/wp-content/uploads/2013/03/7aa52204264f594c4f4a7fdfb5fa4cca.png
 [6]: /images/wp-content/uploads/2013/03/55d203fe83ff32fa33156f66650ac8e1.png
 [7]: /images/wp-content/uploads/2013/03/e4249349b0ca4c6ad94182c21b6b6c69.png
 [8]: /blog/WEB-DB-PRESS-Vol73
 [9]: https://code.google.com/p/bearsunday/wiki/my_first_index
 [10]: https://code.google.com/p/bearsunday/wiki/blog
 [11]: https://github.com/koriym/BEAR.Package/tree/develop/apps/Sandbox
 [12]: http://www.infoq.com/articles/webber-rest-workflow
 [13]: https://github.com/koriym/BEAR.Package/blob/develop/apps/Sandbox/Resource/Page/Restbucks/Index.php
