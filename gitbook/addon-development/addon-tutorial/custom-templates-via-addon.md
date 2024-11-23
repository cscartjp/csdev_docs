---
icon: table-list
---

# 商品リスト、詳細ページのカスタムテンプレートを作成

この記事では、商品リストおよび商品詳細ページのカスタムテンプレートを作成する方法について説明します。

また、これらのカスタムテンプレートを`My Changes`アドオンの一部として保存します。\
`My Changes`アドオンのフォルダーにカスタムテンプレートを保存することには、以下2つの利点があります。

* 作成または変更したテンプレートファイルの追跡が簡単に行えます。
* アップグレード後に変更を再適用する必要が無くなります。

{% hint style="info" %}
HINT

designまたはvar/themes\_repositoryフォルダーで作業できます。 先に進む前に、[これらのフォルダー構造を理解](addon-directories/)してください。
{% endhint %}

## 商品リストのテンプレート

デフォルトでは、CS-Cartには、商品リスト用の3つのテンプレートタイプが用意されています。

* グリッド（products\_multicolumns.tpl）
* オプションなしのリスト（products\_without\_options.tpl）
* コンパクトリスト（short\_list.tpl）

これらのテンプレートのいずれかをデフォルトとして設定するには、管理パネルを開き、基本設定→表示設定→「商品一覧ページのレイアウト設定」に進みます。

`My Changes`アドオンを利用して商品リストのカスタムテンプレートを追加するには、カスタム商品リストのテンプレート設定を含むファイルと、テンプレート自体のファイルを作成します。

## テンプレートのディレクトリ構造

```
|
 └─ addons/
     |
     └─ my_changes/
         |
         └─ blocks/
             |
             ├─ list_templates/
             |   |
             |   └─ modern_list.tpl （商品リストのテンプレート）
             |
             └─ product_list_templates/
                 |
                 └─ products_modern_list.tpl（テンプレートの設定ファイル）
```

設定を含むファイルには、追加するテンプレートとその設定が記述されています。

既存の商品リストテンプレートの1つを`templates/blocks/list_templates`から`templates/addons/my_changes/blocks/list_templates`にコピーしてから、テンプレートの名前を変更することをお勧めします。

{% hint style="info" %}
HINT

既存のテンプレートのファイルをコピーすることで構文エラーのリスクを最小限に抑えることができます。
{% endhint %}

## テンプレート設定ファイルの例

```smarty
    {template-description:"tmpl_modern"}
    //上記の行は、テンプレートの名前がtmpl_modernと呼ばれる言語変数に格納されることを意味します
    
    //以下は、テンプレートの場所と設定です
    {include file="addons/my_changes/blocks/list_templates/modern_list.tpl"
    show_name=true
    show_sku=false
    show_rating=true
    show_features=true
    show_prod_descr=true
    show_old_price=true
    show_price=true
    show_clean_price=true
    show_list_discount=true
    show_discount_label=true
    show_product_amount=true
    show_product_edp=true
    show_add_to_cart=true
    show_list_buttons=true
    show_descr=true }
```

## テンプレート設定の簡単な説明

