---
icon: globe
---

# .poファイルによる言語ファイルの作成

## .poファイルとは？

CS-Cartの言語ファイルは多言語対応を意識してデータベースに保存されています。4.2.4以前のバージョンのCS-Cartでは、addon.xmlにそれぞれの言語変数を用意してインストールしていましたが、新しいCS-Cartでは、[WordPress](https://ja.wordpress.org/) も利用している`gettext` による多言語化を採用しています。

これは各言語毎に.poファイルを用意することでアドオンのインストール時に言語変数がインストールされる仕組みです。[gettextについて詳しく](http://ja.wikipedia.org/wiki/Gettext) （Wikipedia）

> 📘 ヒント
>
> WordPressでは.poファイルを編集し、最終的に .poファイルは msgfmtコマンドにより バイナリファイルにコンパイルされた.moファイルを利用しますが、CS-Cartは.poファイルを使います。

## .poファイル編集ツール

.poファイルを編集するにはテキストエディターでも可能ですが、[Poedit](http://sourceforge.jp/projects/sfnet\_poedit/)というツールも無料で利用可能です。

＜画像＞

## .poファイルの保存場所

翻訳を作成した.poファイルは以下のディレクトリに配置します。

```
/var/langs/[言語コード]/addons/[アドオンID].po
```

* `言語コード` 英語はen、日本語はjaのような[ISO 639-1](http://ja.wikipedia.org/wiki/ISO\_639-1%E3%82%B3%E3%83%BC%E3%83%89%E4%B8%80%E8%A6%A7) の言語コードとなります。
* `アドオンID`　アドオンのaddon.xmlで定義した識別子です。\
  例）カタログモード アドオン・・・catalog\_mode

## .poファイルの構造

こちらに日本語版アドオンの.poファイルを掲載しています。\
[ダウンロード](https://gist.github.com/pierrefrogman/09bc75eab721c6a5c635)

```c
msgid ""
msgstr ""
"Project-Id-Version: cs-cart-latest\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Language-Team: Japanese\n"
"Language: ja_JP\n"
"Plural-Forms: nplurals=1; plural=0;\n"
"X-Generator: crowdin.net\n"
"Last-Translator: cscartjp <info@cs-cart.jp>\n"
"PO-Revision-Date: 2014-07-17 04:42-0400\n"

msgctxt "Addons::name::localization_jp"
msgid "日本語版アドオン"
msgstr "日本語版アドオン"

msgctxt "Addons::description::localization_jp"
msgid "CS-Cartを日本でのECサイト運用に最適化します"
msgstr "CS-Cartを日本でのECサイト運用に最適化します"

msgctxt "SettingsSections::localization_jp::general"
msgid "General"
msgstr "基本設定"

msgctxt "SettingsOptions::localization_jp::elm_contact_information"
msgid "連絡先情報"
msgstr "連絡先情報"

msgctxt "SettingsOptions::localization_jp::jp_familyname_kana_c"
msgid "「姓フリガナ」フィールド"
msgstr "「姓フリガナ」フィールド"

msgctxt "SettingsOptions::localization_jp::jp_firstname_kana_c"
msgid "「名フリガナ」フィールド"
msgstr "「名フリガナ」フィールド"

msgctxt "SettingsOptions::localization_jp::elm_billing_information"
msgid "請求先情報"
msgstr "請求先情報"

msgctxt "SettingsOptions::localization_jp::jp_familyname_kana_b"
msgid "「姓フリガナ」フィールド"
msgstr "「姓フリガナ」フィールド"

msgctxt "SettingsOptions::localization_jp::jp_firstname_kana_b"
msgid "「名フリガナ」フィールド"
msgstr "「名フリガナ」フィールド"

  ..........
```

## .poファイルの構造

### ヘッダー部分

以下は英語の.poファイルです。\
日本語の場合は "Language: ja\_JP\n" となります。ファイルはUTF-8で作成します。

```c
msgid ""
msgstr "Project-Id-Version: tygh"
"Content-Type: text/plain; charset=UTF-8\n"
"Language-Team: English\n"
"Language: en_US"
```

### 言語変数の記述方法

* `msgctxt` 言語の使用場所
* `msgid` 言語の識別子
* `msgstr` 翻訳

### msgctxt 言語変数の使用箇所

* `Addons::name::[アドオンの識別子]` - アドオン名を翻訳
* `Addons::description::[アドオンの識別子]` - アドオンの説明文を翻訳

例）「高解像度ディスプレイ対応」 アドオン

```c
msgctxt "Addons::name::hidpi"
msgid "HiDPI displays support"
msgstr "高解像度ディスプレイ対応"

msgctxt "Addons::description::hidpi"
msgid "Support for Retina displays"
msgstr "Retinaディスプレイに最適化した画像を表示します"

msgctxt "Languages::text_hidpi_support_enabled"
msgid "HiDPI support is enabled"
msgstr "高解像度ディスプレイ対応が有効化されています"
```

### アドオンの設定関連

* `SettingsSections::[アドオンの識別子]\::[設定セクション名]` - アドオンの設定セクション名
  * `SettingsOptions::[アドオンの識別子]\::[設定セクション名]` - アドオンの設定オプション（変数）名
  * `SettingsVariants::[アドオンの識別子]\::[設定セクション名]\::[設定値]` - アドオンの設定値

例）「ポイント」アドオン設定用言語ファイル

```c
msgctxt "SettingsSections::reward_points::general"
msgid "General"
msgstr "基本設定"

msgctxt "SettingsOptions::reward_points::point_rate"
msgid "Points Exchange Rate (PER - the number of points equal to 1 conventional unit)"
msgstr "ポイント交換レート (PER - 基本通貨1単位に相当するポイント数)"

...........
  
msgctxt "SettingsOptions::reward_points::several_points_action"
msgid "If several reward points can be applied, set"
msgstr "複数種のポイントが適用されうる際の適用ポイント数"

...........
  
msgctxt "SettingsVariants::reward_points::several_points_action::minimal_absolute"
msgid "Absolute minimal value"
msgstr "最小値（絶対値）"

msgctxt "SettingsVariants::reward_points::several_points_action::minimal_percentage"
msgid "Minimal percentage value"
msgstr "最小値（％）"

msgctxt "SettingsVariants::reward_points::several_points_action::maximal_absolute"
msgid "Absolute maximal value"
msgstr "最大値（絶対値）"

msgctxt "SettingsVariants::reward_points::several_points_action::maximal_percentage"
msgid "Maximal percentage value"
msgstr "最大値（％）"
```

＜画像＞

### アドオンで使用する言語変数

* `Languages::[言語変数名]`

例）「タグ」アドオンが使用する言語変数（テンプレートなどで利用する）

```c
msgctxt "Languages::popular_tags"
msgid "Popular tags"
msgstr "人気のタグ"

msgctxt "Languages::sign_in_to_enter_tags"
msgid "Sign in to enter tags"
msgstr "ログインしてタグを登録"

msgctxt "Languages::tag_cloud"
msgid "Tag cloud"
msgstr "タグクラウド"

msgctxt "Languages::my_tags"
msgid "My tags"
msgstr "マイタグ"
```

## .tplファイルでの利用例

> /design/themes/responsive/templates/addons/tags/views/tags/components/tags.tpl

```html
<a href="{$config.current_url|fn_url}">{__("sign_in_to_enter_tags")}</a>
```

## コントローラーでの利用例

> /app/addons/tags/controllers/frontend/tags.php

```php
<?php
...
if (empty($_REQUEST['see']) || $_REQUEST['see'] != 'my') {
    $title = __('items_marked_by_tag', array(
        '[tag]' => $tag
    ));
} else {
    $title = __('my_items_marked_by_tag', array(
        '[tag]' => $tag
    ));
}
...
```
