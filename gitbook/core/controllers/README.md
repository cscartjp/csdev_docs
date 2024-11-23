---
icon: diagram-previous
---

# コントローラー

CS-Cartのコントローラーは、２つの主要な実行可能なPHPファイル（`admin.php`または`index.php`）最初に呼び出し、プログラム機能を実装したPHPファイルをさらに連続して実行します。

## 1. index.phpまたはadmin.php-> 2. init.php-> 3. [controller_name] .php

CS-Cartでは、3番目のファイルはコントローラーと呼ばれます。  
プログラムでのデータ処理はこのファイルで行われます。—データベースなどから必要なデータを抽出し、表示用データを作成します。

コントローラーへのパスとその名称は、メインの実行ファイル（index.php等）へ渡された`dispatch`パラメーター（URLのパラメーター）の値に基づいて自動的にプログラムで定義されます。

dispatch パラメーターは以下のようなフォーマットになります

``[controller_name].[mode_name]``  
（コントローラー名.モード名）

- `[controller_name]`はコントローラーの名前です。
- `[mode_name]`ではコントローラーに定義されたモードが動作します。
- 呼び出されるファイル名は`[controller_name].php`となります。

管理パネル(`admin.php`)用のコントローラーは全て`app/controllers/backend`ディレクトリにあります。  
ショップフロント(`index.php`) 用のコントローラーは、`app/controllers/frontend`ディレクトリにあります。  
コントローラーがディレクトリにない場合は、CS-Cartは`app/controllers/common`から起動を試みます。  
「common」は同じコントローラーがショップフロントと管理者パネルの両方にサービスを提供する際に使用されます。提供される機能は両エリアで同じとなります。

コントローラーの接続は`/core/fn.control.php`の`fn_dispatch()`関数により実行され、任意のパラメーターを受け入れず、下記のアクションを実行します:

- `dispatch`パラメーターの整合性のチェック
- コントローラーにおける現在のユーザーのアクセス許可をチェック
- 必要に応じて安全なプロトコル（HTTPS）に置き換える
- 正しい順序で連続接続するために`precontrollers` と `postcontrollers`(アドオンまたはコアから)の実行順のリストを準備
- 表示するためのテンプレートを自動的に定義

`例`

_http\://cscart_dir/admin.php?dispatch=products.manage_  
この場合、実行ファイルはadmin.phpとなり、dispatch パラメーターは、products.manageとなっています。

呼び出されるコントローラーは`/controllers/backend/products.php`です。  
modeパラメーターである`manage` は実行しなければならないアクションとしてコントローラーファイル内に記述されています。  
この例では、productsコントローラーのmanageモードによって、データベースから商品リストを抽出し、管理者エリアに商品のリストを表示します。

> ❗️ 注意
>
> コントローラーの名称はユニークなものにしなければなりません。あるコントローラーがアドオンで定義され、その名前がすでに標準（コア）のコントローラーの名前と同じである場合、呼び出し時にエラーが発生します。

## コントローラーの構造

各コントローラーは下記のロジカルブロックを含んでいます：

- `POST`リクエストの処理
- `GET`リクエストの処理
- コントローラ内でのみ使用されるローカル機能の定義

`dispatch`パラメーターの`[mode_name]`の部分は`GET`リクエスト処理の動作モードを設定するために使用されます。

## POSTリクエスト

`POST`リクエストは常に`GET`リクエストより先に処理されます。  
`fn_dispatch()`関数にコントローラーから返ってくるブロックの最後に文字列が存在しなければなりません。

```php
<?php
return array(CONTROLLER_STATUS_OK, "$index_script?dispatch=controller_name$suffix");
```

`CONTROLLER_STATUS_OK`というパラメーターは、コントローラーの処理結果の成功ステータスを持つ定数が含まれます。2番目のパラメーターは、POSTリクエストの処理後、置き換え用のURIを含む文字列です。

`例`

```php
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {

    if ($mode == 'add') {
        // ここに商品が追加された際の処理（コード）を入力します
    }

    return array(CONTROLLER_STATUS_OK, "$index_script?dispatch=products$suffix");
}
```

## GETリクエスト

`GET`リクエストの処理は常に`POST`リクエストの後ろに記述します。

`例1`

```php
if ($mode == 'manage') {
     // ここに"http：//cscart_dir/admin.php？dispatch = products.manage"が実行された際の処理を書く
}
```

ここではパラメーター`mode` (`dispatch=controller.mode` )の値をチェックされ、パラメーターが`manage`と等しい場合に条件付きオペレーター（if文）内のコードが実行されます。  
このコードはGET リクエストで実行されます。

`GET`モードの処理が終了すると、コントローラーは通常、以下の終了処理を実行します：

