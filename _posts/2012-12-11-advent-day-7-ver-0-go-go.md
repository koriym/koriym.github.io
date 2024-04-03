---
title: 'Advent Day 7: ver. 0.Go.Go'
author: admin
layout: post
permalink: /adv7
custom_permalink:
  - adv7
categories: ["blog"]
tags:
  - Advent2012
  - BEAR
  - フレームワーク
---

## Day 7

Day 7、７日目の休息ということでDI記事お休みして <a href="https://travis-ci.org/koriym/BEAR.Package/builds/3592222" target="_blank">BEAR.Sudnayの0.5.5リリース</a>の案内です。

## BEAR.Sunday version 0.5.5

現在のBEAR.Sundayアプリケーションは三層の構造になっています。

*   Application アプリケーション
*   BEAR.Package アプリケーションパッケージ
*   BEAR.Sunday リソースフレームワーク

#### BEAR.Sunday

Ray.Di/Aopという生成と振る舞いに制約と機能をもたらすオブジェクトフレームワークに、リソース制約を加えリソースフレームワークとしたものです。

#### BEAR.Package

必要なテンプレートエンジンやDBライブラリ等の実装選択等の実装選択と、フレームワークが持つ抽象の束縛の集合です。BEAR.Sundayは（コンポーネントの一貫性のプライオリティを下げてでも）多様なライブラリ選択を好みます。アプリケーションアーキテクトはスタッフやプロジェクトを勘案して、アプリケーションパッケージの構成を柔軟に構成することができます。複数のアプリケーションが共有する、チームのソフトウエア基盤です。アプリケーションのコンパイルタイムで必要な処理の多くはこのレイヤーが担当します。

#### Application アプリケーション

アプリケーションのランタイムコードの記述が中心になります。例えばDBオブジェクトがどのように生成されたかに関与はありません。セットされたDBを使って本質的関心事ではあるビジネスロジックを実現するコードを記述することが中心になります。

ここ最近の作業の中心はBEAR.Packageの分離とBEAR.Packageでのアプリケーションのより良い構成を持つ事でした。

## 1.0.0 ?

BEAR.Package以外の更新を最小化と、アプリケーション作成を通じてBEAR.Packageを熟成させ1.0.0リリースに繋げたいと思います。  
当初予定してた今年中のリリースは難しいですが、来年明けの早い段階でと考えてます。

次の[Symfony勉強会 #7 (BEAR.Sundayワークショップ＆忘年会) 2012/12/15(Sun)][1]ではこの0.5.5を使います。よろしく御願いします。

 [1]: http://atnd.org/events/34068