```json
{
    "data": {
        "h-0": "名称",
        "h-1": "タイプ",
        "h-2": "説明",
        "0-0": "`show_name` ",
        "0-1": "Boolean (true / false)",
        "0-2": "商品名を表示",
        "1-0": "`show_sku` ",
        "1-1": "Boolean (true / false)",
        "1-2": "商品コードを表示",
        "2-0": "`show_rating` ",
        "2-1": "Boolean (true / false)",
        "2-2": "レビュー（星）を表示",
        "3-0": "`show_features` ",
        "3-1": "Boolean (true / false)",
        "3-2": "商品の追加情報を表示",
        "4-0": "`show_old_price` ",
        "4-1": "Boolean (true / false)",
        "4-2": "商品の通常価格を表示します。",
        "5-0": "`show_price` ",
        "5-1": "Boolean (true / false)",
        "5-2": "商品価格を表示",
        "6-0": "`show_clean_price` ",
        "6-1": "Boolean (true / false)",
        "6-2": "税込みの価格を表示",
        "7-0": "`show_discount_label` ",
        "7-1": "Boolean (true / false)",
        "7-2": "割引額を表示",
        "8-0": "`show_product_amount` ",
        "8-1": "Boolean (true / false)",
        "8-2": "在庫数を表示",
        "9-0": "`show_add_to_cart` ",
        "9-1": "Boolean (true / false)",
        "9-2": "`カートに追加`ボタンを表示",
        "10-0": "`show_list_buttons` ",
        "10-1": "Boolean (true / false)",
        "10-2": "リストボタンを表示  \n（欲しいものリストに追加、比較リストに追加など）",
        "11-0": "`show_descr` ",
        "11-1": "Boolean (true / false)",
        "11-2": "商品の説明を表示",
        "12-0": "`but_role` ",
        "12-1": "Text",
        "12-2": "テンプレートでのボタンの表示方法。  \n使用可能な[役割]のいずれかを選択。  \n`Action`  \n`Text`  \n`Delete`  \n`Big`  \n`Tool `"
    },
    "cols": 3,
    "rows": 13,
    "align": [
        "left",
        "left",
        "left"
    ]
}
```

すべて正しく設定出来ていれば、新しいテンプレートが管理パネルの\[一般設定]→\[表示設定]に表示されます。

{% hint style="info" %}
HINT

カスタムテンプレートが表示されない場合は、\[アドオン]→\[アドオンの管理]に移動し、`My Changes` アドオンが有効であることを確認します。`var/themes_repository/templates/addons/my_changes`で作業している場合、`My Changes`をアンインストールする必要があります。\
My Changesはアドオンの管理から再度インストールしてください。
{% endhint %}

＜画像＞

上の画像でもわかるように、アドオンの翻訳を含むファイルに作成したカスタム商品リストの言語変数を追加する必要があります。言語変数の名前は、テンプレート設定ファイルで商品リストをどのように呼び出したかによって異なります。

この場合、次のコードを`var/langs/en/addons/my_changes.po`に追加する必要があります。

```
msgctxt "Languages::tmpl_modern"
msgid "Modern list"
msgstr "Modern list"
```

作成した商品リストのテンプレートを有効にすると、ストアフロントのカテゴリページに表示されます。ただし、テンプレートにはアイコンがありません。

＜画像＞

アイコンの名前は、テンプレートの名前から決定されます。

たとえば、テンプレート設定ファイルの名前が`products-modern-list`の場合、アイコンの名前は`ty-icon-products-modern-list`になります。画像を用紙してCSSを書き換えてください。

## 商品詳細ページのテンプレート

デフォルトでは、CS-Cartには、商品詳細ページ用の2つのテンプレートタイプが用意されています。

* デフォルトのテンプレート（default\_template.tpl）
* 商品画像大（bigpicture\_template.tpl）

これらのテンプレートのいずれかをデフォルトとして設定するには、管理パネルを開き、一般設定→表示設定→`商品詳細ページのビュー`に進みます。

`My Changes`アドオンを介して商品詳細ページのカスタムテンプレートを作成するには、`templates/addons/my_changes/blocks/product_templates`ディレクトリに`.tpl`ファイルを作成する必要があります。

## テンプレートの構造

```
templates/
 |
 └─ addons/
     |
     └─ my_changes/
         |
         └─ blocks/
             |
             └─ product_templates/
                 |
                 └─ modern_template.tpl
```

`templates/blocks/product_templates`から既存のテンプレートのいずれかをコピーすることをお勧めします。コピーしたファイル名は`modern_template.tpl`とします。

＜画像＞

繰り返しになりますが、言語変数をアドオンの翻訳ファイルに追加する必要があります。言語変数の名前は、テンプレートファイルの名前と同じです。この例では、`modern_template`です。

次のコードを`var/langs/en/addons/my_changes.po`に追加する必要があります。

```
msgctxt "Languages::modern_template"
msgid "Modern Product"
msgstr "Modern Product"
```
