---
icon: hammer
---

# テーマを使用したショップの設定の変更

{% hint style="info" %}
HINT

この機能は、CS-Cart4.3.7以降で使用できます。
{% endhint %}

テーマは、サムネイルサイズ、商品リストの列数、特定の商品リストテンプレートなど、特定のショップの設定を考慮して設計することができます。テーマがアクティブになると、それに応じて設定を変更可能です。

## 管理者に表示される設定

管理者がいくつかの設定を変更するテーマを有効化すると、現在の設定とテーマに設定された新しい値のリストが表示されます。

＜画像＞

これらの変更を許可するには、管理者はチェックボックスをオンにして、`選択した設定を上書き`をクリックする必要があります。チェックされた設定のみが変更されます。すべての設定にチェックマークが付いていない場合、テーマの外観と機能が影響を受ける可能性があります。

## 設定を変更する方法

テーマが変更する設定を指定するには、テーマの`manifest.json`に`settings_overrides`セクションを追加します。

```json
"Settings_section_name": {  // 設定のセクション名
  "setting_name": value     // 設定: 値
  ...
}
```

セクションのリストは、`cscart_settings_sections`テーブルにあります。

![](https://files.readme.io/f62549b-cscart\_settings\_sections.png)

設定とその値は、`cscart_settings_objects`テーブルにあります。

＜画像＞

一部の設定は、\[表示設定]→\[クイックビューの有効化]などのチェックボックスで表されます。チェックボックスをオン（`true`）にするには、スキーマで指定します。\
チェックボックスをオフ（`false`）にするには、次の例のように指定します。

`manifest.json`の `settings_overrides` の例を次に示します。\
ここに[サンプルがあります](https://github.com/cscart/custom-theme-tutorial/blob/master/manifest.json)。

```json
"settings_overrides": {
  "General": {
    "enable_compare_products": false
  },
  "Appearance": {
    "default_products_view": "products_without_options",
    "default_product_details_view": "bigpicture_template",
    "thumbnails_gallery": true,
    "enable_quick_view": false,
    "available_product_list_sortings": [
      "product-asc",
      "product-desc",
      "price-asc",
      "price-desc"
    ]
  },
  "Thumbnails": {
    "product_lists_thumbnail_width": 300,
    "product_lists_thumbnail_height": 300
  }
}
```

このテーマが有効化されると、manifest.jsonにこの設定を持つテーマは次のことを行います。

* 商品の比較を無効にします。（一般設定→「お客様による商品の比較を許可する」）
* オプションなしのリストを商品リストテンプレートとして設定します。（表示設定→商品リストのデフォルトビュー）
* 商品ページのテンプレートとして「商品画像大」を設定します。（表示設定→商品詳細ページのビュー）
* ミニサムネイルギャラリーを有効にします。（表示設定→ミニサムネイル画像をギャラリーとして表示）
* クイックビューを無効にします。（表示設定→クイックビューを有効にする）
* 選択ソートアルファベット順：AtoZに、アルファベット順：ZtoAに、価格によるソート：安いものから高いものへ、そして高い順：価格によるソートのみ利用可能な商品のリストソーティングなど。（表示設定→利用可能な商品リストのソート）
* 商品リストのサムネイルの幅と高さを300ピクセルに設定します。（サムネイル→商品リスト（カテゴリ、検索など）サムネイルの幅）（サムネイル→商品リスト（カテゴリ、検索など）サムネイルの高さ）
