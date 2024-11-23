---
icon: graduation-cap
---

# Advanced Add-on チュートリアル

このチュートリアルでは [**フック**](doc:hooking) や[**Pre- and Post- コントローラー**](doc:pre-and-post-controllers) を使用するような高度なテクニックのアドオン作成方法を説明します。

{% hint style="info" %}
INFORMATION _Hook_（フック）はとてもパワフルな技術で、CS-Cartではこれが多用されています。

PHPフックは追加の処理をデフォルト処理の前後で実行する、またはデフォルトの処理ルーチーンをオーバーライド（上書き）するために使用されます。 テンプレートフックはテンプレートのレンダリングを処理するため使用します。例えば、オリジナルテンプレートを修正することなく、追加データを表示したりすることが可能です。 プレまたはポストコントローラーは特別なPHPファイル(アドオンディレクトリ内に置かれたファイルの名前による)で、特定のコントローラーが実行される前か後に呼び出されます。
{% endhint %}

このチュートリアルでは [PHPフック](doc:php-hooks) と [テンプレートフック](doc:tpl-hooks) の両方を使うアドオンを作成します。

このチュートリアルで作成するアドオンはログイン済みのユーザーが閲覧したカテゴリーの情報を収集します。さらに、ユーザーが閲覧したカテゴリーのアクセス数を管理パネルのダッシュボードに表として表示します。

### 必要環境

このチュートリアルを行うためは、インストールされたCS-Carが必要になります。

**PHPやSmarty、CS-Cartアドオンディレクトリ構造に慣れることが重要です。**

このチュートリアルを進める前に [「Hello World」Addonチュートリアル](doc:hello-world-add-on-tutorial) を先に行うことをお勧めします。

### アドオン初期設定: addon.xmlとinit.phpの作成

インストールしたCS-Cartのルートディレクトリ内の\_app/addons\_ ディレクトリに移動し、\_advanced\_addon\_というサブディレクトリを作成します。

このディレクトリに以下の内容を含んだaddon.xml ファイルを作成します：

_addons/advanced\_addon/addon.xml_

```xml
<?xml version="1.0"?>
<addon scheme="3.0">
   <id>advanced_addon</id>
   <version>1.0</version>
   <priority>100500</priority>
   <position>0</position>
   <status>active</status>
   <default_language>ja</default_language>
   <queries>
        <item>DROP TABLE IF EXISTS ?:advanced_addon_data;</item>
        <item>
            CREATE TABLE `?:advanced_addon_data` (
            `user_id` int(11) unsigned NOT NULL DEFAULT 0,
            `categories` text NOT NULL DEFAULT '',
            PRIMARY KEY (`user_id`)
            ) Engine=MyISAM DEFAULT CHARSET UTF8;
        </item>
        <item for="uninstall">DROP TABLE IF EXISTS ?:advanced_addon_data;</item>
    </queries>
</addon>
```

