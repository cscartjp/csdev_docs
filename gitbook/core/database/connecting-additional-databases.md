---
icon: diagram-subtask
---

# 他のデータベースへの接続

場合によっては複製やバックアップ、その他の目的のため外部のデータを取得するために追加のデータベース接続が必要になります。CS-Cartには標準とは別のデータベースホストへの接続を確立する便利なディベロッパーメソッドがあり、コアで厳密に定義されています。

## db\_initiate :

**外部ホスト** のデータベースへの接続を確立するためにはこのメソッドを使います。

```php
<?php
db_initiate($host, $user, $password, $name, $params = array());
```

### パラメーター

* `$host` – DBホスト
* `$user` – DBユーザー
* `$password` – DBパスワード
* `$name` – DB名
* `$params` - 追加の接続パラメーターの配列
  * `dbc_name` - ターゲットのデータベースを決めるためのローカル名
  * `table_prefix` – ターゲットのデータベース名の前に付ける。 プレイスホルダー`?:`によって置き換えられます。

## db\_connect\_to :

```php
<?php
db_connect_to($params, $name);
```

_パラメーター_

* `$name` – DB名
* `$params` - 追加の接続パラメーターの配列
  * `dbc_name` - ターゲットのDBを決めるためのローカル名
  * `[table_prefix]` – ターゲットのDB名の前に付ける。 プレイスホルダー`?:`によって置き換えられます。`db_initiate`メソッドで`$params`に使用されるデフォルト値`table_prefix`

これらの関数を呼び出した後、リクエストは新しいデータベースに対して処理されます。

例:

```php
<?php
$params = array(
	'dbc_name' => 'backup',
	'table_prefix' => 'cscart'
);
db_initiate('localhost', 'db_user', 'db_password', 'cscart_backup', $params);
db_connect_to($params, 'cscart_backup');
 
$data = db_get_array("SELECT * FROM ?:products");
```

この例では、`$data`には、ローカルホスト上のデータベース`cscart_backup`内の `cscart_products`テーブルからのデータを含んでいます。
