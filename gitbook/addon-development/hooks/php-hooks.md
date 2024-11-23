---
icon: php
---

# PHPフック

PHPコード・フック（フックポイント）は作成したアドオンに処理を引き渡すことができるようにコア内に定義された箇所です。

フックはコードの必要な部分で特別な関数を呼び出すことによって宣言されます:

```php
<?php
fn_set_hook('hook_name', $params, [$param2], [$paramN]);
```

フックはとても柔軟なプログラム・テクニックです。\
1つの関数に複数のフック・ポイントを設定することができます。

CS-Cart公式サイト上の[Hooks base](http://www.cs-cart.com/api)（英語）を利用することでCS-Cartの全てのバージョンで利用可能なPHPフック・ポイントを確認することが出来ます。

＜画像＞

## PHPコード・フックの例

CS-Cartで使用されているPHPコード・フックの例です。\
コードフックは、\_ギフト券アドオン\_が使用している関数 `fn_get_gift_certificate_info()` 内で宣言されています。

```php
<?php
....
fn_set_hook('get_gift_certificate_info', $_certificate, $certificate, $type);
```

## PHPフックはいつ使用されるのか？

フックはコアプログラムのメインフローの途中でアドオンが追加のアクションを実行するために便利な方法を提供します。

フックポイントでアドオンはコア・プログラムのコードの実行を遮ることができ、必要な処理を実行して、メインプログラムに実行プロセスを返します。

特定のフックを呼び出した際には、すべての変数がアドオンに直接渡されます。

アドオン内でのフックの呼び出しは関数名が`fn_ (アドオン名)(アンダーバー)(呼び出されたフックのフルネーム)`という名称で定義することによって実行されます:

```php
<?php
fn_gift_certificates_get_gift_certificate_info($_certificate, $certificate, $type)
```

上記の関数名は、`ギフト券アドオン`の`get_gift_certificate_info` フック内で実行される関数となります。

## PHPフックの使い方

あなたのアドオンにPHPフックを利用するには：

* 先ず使用するフック・ポイントをアドオンの`init.php` ファイル内で宣言します：

```php
<?php
...

fn_register_hooks(
   'get_category_data_pre'
);

  ...
```

* アドオンの`func.php`ファイル内のフックのアクションを作動するための関数を作成します:

```php
<?php

if ( !defined('AREA') ) { die('Access denied'); }

function fn_my_addon_get_category_data_pre($category_id, $field_list, $get_main_pair, $skip_company_condition, $lang_code)
{
   ...
}

?>
```

これだけです！

CS-Cartはアドオンが定義するフック・ポイントでフックが発生した際に処理をアドオンに引き渡します。

## 別のアドオンを他のアドオンで拡張する方法

別のアドオンの処理を拡張する必要がある場合、他のアドオンの中のフックポイントを利用することが可能です。

例えば、\_新着情報とメルマガ\_アドオンは\_SEOアドオン\_を使用します。

\_新着情報とメルマガ\_アドオンのためにSEOアドオンを拡張する必要はなく、SEOアドオンのフックポイントに処理を引き渡すことができます。

_design/backend/templates/addons/news\_and\_emails/addons/seo/hooks/_

動作の原理は他のフックと同様です。

例外としては、\_新着情報とメルマガ\_アドオンからSEOアドオンのフックポイントを利用する場合は、SEOアドオンが有効な場合のみそのフックポイントが利用可能となります。

以下の関数`fn_register_hooks`の三番目の引数はt他のアドオンが有効な場合のみPHPフックを登録するために追加されました：

```php
<?php
fn_register_hooks( array('get_news_data', '', 'seo') )
```

この場合のフックポイントに作用する関数名は`fn_seo_get_news_data`となります。これは`SEOアドオン`が有効な場合のみで呼び出されます。

{% hint style="info" %}
INFORMATION

全てのアクションは「アドオンを他のアドオンで拡張する」セクションに記述されています。
{% endhint %}
