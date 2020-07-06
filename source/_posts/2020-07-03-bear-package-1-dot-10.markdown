---
layout: post
title: "BEAR.Package 1.10"
date: 2020-07-03 02:10:19 +0900
comments: true
categories: 
---

BEAR.Package 1.10をリリースします。

新しいインジェクターにより開発時のパフォーマンスが大幅に改善し、コンパイラはより最適化された`autoload.php`や`preload.php`を出力します。
最新の Ray.Aop/Di/Compilerはオンデマンドコンパイルの耐障害性が向上しました。新しいスケルトンも用意しました。

## 後方互換性
 
マイナーバージョンアップなので後方互換性は維持されます。@deprecatedとマークされたクラスも引き続き使用可能で将来の廃止も予定にありません。
 
## BEAR.Injector 

インジェクターが改善され開発時に高速になりました。特に連続してインジェクターを使うテストで顕著です。

シンプルになりました。ルートオブジェクトを取得する`Bootstrap`、アプリケーションが扱う任意の依存を取得する`AppInjector`
が統合されBEAR.Injector (`BEAR\Package\Injector`)になりました。

最適化され開発時にはコンパイルを行わないRay.Di Injector、プロダクションの時には従来と同じくPHPスクリプトをコンパイルする`ScriptInjector`とコンテキストに応じたインジェクターが用意されます。
インジェクターはシングルトンで管理され、リクエストが異なる場合も再利用され高速です。開発時のパフォーマンスが大幅にアップします。

複数のアプリケーションや複数のコンテキストのインジェクターは同一メモリ空間で共存できます。"admin"アプリで保存した記事を"client"アプリで確認するテストが連続して行えます。

```php
$appInjecot = Injector::getInstance('app');
$htmlInjector =  Injector::getInstance('html-app');
```

リクエストの度にDIコンテナの再初期化を行ません。リクエストを連続して行うユニットテストのパフォーマンスが大幅に向上します。各インスタンスはシングルトン管理されるので、連続したテストでDB接続が枯渇することもありません。

従来の`Bootstrap`、`AppInjector` から新しいインジェクターに移行するガイドを用意しています。

[http://bearsunday.github.io/manuals/1.0/ja/upgrade/injector.html](http://bearsunday.github.io/manuals/1.0/ja/upgrade/injector.htm)

## コンパイラ

コンパイラが改善され、`autoload.php`、`preload`がより正確に出力されオブジェクトグラフの描画出力(`module.dot`)も行われます。

ファイルの上書きに気が付くように`(overwritten)`の表示が行われるようになりました。複数コンテキストのコンパイルを行うときにはファイルの対比が必要です。

## prodキャッシュの変更

`prod`コンテキストのキャッシュを従来の`apc + file`のチェーンキャッシュから、`PhpFileCache`に変更しました。速度はやや劣りますが、取り扱いが簡単になります
。`PhpFileCache`はファイルとして保存されるキャッシュなので`/tmp/`を消せばキャッシュを消すことができます。(`opcache.validate_timestamps`=0の場合）
一方PHPスクリプトなのでOPCodeにキャッシュされ、(`opcache.validate_timestamps`=1にした）プロダクションでファイルアクセスを無しにすることができます。

## キャッシュクリア

新しいインジェクターでは、以前までの`src`のタイムスタンプをキャッシュキーに含める方法は廃止されました。これは元々、コンパイル環境とプロダクション環境が同一なことを前提として、APCのクリアもwebサーバーの再起動無しに行う、tmp削除の権限のないユーザーなどの理由で設置されたものですが、prodキャッシュの変更により単純に開発時は`tmp/`を消去するというのがキャッシュクリアの方法になります。


## BEAR.Resource
 
 
[BEAR.Resource 1.14.3](https://github.com/bearsunday/BEAR.Resource/releases/tag/1.14.3) 以降、`ResourceObject`で`declare(strict_types=1);`を宣言してもstring以外もタイプできます。
 ```php	
 public function onGet(int $num = 0)
  {
 ```
 
## BEAR.Skeletonの変更

新しいBEAR.PakcageのInjectorに対応したスケルトンを用意しました。加えてQAやテストのテンプレートを強化しました。

### 新しいテストスイート

新しいスケルトンは従来のリソースCRUDテストのテンプレートに加えて、ハイパーメディアとHTTPテストのテンプレートを付加しています。ハイパーメディアテストにはリンクを辿ったテストを記述することでユーザーのワークフローを表すことができます。

ハイパーメディアのテストを継承したHTTPテストはハイパーメディアのテストを継承することでほとんど記述をする必要がありません。PHPビルトインサーバーがテスト実行中にのみ立ち上げられられます。そのテストの結果はログフォルダに`curl`のリクエストとレスポンスとして記録されます。この記録は実際に`HttpResourceCleint`で実行されたもので、複雑なクエリーやレスポンスの確認を仕様書以上に具体的なものとして確認することができます。クライアントサイドとサーバーサイドエンジニアのコミニケーションロスを減らす事ができるかもしれません。

### QAの変更

 * 従来のbootstrapスクリプトからBootstrapクラスに変更しました。
 * `phpmd.xml`をより厳しい値をデフォルトとして設定しました。
 * `php-cs-fixer`を外し[doctrine/conding-standard](https://github.com/doctrine/coding-standard)をベースとしてsquizlabs/php_codesniffer(phpcs)のみにしました。[PSR12](https://www.php-fig.org/psr/psr-12/)が適用されます。
 * phpstan / psalmをより厳しいデフォルトにしました。
 * [pcov](https://github.com/krakjoe/pcov)を使ったcoverageレポートを出力する`composer pcov`をcomposer.jsonに追加しました。
 * 静的解析を行う`composer qa`を追加しました。

新しいphpcsのルールは単にシンタックスだけでなく、if文内の変数アサインや、ループ中のcount実行などコーディングスタンダードが避けるべきと考えるプラクティスもエラーとしてレポートします。

## array shapes記法

Index.phpのサンプルを以下のように変更しました。

 * strict_types=1に
 * bodyの配列を型を表すarray shapes記法(Object-like arrays)に。
 * return typeを`static`に

```
<?php

declare(strict_types=1);

namespace BEAR\Skeleton\Resource\Page;

use BEAR\Resource\ResourceObject;

class Index extends ResourceObject
{
    /** @var array{greeting: string} */
    public $body;

    /** @return static */
    public function onGet(string $name = 'BEAR.Sunday')
    {
        $this->body = [
            'greeting' => 'Hello ' . $name,
        ];

        return $this;
    }
}
```

return staticはphp8で採用予定です。従来の`ResourceObject`リターンタイプより正確です。

* [https://wiki.php.net/rfc/static_return_type](https://wiki.php.net/rfc/static_return_type)

array-shapeはネイティブのPHPは理解しませんが、qaツール(phpstan/psalm)は理解し、配列キーのアクセスを検査し間違っていればエラーとして報告します。PhpStorm + [deep-assoc-completion
klesun
](https://plugins.jetbrains.com/plugin/9927-deep-assoc-completion)では配列キーの補完もされます。

既存のプロジェクトに新しい記法や新しいQA設定値を適用するかしないかは完全にユーザーの自由です。QAツールとBEAR.Packageに密結合はありません。コーディングルールだけなど、部分的に適用することもできます。
