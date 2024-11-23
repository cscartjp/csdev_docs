---
icon: diamonds-4
---

# ツールチップ

## ツールチップとは

ツールチップはページ上の追加情報を表示するインターフェース要素です。

ユーザーはアイテムをクリックすることなく、カーソルを乗せるとそのアイテムについての追加情報が書かれているツールチップが表示されます。

## 使用法

CS-Cartのツールチップは管理パネルのフォームフィールドへ説明ボックスを追加するために使用します。

＜画像＞

## ツールチップの追加方法

ツールチップは共通のフォームフィールドとアドオンでは異なる方法で定義されています。

それぞれの場合を説明します。

## 共通のフォームフィールドへのツールチップ追加

これらのツールチップは特別な言語変数を定義することで追加されます。このようなツールチップ変数には2つのタイプがあります：

共通フォームへのツールチップは特別な言語変数を定義することで追加されます。\
このツールチップ変数は2つのタイプがあります：

1. このツールチップを追加するための変数の名前のみを含むタイプ
2. このツールチップを追加するため変数が使用されるテンプレートの変数の名前のみを含む、またはこの変数が使用されるテンプレート

### 1.変数の名前のみを含んだツールチップ

これらは以下の構造を持っています：\
「ttc\_LANGVAR\_NAME. Where LANGVAR\_NAME」 はこのツールチップを追加するための変数の名前です。\
このようなツールチップ変数を定義する場合、ツールチップは\_LANGVAR\_NAME\_ が使用されている全てのフォームに表示されます。

### 2.変数の名前とテンプレートを含んだツールチップ

これらは以下の構造を持っています

`tt_TEMPLATE_NAME` `LANGVAR_NAME`.`LANGVAR_NAME` とはこのツールチップを追加するための変数の名前です。\
`TEMPLATE_NAME` はこの変数が使用されたテンプレートです。\
このツールチップは定義されたテンプレートのみに表示されます。\
これは異なるページの同じフィールド名で同じツールチップが表示されないようにするために役立ちます。

ツールチップ変数がどのように形成されるかの例：

`tt_views_products_update_list_price` = tt\_ + TEMPLATE\_NAME + \_ + LANGVAR\_NAME = tt\_ + views\_products\_update + \_ + list\_price\_

### ツールチップ用の用語変数表示

目的のページに直接ツールチップ用のすべての言語変数を表示することが可能です。

これらを表示するには、選択したページのURLに以下のパラメータを追加してください：`stt`（ツールチップを表示する）と`ctpl`（キャッシュをクリアする）

例えば、以下のページに全てのツールチップを表示するには：

```http
http://example.com/admin.php?dispatch=products.update&product_id=180
```

↓

```http
http://example.com/admin.php?dispatch=products.update&product_id=180&stt&ctpl
```

2つの言語変数をもつポップアップボックスはそれぞれ並んで表示されます。\
変数値がすでに定義されている場合は、同じポップアップボックスでも表示されます。

＜画像＞

変数表示モードを無効にするには、ページのキャッシュをクリアしてください：

```http
https://example.com/admin.php?dispatch=products.update&product_id=180&ctpl
```

\`\*

### 例

例として、商品の詳細ページで`Name`フィールドに選択したテンプレートのにのみ表示されるツールチップを追加してみましょう:

```http
http://example.com/admin.php?dispatch=products.update&product_id=180
```

* ページを開き、URLの最後に`&stt&ctpl` を追加してください。

```http
https://example.com/admin.php?dispatch=products.update&product_id=180&stt&ctpl
```

* 目的のファイル名(この例のネームフィールド)にマウスポインターを合わせ、`tt_` とともに言語変数をコピーします：_tt\_views\_products\_update\_name_

Go to Administrtation > Languages > Translations and click + to add the new language variable.

* \_一般設定 > 言語 > 言語変数\_へ移動し、＋をクリックして新しい言語変数を追加します

tt\_views\_products\_update\_name ), `言語変数`フィールドでは前の手順( tt\_views\_products\_update\_name ) から変数を入力できます\
`値`フィールドではツールチップのテキストを入力します。

最後に`登録`をクリックします。

＜画像＞

* 商品詳細ページを開いてツールチップが正確に表示されるか確認します。

## アドオンの設定のためにツールチップを追加

アドオン用のツールチップは\_addon.xml file\_ (_/app/addons/add-on\_name_) で定義されています。

設定用のツールチップを追加するには、このXMLに値を定義します。\
ツールチップの翻訳はタグに定義されています:

```xml
<tooltip>Tooltip text</tooltip>
<tt_translations>
        <item lang="es">Tooltip translation</item>
</tt_translations>
...
```

例えば

`Anti Fraud`アドオンのコードでは`Maxmind license key`フィールドに`Hello`ツールチップを追加する際はこのようになります：

```xml
<settings>
    <sections>
        <section id="general">
            <name>General</name>
            <items>
                <item id="anti_fraud_key">
                    <name>Maxmind license key</name>
                    <type>input</type>
                    <default_value/>
                    <translations>
                        <item lang="ja">MaxMindのライセンスキー</item>
                        <item lang="es">Clave de licencia de MaxMind</item>
                        <item lang="fr">Clé de licence Maxmind</item>
                        <item lang="da">Maxmind licens nøglen</item>
                        <item lang="el">Αριθμός-κλειδί Maxmind</item>
                        <item lang="ro">Liciență cheie Maxmind</item>
                        <item lang="ru">Лицензионный ключ Maxmind</item>
                        <item lang="sl">Maxmind licenčni ključ</item>
                        <item lang="zh">Maxmind 许可证钥匙</item>
                    </translations>
                    <tooltip>Hello</tooltip>
                    <tt_translations>
                        <item lang="es">Hola</item>
                    </tt_translations>
                </item>
...
```
