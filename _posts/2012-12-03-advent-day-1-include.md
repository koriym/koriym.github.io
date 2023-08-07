---
title: 'Advent Day 1: include'
author: admin
layout: post
permalink: /adv1
custom_permalink:
  - adv1
categories: blog
tags:
  - Advent2012
  - BEAR
  - PHP
  - Aura
  - bootstrap
  - DI
  - include
  - require
---

## include

> **include**
> 
> (PHP 4, PHP 5)
> 
> include 文は指定されたファイルを読み込み、評価します。 

*&#8220;値の返し方: include に失敗したときには FALSE を返し、警告を発生させます。 成功した場合の返り値は、インクルードしたファイル側で変更していない限りは 1 です。 インクルードしたファイルの中で return を実行すれば、 そのファイルの処理をそこで止めて呼び出し元に処理を戻せます。 &#8220;*

このようにinculude/requireはクラスや関数を定義するだけでなく、そのファイルの中で生成した変数を渡す事ができます。BEAR.Sundayではこの機能を使ってインスタンスの取得を行っています。

## bootstrap時のインスタンス取得

インスタンスの取得には様々な方法があります。

*   new演算子
*   サービスクラスでインスタンス提供メソッド利用（getInstance() / factory()等）
*   Factoryクラス利用
*   オブジェクトコンテナからの取得 (SL)
*   Dependency Injectorによる注入 (DI)
*   上記includeを利用

アプリケーションのbootstrapの時にはまだSLやDIの機能の準備はできていません。アプリケーションはbootしたばかりで関心の分離もこれからです。このような場合にincludeでのインスタンス取得が有効だと考えました。

## SLやDIとの共通点

includeによるインスタンス取得は単純です。[DIP原則][1]にも従いませんが**「インスタンスの生成をクライアント、およびサービスクラスがが関与しない」**というDIがもつ機能との共通点があります。

そのインスタンスはsingletonなのかprototypeなのか、どのようなオブジェクトにより構成されてるのかといオブジェクトの構成知識はクライアントに必要ありません。インスタンス提供メソッドを用意する方法と違い構成が変わってもサービスクラスにも変更はありません。

専用Factoryクラスを用意するとクライアント／サービスクラスに構成知識が不要という点は同じですが、より簡易な記述になり利用も簡単です。

## Aura Framework

[Aura Framework][2]はそれぞれのライブラリのインスタンスはスクリプトで取得することができます。実用的であると同時にインスタンス生成方法の実例にもなっています。例[ Aura.Signal][3]、[Aura.Filter][4]

BEAR.Sundayでは開発当初から様々なフレームワークのbest practiceを取り込みたいと考えてますがinclude文によるインスタンスの取得、およびその位置の固定化(scripts/instance.php)もその一環です。

 [1]: /blog/2012/05/dip%EF%BC%9Adependency-inversion-principle/
 [2]: http://auraphp.github.com/
 [3]: https://github.com/auraphp/Aura.Signal/blob/develop/scripts/instance.php
 [4]: https://github.com/auraphp/Aura.Filter/blob/develop/scripts/instance.php
