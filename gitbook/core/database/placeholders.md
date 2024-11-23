---
icon: pen-field
---

# データベース・プレイスホルダー

## ?u

データを更新するための構造持つ配列を格納します:

```php
<?php
$data = array (
    'payment_id' => 5
    );
$order_id = 3;
 
db_query('UPDATE ?:orders SET ?u WHERE order_id = ?i', $data, $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
UPDATE cscart_orders SET payment_id = '5' WHERE order_id = 3;
```

## ?e

データを挿入するための構造持つ配列を格納します:

```php
<?php
$data = array (
    'payment_id' => 5,
    'order_id' => 3
);
 
db_query('INSERT INTO ?:orders ?e', $data);
```

上記PHPコードは以下のSQLとなります。

```mysql
INSERT INTO cscart_orders (payment_id, order_id) VALUES ('5', '3');
```

## ?i

文字列や数値を受け取り、整数に変換します

```php
<?php
$order_id = 4;
db_query('SELECT * FROM ?:orders WHERE order_id = ?i', $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE order_id = 4;
```

## ?s

文字列や数値を（スラッシュを追加して）文字列に変換します

```php
<?php
$order_id = 'adasd';
db_query('SELECT * FROM ?:orders WHERE order_id = ?s', $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE order_id = 'foo';
```

## ?l

受け取ったデータをLIKEオペレーターに置換するために文字列に変換します(ダブルバックスラッシュに変換されスラッシュが追加されます)

```php
<?php
$piece = '%black\white%';
db_query('SELECT * FROM ?:product_descriptions WHERE product LIKE ?l', $piece);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_product_descriptions WHERE product LIKE '%black\\\\white%';
```

## ?d

データをデシマル（分数）に変換する

```php
<?php
$order_id = '123.345345';
db_query('SELECT * FROM ?:orders WHERE order_id = ?d', $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE order_id = '123.35';
```

## ?a

prepares data to be used in the structure IN () as a set of strings, receives a string, number, array:\
文字列や数値、配列をIN ()で使えるデータに変換します

```php
<?php
$order_id = '123';
db_query('SELECT * FROM ?:orders WHERE order_id IN (?a)', $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE order_id IN ('123');
```

## ?n

文字列や数値、配列を`IN()`で使えるデータに変換します

```php
<?php
$order_id = '123.45';
db_query('SELECT * FROM ?:orders WHERE order_id IN (?n)', $order_id);
```

## ?p

準備された値の挿入:

```php
<?php
$order_id = 'order_id = 4';
db_query('SELECT * FROM ?:orders WHERE ?p', $order_id);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE order_id = 4;
```

## ?w

WHERE句で使えるデータを格納します

```php
<?php
$data = array (
    'payment_id' => 5,
    'order_id' => 3
);
 
db_query('SELECT * FROM ?:orders WHERE ?w', $data);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * cscart_orders WHERE payment_id = '5' AND order_id = '3';
```

**Operators**\
`?w` プレースホルダーは次のオペレーターをサポートしています: `=`, `!=`, `>`, `<`, `<=`, `>=`, `<>`, `LIKE`, `NOT LIKE`, `IN`, `NOT IN`, `NULL`

配列の例

```php
<?php
$data = array (
    field => value,
    array(field, operator, value)
);
```

* `field` テーブルのフィールド名
* `value` 値の条件
* `operator` 条件のオペレーター

## ?f

変数の値が有効なフィールド名かチェックします

```php
<?php
$data = 'payment@id';
 
db_query('SELECT * FROM ?:orders WHERE ?f = 5', $data);
```

上記PHPコードは以下のSQLとなります。

```mysql
SELECT * FROM cscart_orders WHERE  = 5;
```

## ?m

マルチインサートに対応する配列を格納します。

```php
<?php
$data = array(
    array(
        'payment_id' => 5,
        'order_id' => 3
    ),
    array(
        'payment_id' => 5,
        'order_id' => 4
    ),
);
 
db_query('INSERT INTO ?:orders ?m', $data);
```

上記PHPコードは以下のSQLとなります。

```mysql
INSERT INTO cscart_orders (payment_id, order_id) VALUES ('5', '3'),('5', '4');
```