[addon.xml ダウンロード](https://gist.github.com/cscartjp/fa514bef48ca4bdea551)

これに対応する言語ファイルは以下となります。

> /var/langs/ja/addons/advanced\_addon.po

```
msgid ""
msgstr "Project-Id-Version: tygh"
"Content-Type: text/plain; charset=UTF-8\n"
"Language-Team: Japanese\n"
"Language: ja_JP\n"

msgctxt "Addons::name::advanced_addon"
msgid "Advanced Add-on"
msgstr "アドバンスド・アドオン"

msgctxt "Addons::description::advanced_addon"
msgid "This is Advanced Add-on tutolial."
msgstr "アドオンの学習を行います。"
```

[advanced\_addon.po ダウンロード](https://gist.github.com/cscartjp/fa514bef48ca4bdea551)

***

さらに同じディレクトリに以下の内容を含んだ\_init.php\_ファイルを作成します。

> addons/advanced\_addon/init.php

```php
<?php
/***************************************************************************
*                                                                          *
*   (c) 2004 Vladimir V. Kalynyak, Alexey V. Vinokurov, Ilya M. Shalnev    *
*                                                                          *
* This  is  commercial  software,  only  users  who have purchased a valid *
* license  and  accept  to the terms of the  License Agreement can install *
* and use this program.                                                    *
*                                                                          *
****************************************************************************
* PLEASE READ THE FULL TEXT  OF THE SOFTWARE  LICENSE   AGREEMENT  IN  THE *
* "copyright.txt" FILE PROVIDED WITH THIS DISTRIBUTION PACKAGE.            *
****************************************************************************/

if (!defined('BOOTSTRAP')) { die('Access denied'); }

fn_register_hooks(
    'get_category_data_pre'
);
```

[init.php ダウンロード](https://gist.github.com/cscartjp/8aaee0a0ea7b46b026fe)：

> 👍 ヒント
>
> 通常、関数名はひと目で分かるように書かれています。\
> （例えば、`get_product`は”一つの”商品情報を取得する関数です）
>
> フックは通常、 （例えばget\_products\_preとget\_products\_post ）名称に応じて前処理や後処理のフックのために`_pre`または`_post`という接尾辞が付いていて、中に置かれている機能にちなんで命名されています。

このファイル（init.php）では、カテゴリーデータを取得する特定のクエリの前に呼び出される`get_category_data_pre` フックに接続しようとしていることを表しています。

[Hooks baseツール](http://www.cs-cart.com/api)では、この他のフックについての情報を検索することが出来ます。

### データの取得: func.php

アドオンディレクトリ(_app/addons/advanced\_addon_ ) に\_func.php\_ ファイルを作成します。このPHPファイルは、フックを埋め込むための関数を含んでいます。

> _addons/advanced\_addon/func.php_

```php
<?php
/***************************************************************************
*                                                                          *
*   (c) 2004 Vladimir V. Kalynyak, Alexey V. Vinokurov, Ilya M. Shalnev    *
*                                                                          *
* This  is  commercial  software,  only  users  who have purchased a valid *
* license  and  accept  to the terms of the  License Agreement can install *
* and use this program.                                                    *
*                                                                          *
****************************************************************************
* PLEASE READ THE FULL TEXT  OF THE SOFTWARE  LICENSE   AGREEMENT  IN  THE *
* "copyright.txt" FILE PROVIDED WITH THIS DISTRIBUTION PACKAGE.            *
****************************************************************************/

if (!defined('BOOTSTRAP')) { die('Access denied'); }

function fn_advanced_addon_get_category_data_pre($category_id, $field_list, $get_main_pair, $skip_company_condition, $lang_code)
{
    //ログイン中のユーザーIDをセッションから取得する
    $auth = $_SESSION['auth'];
    
    //ログイン中のユーザーがストアフロントにいるかどうかの判定
    if (!empty($auth['user_id']) && AREA == 'C') {
        //データベースにユーザーのデータが存在するかチェック
        //必要に応じてアクセスデータを作成
        $viewed_categories = db_get_field('SELECT categories FROM ?:advanced_addon_data WHERE user_id = ?i', $auth['user_id']);
        
        if (!empty($viewed_categories)) {
            $viewed_categories = unserialize($viewed_categories);
        }
        
        $viewed_categories[$category_id] = true;
        $viewed_categories = serialize($viewed_categories);
        
        //データベースに書き込む
        db_query('REPLACE INTO ?:advanced_addon_data VALUES (?i, ?s)', $auth['user_id'], $viewed_categories);
    }
}
```

[func.php ダウンロード](https://gist.github.com/cscartjp/0f4588e1667a21bbeb00)：

関数`fn_advanced_addon_get_category_data_pre()` は現在表示されているカテゴリーを取得し、ストアを閲覧している人とリンクしてこのデータベースを格納します。

> 👍 ヒント
>
> PHPコード・フックの関数名は命名規則に従って書いて下さい：
>
> **fn\_** + \[**addon id**] +**\_**+ \[**hook name**]
>
> 命名規則が正しくない関数は無視されます。

### 管理パネルにデータを表示する（index.post.tpl, index.post.php）

管理パネルに取り出したデータを表示するためには、管理パネルのダッジュボードテンプレート(_design/backend/templates/views/index/index.tpl_ )のフックを利用して新しいデータブロックを表示します。

1. _design/backend/templates/addons_ ディレクトリに移動し、_advanced\_addon_ ディレクトリを作成してください。
2. このディレクトリにサブディレクトリとして\_hooks\_を作成し、さらにその中に\_index\_ディレクトリを作成します。
3. このディレクトリ（_design/backend/templates/addons/advanced\_addon/hooks/index_）に次の内容を含んだ _index.post.tpl_ ファイルを作成します：

> _design/backend/templates/addons/advanced\_addon/hooks/index/index.post.tpl_

```html
<div class="dashboard-row-bottom">
    <div class="dashboard-viewed-category">
        <h4>Viewed categories</h4>
        <div class="dashboard-viewed-category-list">
            {if $viewed_categories}
            <ul>
                {foreach from=$viewed_categories item="category_data"}
                <li><strong><a href="{"profiles.update?user_id=`$category_data.user_id`"|fn_url}">{$category_data.user_name}</a></strong>:&nbsp;
                    {foreach from=$category_data.categories key="category_id" item="category_name"}
                    <a href="{"categories.update?category_id=`$category_id`"|fn_url}">{$category_name}</a>, 
                    {/foreach}
                </li>
                {/foreach}
            </ul>
            {else}
            <ul>
                <li>No data found</li>
            </ul>
            {/if}
        </div>
    </div>
</div>
```

[ダウンロード](https://gist.github.com/cscartjp/3f95c6843c81eb4205bb)

> 👍 ヒント
>
> PHPコード・フックとは違い、テンプレート・フックは、他のファイルで宣言する必要はありません。
>
> 適切な名前のついたディレクトリに適切に名前の付けられたテンプレートを置くだけで動作します。その条件は以下の通りです：
>
> * **design/backend/templates/addons//hooks** ・・・管理パネル用ディレクトリ
> * **var/themes\_repository/basic/templates/addons//hooks** ・・・ショップフロント用ディレクトリ
> * **var/themes\_repository/basic/mail/templates/addons//hooks** ・・・メールテンプレート用ディレクトリ
>
> さらに上記のディレクトリの中に次のような名称のテンプレートファイル（.tplファイル）を作成する必要があります。：\
> **\[template name]/\[hook name].\[pre|post].tpl**

テンプレートファイルだけでは、データベースからデータを取得することはできません。これらの処理は、\_index.php\_のコントローラの「ポストコントローラ」で実行する必要があります。

_app/addons/advanced\_addon_ ディレクトリに移動し、サブディレクトリ\_controllers/backend\_ を作成します。

作成したディレクトリで \_index.post.php\_という名称で下記の内容が書かれたPHPファイルを作成してください：

> app/addons/advanced\_addon/controllers/backend/index.post.php

```php
<?php
/***************************************************************************
*                                                                          *
*   (c) 2004 Vladimir V. Kalynyak, Alexey V. Vinokurov, Ilya M. Shalnev    *
*                                                                          *
* This  is  commercial  software,  only  users  who have purchased a valid *
* license  and  accept  to the terms of the  License Agreement can install *
* and use this program.                                                    *
*                                                                          *
****************************************************************************
* PLEASE READ THE FULL TEXT  OF THE SOFTWARE  LICENSE   AGREEMENT  IN  THE *
* "copyright.txt" FILE PROVIDED WITH THIS DISTRIBUTION PACKAGE.            *
****************************************************************************/

use Tygh\Registry;

if (!defined('BOOTSTRAP')) { die('Access denied'); }

$viewed_categories = db_get_array('SELECT * FROM ?:advanced_addon_data');

if (!empty($viewed_categories)) {
    foreach ($viewed_categories as $key => $category_data) {
        $category_data['user_name'] = fn_get_user_name($category_data['user_id']);
        $category_data['categories'] = unserialize($category_data['categories']);
        $category_data['categories']  = fn_get_category_name(array_keys($category_data['categories']));

        $viewed_categories[$key] = $category_data;
    }

    Registry::get('view')->assign('viewed_categories', $viewed_categories);
}
```

[ダウンロード](https://gist.github.com/cscartjp/0e5b7525a12fe6e23fed)

すべてのファイルパスや名前、ファイルの内容が正常に作動することをよく確認して下さい。

### 動作確認

ファイルをアップロード後、まずはアドオンを確認します。\
CS-Cartの管理パネルの\_アドオン→アドオン管理\_に移動します。

リストから**Advanced Add-on**を探し、アドオン名の近くにある【**インストール**】をクリックします。

インストールが無事に完了すると通知が表示されます。

ダッジュボードに移動して、ページの下部を確認して下さい。\
以下の画像に似た新しいセクションが表示されているはずです。

ご覧の通り、これといったデータはありませんがダッシュボードに新しいセクションが表示されました。

ブラウザをショップフロントに切り替えて、お客様としてログインしてストア内をいろいろ検索してみてください。ランダムにカテゴリーを表示するだけでも結構です。さらに、これとは異なるユーザーアカウントでも同じように試してみてください。

管理パネルのダッシュボードページをリロードし、**Viewed categories**の状態を確認してください：

このセクションにはあなたが閲覧したカテゴリーが表示されているはずです。

### 独自のスタイルを適用する

管理パネルのデザインに合わせて作成したアドオンから特別なCSSを読み込む方法を説明します。

1. CSS（.less）を作成する。
2. 1で作成したファイルを読み込むテンプレートフックを作成する。

### このアドオンから利用するCSS（.less）は以下のディレクトリに作成します。

> /design/backend/css/addons/advanced\_addon/styles.less\
> （ファイル名「styles.less」は自由に決めることが出来ます。）

```less
.dashboard-viewed-category{
    margin-top: 32px;
    width: 50%;
    
    .dashboard-viewed-category-list{
        padding: 8px 0;
        -webkit-border-radius: 4px;
        -moz-border-radius: 4px;
        border-radius: 4px;
        overflow-y: scroll;
        min-height: 200px;
        max-height: 290px;
        overflow-y: auto;
        border: 1px solid #eee;
        background: #fcfcfc;
        
        
        ul{
            margin: 0;
            list-style: none;
            
            li{
                padding: 7px 10px;
                border-top: 1px solid #eee;
            }
            
            li:first-child{
               border: none; 
            }
        }
    }
}
```

[ダウンロード](https://gist.github.com/cscartjp/15bdd1d147a83a8284ff)

### CSSを読み込むテンプレートフックのファイルを作成する。

> /design/backend/templates/addons/advanced\_addon/hooks/index/styles.post.tpl

ファイルの中には以下のようにします。\
これで1で作成したファイルを読み込むHTMLソースが書き出されます。

```smarty
{style src="addons/advanced_addon/styles.less"}
```

テンプレートキャッシュをクリアし、再度ダッシュボードを表示してください。\
このようにアドオンで使用するCSSファイルやJavascriptファイルを作成し、読み込むことが出来ます。