- テンプレーターへコントローラーを移動する
- `GET`メソッドによるリダイレクション
- 追加アクション: `exit;` 無しでのコントローラーの実行とプログラムの完了

この場合では、コントローラーの処理は自動的に`fn_dispatch()`関数に移されます。  
それがはっきりと定義されてない場合は、以下の`例２`と`例３`を行う必要があります。  
テンプレーターにデータを渡すには、以下のように記述します：

```php
<?php
$view->assign('template_var_name', $php_var_name);
```

ここでの`template_var_name`は、テンプレートで`$php_var_name`という変数の名前で利用できます。

`例2`

```php
<?php
if ($mode == 'manage') {
    $product_name = 'Product 1';
    $product_description = 'Product description';
    $view->assign('tpl_product_name', $product_name);
    $view->assign('tpl_product_description', $product_description);

}
```

このコードがコントローラで実行された2つの変数（`tpl_product_name`、`tpl_product_description`）がtemplaterに渡され、テンプレートでは`$tpl_product_name`、`$tpl_product_description`という変数で利用できるようになります。

## Functions

コントローラーファイル内に書かれた関数は、一般的な関数（function）の規則に従って定義します。特定のコントローラーファイルに書かれた関数を別のコントローラーで呼び出す必要がある場合は、アドオンの`func.php`に記述する必要があります。

## 利用可能なデータ

コントローラでデータを操作するには、次の標準配列を使用する必要があります。

- `$_REQUEST` - `GET`と`POST`リクエストが含まれます。この配列のすべての変数は特別な方法で処理されます。HTMLタグは削除されます。PHPによって自動的に追加されるスラッシュ（対応する設定が有効になっている場合）は削除されます。

- `$_SESSION` -セッションデータを格納する標準のPHP配列。

- `Registry` - プログラムの任意の場所からアクセスする必要があるデータ保持用の特別な静的クラスリポジトリ。

たとえば、起動時に読み取られるプログラムの構成パラメータは、`Registry class`に格納されます。このクラスの特徴は、このクラスに格納されているデータをキャッシュできることです。これにより、たとえば、この`Registry class`のキャッシュに情報を保存することにより、ほとんど更新されない情報をデータベースに繰り返し要求することを回避できます。

## Templaterへのデータの転送

コントローラーが実行され、制御がに戻された後、コントローラーは`fn_dispatch()`関数、プログラムの制御と、処理および表示が必要なテンプレートへのパスをテンプレート作成プログラムに転送します。

{% hint style="info" %}
TIPS

コントローラでは、templator を制御するか、スクリプトの実行を終了を行うか、別のアドレスにリダイレクトする必要があります。
{% endhint %}

デフォルトでは、このコントローラーのテンプレートのパスは次のように自動的に定義されます。

管理パネル：`/backend/templates/views/[controller_name]/[mode_name].tpl`  
ショップフロント：`/ themes / [テーマ名] /templates/views/ [controller_name] / [mode_name] .tpl`

`例`  
_http\://cscart_dir/admin.php?dispatch=products.manage_

表示されるテンプレートのパス：`/backend/templates/views/products/manage.tpl`

## コンソールでコントローラーを実行する

- バックエンドコントローラー：

```text
•	php /path/to/cart/admin.php --dispatch=controller.mode
```

- フロントエンドコントローラー：

```text
•	php /path/to/cart/index.php --dispatch=controller.mode
```

- 共通（common）コントローラー：上記のコマンドのいずれか（共通コントローラーはadmin.phpおよびindex.php）で動作します。

{% hint style="info" %}
TIPS

/path/to/cartを、お使いのサーバー上のCS-Cartをインストールしたパスに置き換えます。`admin.php`の名前も異なる可能性があります（セキュリティ対策としてadmin.phpの名前を変更することをお勧めします）。
{% endhint %}

コントローラーのパラメーターは次のようになります。

```text
php admin.php --dispatch=controller.mode --param1=value --param2=value --param3[]=value1 --param3[]=value2 --param4[param5]=value
```

これらのパラメーターは、通常のリクエストパラメーターとしてコントローラーに渡され、`$_REQUEST`配列で使用できます。コントローラーのモードがPOST要求を処理する場合は、パラメーターを指定した後、スクリプトに`--p引数`を設定します。

コンソールでコントローラーのモードを実施すると、そのモードで処理されたものが実行されます。コマンドの実行後にコマンドラインに何も表示されない場合は、次のような場合が考えられます：

- コントローラは、`fn_set_progress`、`fn_echo`、`fn_set_notification`の場合は値を返しません。
- コマンドに間違ったモードが指定されていた
- モードに指定されたパラメーターが正しくない（もしくはパラメーターが指定されていない）
- コントローラーは一部のスクリプトでは実行できません。たとえば、バックエンドコントローラーは`index.php`を使用して実行することはできません。