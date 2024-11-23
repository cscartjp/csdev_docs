---
icon: folder-gear
---

# スキーマー

スキーマー（scheme）は特定のオブジェクト構造を記述した特別なファイルです。 ブロック、設定、プロモーションなどに関するスキーマーがあります。

全てのスキームは(_...app/schemas_)ディレクトリに置かれています。\
アドオンはそれらを拡張したり、全体または部分的にそれらを無効にすることができます。\[さらに詳しく]\(doc:extending a scheme)

## スキーマーとは

スキーマーはデータ構造を含んでおり、スキーマーによって定義されたアクションを処理する際にエンティティ（オブジェクト）によって使用されます。

例えば、特定のアイテム（商品やページなど）のインポートやエクスポートに関する記述は、それらに直接書かれておらず、すべてのインポートやエクスポート用のスキームによって記述されています。

## スキーマーの種類

CS-Cartには、3種類のスキーマーがあります：\
`データ配列`、`XML構造`、`関数のセット`

これら3種類を組み合わせることはできません。\
（例えば、同じスクリプトにデータ配列と関数セットを記述することは出来ません）

## データ配列

このスキーマーは配列を返します。

例）

* _app/schemas/permissions/admin.php_
* _app/schemas/clone/objects.php_
* _app/schemas/sharing/schema.php_

```php
<?php 
return array(
    'layouts' => array(
         'checked_by_default' => true,
        'function' => 'fn_clone_layouts'
    ),
    'settings' => array(
        'checked_by_default' => true,
        'tables' => array(
            array(
                'name' => 'settings_vendor_values',
                'key' => '', // Do not needed
             ),
         ),
    ),
    // Some other code
);
?>
```

## XML構造

このスキーマーは`fn_get_contents`関数によって読み込まれるテキストファイルです。\
現在では旧式のものとして扱われ、コアでは使用されていません。

```xml
<menu>
    <items>
        <subitems>
            ....
        <subitems>
    </items>
</menu>
```

## 関数のセット

関数は以下のようなスキーマーで記述されます。

関数を持つスキーマーのほとんどは、データ配列をスキーマーへ追加します。

このスキーマーを特別に呼び出す必要はありません。\
ファイルの先頭に`include_once()` で使用すれば、それらを利用することが出来ます。

スキーマーが独立したものである場合（例えば、アクションや設定の値等）、標準で含まれています:`fn_get_schema("settings", "actions.functions")`

* _app/schemas/exim/products.functions.php_
* _app/schemas/last\_view/frontend.functions.php_

```php
<?php
function fn_some_scheme_func1($params)
{
     ...
 }

function fn_some_scheme_func2()
{
     ...
}
?>
```

## スキーマーの拡張

拡張には二種類の方法あります - アドオンを使用するか、タグを編集するかです。

## アドオンを使って拡張する方法

アドオンを使ってスキームを拡張するためには、アドオンのパッケージ内にコアと同じディレクトリ構造を作成し、ファイル名に\_.post\_を加えます。

例えばメインファイルへのパスの場合は以下のようになります。：

_app/schemas/permissions/admin.php_

このファイルの拡張は：

_app/addons/seo/schemas/permissions/admin.post.php_

となります。\
`$schema` 変数に拡張スキームを渡します。

`$schema` 変数は拡張データを含んでおり、最終的に追加された`$schema`変数を返します。

例：

```php
<?php
// $schema変数はすでに標準のスキーマーを含んでいます。
$schema['news'] = array(
    'modes' => array(
        'manage' => array(
            'permissions' => 'manage_news'
        )
    )
);
return $schema;
?>
```

アドオンによるスキームの拡張方法のより詳しい情報は[こちら](doc:extending-a-scheme)を確認してください。

## タグを編集して拡張する方法

編集されたタグはエディション（スタンダード版かマーケットプレイス版）毎に必要なスキームデータを形成するために使用します。

タグの編集によってスキーマーを拡張するには、\
schemeディレクトリに\_schema\_\[PRODUCT\_EDITION]\_ ファイルを作成します。

例えば、次のファイルのスキーマーを拡張する場合、

* _app/schemas/permissions/admin.php_

編集するタグのファイルパスは以下となります。：

* _app/schemas/permissions/admin\_ultimate.php_

`$schema`変数はここでも使用されます。

`$schema`変数は拡張データを含んでいます。このスクリプトは拡張された変数で作動し、最終的に`$schema`変数を返します。

## スキーマーデータの取得方法

スキームデータを取得するには`fn_get_schema()`関数を使用します。

```php
<?php 
function fn_get_schema($schema_dir, $name, $type = 'php', $force_addon_init = false)
{
		....
}
```

引数の解説:

* `$schema_dir` - スキーマーディレクトリの相対パス（パーミッションや設定）
* `$name` - 拡張されていないスキーマーファイル名(admin, schema)。スキーマータイプが関数の場合、この引数は次ようになります：_actions.functions, variants.functions_
* `$type` - スキーマーの種類：_php\_や\_xml_
* `$force_addon_init` - この引数がある場合、無効にするアドオンとその\_func.php\_ファイルが考慮されなければならないことを示します。

スキーマーをリクエストしているとき、全体のスキーマーは全てのアドオンのスキームを含んでいます。

例：

```php
<?php 
$menu = fn_get_schema('menu', 'menu', 'php');
```

メインスキームファイルの検索： _/app/schemas/menu/menu.php_

拡張するスキーマーが次のアドオンディレクトリにも置かれている場合：_addons/my\_addon/app/schemas/xxx/yyy.php_

次のような関数を使用してスキーマーを追加します：`fn_get_schema('xxx', 'yyy')`

スキームを追加するとキャッシュされますので、次回以降すべてのスキーマー・ディレクトリをスキャンする必要はありません。

* アドオンによってスキーマーが追加されているか確認します：
  * _app/addons/access\_restrictions/schemas/menu/menu.post.php_
  * _app/addons/attachments/schemas/menu/menu.post.php_
  * _app/addons/banners/schemas/menu/menu.post.php_
  * _app/addons/data\_feeds/schemas/menu/menu.post.php_
  * _app/addons/news\_and\_emails/schemas/menu/menu.post.php, ‥_
* CS-Cartはスキーマーファイルを１つずつ読み込みますので、結果として全ての有効なアドオンのスキーマーを含んだスキーマーを持つことになります。
