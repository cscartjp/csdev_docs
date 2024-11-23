---
description: CS-Cartで利用されているCSS関連技術の概要
icon: css3-alt
---

# CSS

## LESS

＜画像＞

CS-Cartのレイアウトスタイルは\_LESS\_を用いて書かれています。\
LESSは、非常に便利なCSSプリプロセッサです。

LESSについての詳しい情報は以下のサイトで読むことができます：

[http://lesscss.org/features/](http://lesscss.org/features/) （英語）\
[http://less-ja.studiomohawk.com/](http://less-ja.studiomohawk.com/) （日本語）

CS-Cartでは、すべてのLESSの機能が使用出来るわけではありません。\
どのような機能が使われているのかを理由とあわせて説明します。

## Variables（変数）

変数を作成し、複数の場所で同じプロパティ値を使用することが出来ます。

`例：`

```less
@price: #343434;
```

CS-CartのLESS変数はテーマの`スタイル`コンセプトに関連しています。( [Styles and Visual Editor](doc:styles-and-visual-editor) の項目を参照してください。)

CS-Cartがインストールされているディレクトリ以下の\_design/themes/THEME\_NAME/styles/data\_ディレクトリにビジュアルエディタと関連のあるLESS 変数が含まれた .lessファイルがあります。

これらの値はファイルを直接編集するか、ビジュアル·エディタで編集することができます。

このディレクトリの\_.less\_ファイルの変数値はこのファイルでのみ編集可能です。

例えば、`styles.less`ファイルで定義された`@price` 変数は作動しません。\
なぜなら、_.../styles/data/.less_ ファイルに書かれた変数の値は、一番最後に読み込まれるためです。

## Mixins（ミックスイン）

LESSにはMixinという複数のプロパティをまとめる機能があります。ミックスインはただのプロパティ値のひとつとしてクラス名を含む他のクラス内の全てのクラスのプロパティを含むことができます。

ミックスインは関数のように動作し、次の例のように引数を取ることができます:

```less
rounded-corners (@radius: 5px) {
    border-radius: @radius;
    -webkit-border-radius: @radius;
    -moz-border-radius: @radius;
}

#header {
    .rounded-corners{
      .rounded-corners(10px);
    }
    #footer {
      .rounded-corners(10px);
    }
}
```

Bootstrap2.3のミックスインのセットがCS-Cartでは使用されています。

`design/themes/responsive/css/tygh`ディレクトリのファイルにある`mixins.less`ファイルに保存されています。

このファイルには、Utility mixinsとComponent mixinsがあります。

* `Utility mixins` - 特定の使用目的のためのシンプルなミックスイン
* `Component mixins` - グリッドを形成するために使用される複雑なミックスイン

ミックスインはCSSルールの記述を少なくすることが出来ます。ブラウザのベンダープレフィックスを用いるCSSプロパティのために使用されています。

## Operations（演算）

この機能により、CSSルール内の数学的演算が可能です：\
たし算、引き算、かけ算、割り算

例：

```less
width: (900 / @columns)px;
```

## Functions（関数）

LESSは、カラーコード、数学関数、関数を操作するための一連の関数が含まれています。

CS-Cartで使用されている操作：

* `lighten(@color, 10%)` - 現在の色よりも10％明るい色を返します。
* `darken(@color, 10%)` - 現在の色よりも10％暗い色を返します。

これらの操作は、スタイルとビジュアル·エディタに関連しています。

一つのベース色を基本色を明るくしたり、暗くしたりすることによって、他の要素の色を変更することができます。

* `percentage(0.5)` - パーセント値に変換します。グリッドと列の作成中に使用されます。

## Nested rules（入れ子）

「入れ子」は別の一つのCSSルールを囲むことが可能です。

ブラウザの開発ツールでの作業を複雑にしたのでCS-Cartでは、\
ネストされたルールはほとんど使用されていません。

開発者ツールからコピーされネストされたルールを探す時、親ルールを考慮する必要があります。

その他のLESSの可能性については公式サイトで見ることができます：[http://lesscss.org/features/](http://lesscss.org/features/)

CS-CartではLESSのすべてが動作するわけではないことに注意してください。

## CSSディレクトリ構造

利用中のテーマのCSSディレクトリへのパスは\_design/themes/THEME\_NAME\_です。

* `css/addons directory` - アドオンで追加されるスタイル
* `css/lib directory` - 他のライブラリのスタイル。Jqueryライブラリのスタイルがあります。
* `css/typh directory` - コア・スタイル
  * `css/typh/reset.less` - スタイルのリセット。`Normalize.css`が使用されています。
  * `css/typh/print.less` - 印刷用のスタイル
  * `ss/flags.less` - 国旗用スタイル
  * `css/design_mode.less` - テンプレートやデザイン・モードで使用されているスタイル
  * `css/theme_editor.less` - ビジュアルエディタで使用されるスタイル
  * `css/grid.css` - 固定レイアウトのためのグリッドを形成するために使用される言語変数やミックスイン
  * `css/mixins.less` - LESSミックスインのセット

　

### レスポンシブレイアウトに使用されるスタイル：

* `css/responsive-utilities.less` - 表示または異なるデバイスの画面解像度のコンテンツを非表示にするために使用されているBootstrapクラス。
* `css/responsive.less` - 異なる画面解像度のために使用されるスタイル。

主なファイルは\_styles.less\_です。テーマのスタイルが含まれています。_css/typh_ に格納されたファイルは、`@import`オペレーターと\_styles.less\_ に含まれています。

## CSSやLESSファイルの継承順

1. `design/themes/basic/templates/common/styles.tpl`
2. `<style></style>`タグで`design/themes/basic/templates/common/styles.tpl`テンプレートに追加されたすべてのインラインスタイル。
3. プリセットされたlessファイルが含まれています。
4. ビジュアル·エディタの`カスタムCSS`フィールドに追加されたもの。
5. アドオンのスタイルはプライオリティによって定義された順序で処理されます。

HTTPリクエスト数を減らすためにすべてのファイルが１つのcssファイル集約されます。

不便な点があるといえば、そのスタイルが書かれているファイルそのものを検索することです。：ファイルとdevツールを使用して検索するときに、各ファイルの行を検索することは出来ません。
