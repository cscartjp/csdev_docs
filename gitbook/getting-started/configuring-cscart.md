---
description: CS-Cartの構成
icon: folder-tree
---

# CS-Cartの構成

## 開発用設定ファイル

CS-Cartでは、`config.local.php`を変更せずに開発用に個別の設定を行うことのできる開発用の設定ファイルを作成できます。このファイルは`local_conf.php`と呼ばれます。

{% hint style="success" %}
TIPS

`local_conf.php`で指定された設定は`config.local.php`よりも優先されます。
{% endhint %}

## 開発用の設定ファイルについては、以下の手順で行ってください。

- CS-Cartをインストールしたルートディレクトリに`local_conf.php`を作成します。
- `local_conf.php`を開き、次のコードを追加します。

```php
<?php

//ライブストアで作業する必要がある場合は、以下の設定を特定のIPのみに適用して、ショップのお客様が影響を受けないようにすることができます。127.0.0.1の代わりにIPアドレスを指定してください。
if ($_SERVER['REMOTE_ADDR'] == '127.0.0.1') {

    // 管理パネルおよびストアフロントのデバッグモードをオンにする
    // define('DEBUG_MODE', true);

    // 開発モードを使用してエラーを表示する
    define('DEVELOPMENT', true);

    // SMARTYおよびPHPエラーを表示
    error_reporting(E_ALL);
    ini_set('display_errors', 'on');
    ini_set('display_startup_errors', true);

    //  PHPブロックキャッシュを無効にする
    $config['tweaks']['disable_block_cache'] = true;

}

// config.local.phpを変更せずに構成を変更できます。

/*
$config['db_host'] = '%DB_HOST%';
$config['db_name'] = '%DB_NAME%';
$config['db_user'] = '%DB_USER%';
$config['db_password'] = '%DB_PASSWORD%';

$config['http_host'] = '%HTTP_HOST%';
$config['http_path'] = '%HOST_DIR%';

$config['https_host'] = '%HTTPS_HOST%';
$config['https_path'] = '%HOST_DIR%';
*/

// キャッシュとストレージのバックエンドも設定することができます

/*
// Cache backend
// 利用可能なキャッシュバックエンド: file, sqlite, database, redis, xcache, apc
// sqliteキャッシュを使用するには、「sqlite3」PHPモジュールをインストールする必要があります
// xcacheキャッシュを使用するには、「xcache」PHPモジュールをインストールしてください
// apcキャッシュを使用するには、「apc」PHPモジュールをインストールしてください
$config['cache_backend'] = 'file';
$config['cache_redis_server'] = 'localhost';
$config['cache_redis_global_ttl'] = 0; // キャッシュサイズがRedisサーバーのメモリサイズに達した場合、設定してください

// セッションのストレージバックエンド。利用可能なバックエンド： database, redis
$config['session_backend'] = 'database';
$config['session_redis_server'] = 'localhost';
$config['cache_apc_global_ttl'] = 0;
$config['cache_xcache_global_ttl'] = 0;
*/
```

## 設定の詳細

### IPアドレスの制限

`local_conf.php`で指定した設定を特定のIPアドレスのみに制限できます。ライブストアで作業する場合にこの設定をしておくと、エラーメッセージとデバッガが表示されるため便利です。IPアドレスの制限は、以下を参考にしてください。

```php
<?php
if ($_SERVER['REMOTE_ADDR'] == '127.0.0.1'){

}
```

{% hint style="info" %}
HINT

ライブストア（公開されているストア）で作業している場合は、127.0.0.1をあなたの環境のIPアドレスに置き換えてください。ストアがまだ一般公開されていない場合は、この条件を使用しなくても問題ありません。
{% endhint %}

### デバッグモード

CS-Cartには、以下の値を確認できる組み込みデバッガーがあります。

- サーバーとPHPの設定
- 現在のページで実行されたSQLクエリーのリスト
- ページの構築に使用されているテンプレート
- リクエストパラメーター
- ページが表示される際に使用された時間とメモリーの量

デバッガーにアクセスするには、CS-Cartの管理パネルに移動し、URLに`debug`パラメーターを追加します。
http://example.com/admin.php?debug

Bug（虫）のアイコンは、ページの右上に表示されます。デバッガーはサイドバーをクリックして開閉することができます。または、`Ctrl + Alt + D`を押すことでも開閉が可能です。

{% hint style="info" %}
TIPS

`debug`パラメーターを使用すると、現在のブラウザーセッションのショップフロント（カスタマーエリア）でデバッガーが使用可能になります。
{% endhint %}

＜画像＞


ストアフロントと管理パネルの両方でデバッガーを常時有効にするには、`local_conf.php`の下記の箇所をコメントアウトします。

