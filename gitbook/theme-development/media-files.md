---
icon: photo-film-music
---

# メディアファイル

CS-Cartの全てのメディアファイルは下記のディレクトリに保存されています：

> design/themes/your\_theme\_name/media

メールテンプレート用には、次のディレクトリを使用します：

> design/themes/your\_theme\_name/mail/media

## ディレクトリ構造

### images

* `addons` -アドオンで使用する画像は`addons/your_addon_name/`ディレクトリを使用します。
* `lib` - サードパーティーのライブラリ (jqueryui等)によって使用する画像
* `icons` - アイコンやGIFアニメーション (読み込み中の画像やポインターなど)。
* `patterns` - ビジュアルエディタで追加された背景画像など
* `fonts` - アイコンフォント（詳細は次のパラグラフを確認してください）

## アイコンフォント

アイコンフォントはCS-Cartで広く使われています。\
すべてのシンボルでアイコンフォントが使用されています。

画像アイコンと比較するとアイコンフォントには多くの利点があります：

* アイコンフォントはすべてのデバイスの画面で読みやすく表示されます。
* アイコンは１つのフォントファイルとして管理されます。
* アイコンのサイズや色を選択することができます。
* アイコンにモダンなCSSスタイルを適応させることができます：影やトランジション、アニメーション等

CS-Cartで使用される[IcoMoon](https://icomoon.io/)というサービス利用しており、シンプルにアイコンを利用できます。

次にアイコンの追加や編集の方法を説明します。

＜画像＞

* IcoMoonのサイトへ移動してください。\
  [_IcoMoon_](https://icomoon.io/)
* 右上の赤い`IcoMoon App`ボタンをクリックして編集を開始します。
* 目的のアイコンをクリックして選択し､下の`Ganerate Font…`ボタンをクリックします。

＜画像＞

* 作成したアーカイブをダウンロードするにはその`Download`ボタンをクリックします。
* ダウンロードされたファイルを解凍し、CS-Cartをインストールしたディレクトリの次のディレクトリに移動します：`design/themes/your_theme_name/media`
* 解凍して出来た`icomoon/fonts`ディレクトリの中身を`design/themes/your_theme_name/media/fonts`ディレクトリにコピーします。
* `icomoon` ディレクトリ内の\_style.css\_を`custom_icons.less`にファイル名を変更し、`design/themes/your_theme_name/css/tygh`にコピーします。
* `styles.less`ファイル内の`@import` ディレクティブで`custon_icons.less`ファイルを指定します。

```html
@import "custom_icons.less";
```

* 必要に応じて`tygh/custom_icons.less` ファイルのフォントとクラス名へのパスを変更します。
* テキスト内でこれらのアイコンを使用するには、このタイプのhtml要素を作成する必要があります： テンプレートの目的部分に`<i class="icon-user"></i>`を記述し、`icon-user`が対応するクラスを指定します。
