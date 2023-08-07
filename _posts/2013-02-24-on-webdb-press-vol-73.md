---
title: WEB+DB PRESS Vol.73
author: admin
layout: post
permalink: /WEB-DB-PRESS-Vol73
custom_permalink:
  - WEB-DB-PRESS-Vol73
categories: blog
tags:
  - BEAR
  - PHP
---

## BEAR.SundayでRESTfulなシステム開発

WEB+DB PRESSの大人気連載「巨人の肩からPHP 先人たちに学ぶモダンプログラミング」の連載５回目にBEAR.Sundayをとりあげて頂きました。

[<img src="/images/wp-content/uploads/2013/02/734505566.jpg" alt="734505566" class="aligncenter size-full wp-image-1676" />][1] 
RESTやRESTとBEAR.Sundayの関わりに触れてから、実際のリソースやリソース実装がどのようなコードになるかを紹介してもらっています。限られたページの中でよくまとまっていると思います。執筆編集の方々、ありがとうございます。

サンプルを雑誌に掲載されているようにSandboxアプリケーションでお試ししてもらっても全然問題ないのですが、雑誌掲載後にアプリケーションのスケルトンコードをcomposer create-projectで作成する機能がつきました。apps/ディレクトリで以下のようにアプリケーションディレクトリ名を指定してインストールします。

`php composer.phar create-project -s dev --dev bear/skeleton ./MyApp`

MyAppはアプリケーション名です。インストール直後にcomposerのpost-install-scriptが実行され「フォルダ名として指定したアプリケーション名」がコード中のnamespaceなどにも適用されます。アプリケーション固有のコマンド群を持つ代わりに汎用のcomposer create-projectを使用しています。

スクリーンキャストも用意しました。ご覧ください。  


BEAR.Sundayの総合パッケージであるBEAR.PackageのインストールしてからMyAppというアプリケーションを作成してます。作成した後にコンソールでHTMLの確認、built in web serverでそのwebサイトを確認しています。

 [1]: /images/wp-content/uploads/2013/02/734505566.jpg