```php local_conf.php
<?php
define('DEBUG_MODE', true);
```

{% hint style="warning" %}
注意

一般のお客様がデバッグデータにアクセス可能になるため、ライブストアでは絶対に`define('DEBUG_MODE', true);`は使用しないでください。
{% endhint %}

### 開発モード

PHPコード、Smartyテンプレート、またはSQLクエリーにエラーがある場合、通常はSmartyおよびPHPのエラーが発生した場合は空白のページになります。

間違ったSQLクエリーが原因のデータベースエラーの場合は、次のメッセージが表示されます：  
`申し訳ございません。当ショップはメンテナンスのため一時クローズ中です。  
再開まで今しばらくお待ちください。`

＜画像＞

実際のエラーメッセージを表示するには、`開発モード`を使用します。  
`local_conf.php`で下記の設定を使用することで有効化できます：

```php
<?php
define('DEVELOPMENT', true);
```

何か問題が発生した場合、エラーの原因となったファイルへのパスを含むエラーメッセージが表示されます。

＜画像＞

`開発モード`には、アドオン開発者にとって便利な機能がもう一つあります。

CS-Cartは、インストールされたアドオンの`CSS`および`TPL`ファイルを_design/themes_ディレクトリに保存します。通常、アドオンがアンインストールされると、CS-Cartはこれらのファイルを削除します。

また、アドオンが再度インストールされると、これらのファイルは`var/themes_repository`から取得されます。

よって開発中に_design/themes_ディレクトリでアドオンの`.css`ファイルと`.tpl`ファイルを変更し、管理パネルでそのアドオンをアンインストールすると、変更が失われてしまいます。

しかし、`開発モード`を有効にした場合は、アドオンをアンインストールしても、_design/themes_ディレクトリ内のアドオンのファイルは削除されません。

{% hint style="warning" %}
注意

もし`var/themes_repository`に`.css`や`.tpl`ファイルを含んだ状態でアドオンをインストールした場合は`design/themes`内のファイルは`var/themes_repository`の内容で上書されます。
{% endhint %}

### SmartyおよびPHPエラーの画面表示

エラーメッセージを表示するには、PHPやWebサーバーの設定を再定義する必要がある場合もあります。そのため次のコードを`local_conf.php`に追加します。

```php local_conf.php
<?php
error_reporting(E_ALL);
ini_set('display_errors', 'on');
ini_set('display_startup_errors', true);
```

### PHPブロックキャッシングを無効にする

CS-Cartのキャッシュはページの読み込み時間を短縮します。ただし、開発中のプロジェクトでは変更箇所を都度確認する必要があります。その際は、次のコードを`local_conf.php`に追加して、PHPブロックキャッシュを無効にしてください。

```php local_conf.php
<?php
$config['tweaks']['disable_block_cache'] = true;
```

### データベースとホストの設定

データベースの接続情報とストアのHTTPホストを再定義できます。  
これは既存のストアのローカルコピーで作業し、`config.local.php`を変更したくない場合に便利です。  
この機能を利用するには`local_conf.php`でこれらの設定をコメントアウトしてください。

```php local_conf.php
<?php
$config['db_host'] = '%DB_HOST%';
$config['db_name'] = '%DB_NAME%';
$config['db_user'] = '%DB_USER%';
$config['db_password'] = '%DB_PASSWORD%';

$config['http_host'] = '%HTTP_HOST%';
$config['http_path'] = '%HOST_DIR%';

$config['https_host'] = '%HTTPS_HOST%';
$config['https_path'] = '%HOST_DIR%';
```

## キャッシュおよびセッションバックエンド

`local_conf.php`を使用して、ストレージバックエンドなどを再定義できます。  
次のコードから以下の部分をコメントアウトし、設定をしてください。

```php local_conf.php
<?php
$config['cache_backend'] = 'file';
$config['cache_redis_server'] = 'localhost';
$config['cache_redis_global_ttl'] = 0; // キャッシュサイズがRedisサーバーのメモリサイズに達した場合、設定してください
```

{% hint style="info" %}
HINT

キャッシュは、file, sqlite, database, redis, xcache、およびapcが利用可能です。
Sqlite、apc、およびxcacheを使用するには、対応するPHPモジュールをサーバーにインストールする必要があります。
{% endhint %}

セッションのストレージバックエンドを変更することもできます。次のコードの部分をコメントアウトし、目的の設定行ってください。

```php local_conf.php
<?php
$config['session_backend'] = 'database';
$config['session_redis_server'] = 'localhost';
$config['cache_apc_global_ttl'] = 0;
$config['cache_xcache_global_ttl'] = 0;
```

{% hint style="info" %}
HINT

セッションのストレージバックエンドは、databaseとredisが利用可能です。
{% endhint %}