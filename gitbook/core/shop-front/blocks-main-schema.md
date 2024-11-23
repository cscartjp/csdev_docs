---
icon: cubes
---

# blockメインスキーマ—

メインスキーマは、レイアウトエディタで使用可能なブロックについて記述するフォーマットです。

```php
<?php
$schema = array(
    // ブロックタイプのユニークな識別子 => 配列
    'unique_block_type' => array(
        'settings' => array(),
        'templates' => array(),
        'cache' => array(),
        ...
    ),
);
```

## ブロックのパラメーター

### settings (array)

ブロックの設定の配列（settings）は、ブロックの編集で指定することができる設定を含みます。\
`（デザイン→レイアウト→ブロックオプション）`

設定は`key` – `value`のペアのリストです。`key`は設定の識別子として機能し、`value`は設定の種類とその動作を記述する配列です。keyには、ブロック編集ページで設定の名前を定義するために対応する言語変数が必要です。

ブロックの設定（settings）は次のようになります。

```php
'settings' => array(
    'ip_address' => array(
     'type' => 'text',
            'default_value' => '127.0.0.1',
    ),
    ...
),
```

## 利用可能なパラメーター

* `type` （string） - 次の設定タイプのいずれか：
  * `checkbox`—チェックボックス
  * `input`—入力フィールド
  * `selectbox`— selectフィールド
  * `input_long`— `input-text-long`クラスを持つ入力フィールド
  * `multiple_checkboxes`—複数選択可能なチェックボックス
  * `text`—WYSIWYGエディタ付きのテキスト入力フィールド
  * `simple_text`— textarea
  * `picker`—ピッカー
  * `template`—指定したテンプレートが表示されます
  * `enum`—オブジェクトの羅列（コンテンツセクションでのみ使用）
* `required`— (bool) 正の場合、必須項目となります。
* `option_name`— (string) 設定の言語変数の名前。名前が指定されていない場合は、代わりに設定の識別子（ID）が使用されます。
* `default_value`— (mixed) 設定のデフォルト値
* `values`— (array) `selectbox`や`multiple_checkboxes`などの設定に使用するオプションのリスト\
  `key - value`の形式の配列。`key`はデータベースに保存されているオプションの名前です。`value`はオプションの名前を格納した言語変数名。`no_lang`がtrueの場合、オプションの実際の名前となります。
* `no_lang`— (bool) trueの場合、オプションの値のリストが管理パネルにそのまま表示されます。falseの場合、その値は言語変数として扱われます。
* `values_settings`— (array) trueの場合、オプションの値のリストが管理パネルにそのまま表示されます。falseの場合、その値は言語変数として扱われます。
* `values_settings`— (array)このパラメーターは、親リストの特定のオプションが選択された場合に表示されるオプションおよびその他の設定のリストを指定します。これはselectboxタイプでのみ機能します。

## values\_settingsの使用例

```php
'settings' => array(
    'foo' => array(
        'type' => 'selectbox',
        'values' => array (
            'products' => 'products',
        ),
        'values_settings' => array(
            'products' => array(
                'settings' => array(
                    'rss_sort_by' => array(
                        'type' => 'selectbox',
                        'values' => array(
                            'A' => 'rss_created',
                            'U' => 'rss_updated'
                        ),
                    ),
                    'rss_display_sku' => array (
                        'type' => 'checkbox',
                    ),
                )
            )
        )
    )
);
```

* `picker`— (string) ピッカーのテンプレートへのパス。pickerタイプでのみで使用します。
* `picker_params`— (array) ピッカーの設定。pickerタイプでのみで使用します。
* `remove_indent`— (bool) trueの場合、管理パネルの右側にインデントが設定されません。
* `template`— (string) テンプレートへのパス。templateタイプでのみ使用します。

### templates (array)

templates配列は、テンプレートのリストが含まれています。

```php
'テンプレートへのパス' => array (
    'settings' => '設定のリスト',
    'fillings' => 'テンプレートで使用可能な内容の配列。他の内容はリストから自動的に除外されます。 ',
    'params' => 'パラメーターの配列',
    'bulk_modifier' => '出力前のブロックのすべての構成部分に適用される関数名'
),
```

スキーマが生成される`template`とブロックスキーマのパラメーターに含まれるすべての情報が`templates.php`で指定されたパラメーターに追加されます。テンプレートへのパスがキーとして機能します。

ブロックスキーマの`template`パラメーターには、次のものが含まれます。

`A.`すべてのパラメーターを含むテンプレートへのパスのリスト-この方法では、templates.phpファイルからの情報は必要ありません。

`B.`テンプレートへのパスのリスト—対応するキーのすべてのパラメーターは、templates.phpファイルで指定されます。

`C.`テンプレートを含むディレクトリへのパス—対応するキーのすべてのパラメーターは、templates.phpファイルで指定されます。

`D.`テンプレートのリストを返す関数の名前—対応するキーのすべてのパラメーターは、templates.phpファイルで指定されるか、関数によって返されます。

### wrappers (array)

`wrappers`の配列は、wrappersのリストまたはwrappersのフォルダへのパスを含むことができます。wrappersには追加のパラメーターはありません。

### content (array)

ブロックには、テンプレートに渡される変数をいくつでも含めることができます。\
例として次のようなブロックがあります。

```php
'test_block' => array (
    'content' => array(
        'some_value' => array(
            'type' => 'text',
        )
    )
)
```

管理パネルのブロック設定には、テキスト入力フィールドが含まれます。カスタマーエリアでは、このブロックのテンプレートに`{$some_value}`変数があり、管理パネルで指定できます。

配列`content`には、任意の設定（上記を参照）、enumeration、または関数を指定できます。

設定はシンプルに機能します。ユーザーは管理パネルでそれらを指定し、テンプレートに渡されます。

例えば、製品やカテゴリのリストなどの異なるフィリングを持つ構成部分のリストを定義する列挙型（enum）設定タイプの使用。

以下の列挙型パラメーターのリストを参照してください。

```php
'変数名' => array (
     'type' => 'enum',
     'object' => 'スキーマ内の動的オブジェクトの名前'
     'items_function' => 'アイテム生成する関数'
     'fillings' => array ( // Filling タイプ.
         'manually' => array ( // 手動 filling. ピッカーパラメータでは必要です.
             'picker' => 'pickers/companies/picker.tpl',
             'picker_params' => array (
                 'multiple' => true,
             ),
         ),
         'some_other_filling' => array (
             'params' => array (

             ),
         ),
     ),
 ),
```

`params`セクションを作成して、非manual fillingのパラメーターを保存できます。これらのパラメーターは、アイテムを生成する関数に渡されます。テンプレートまたはブロックに対して行うように、fillingの設定を指定することもできます。

関数を`content`の構成部分として使用する場合、関数によって返される結果はこの変数の値になります。

```php
'変数名' => array (
     'type' => 'function',
     'function' => array('fn_get_languages'[, 'param1'][, 'param2'][..]),
 ),
```

### cache (bool/array)

`cache` （bool / array）はブロックの一般的なキャッシュ設定を含みます。パラメーターが`false`に設定されている、もしくは指定されていない場合は、現在のパラメーターの`cache_overrides_by_dispatch`セクションにエントリがない場合、ブロックはキャッシュされません。

cacheで利用可能な値

* `false` （string） - ブロックはキャッシュされません。
* `true` （string） - ブロックは、`block_cache_properties`スキームの設定に従ってキャッシュされます。
* `array` （array） - ブロックのキャッシュ設定を含む配列。この配列で指定されたパラメーターは、`block_cache_properties`スキームからグローバルブロックキャッシュ設定に追加されます。

cacheで利用可能な値

`cache`配列のこれらのパラメーターは、ブロックのキャッシュ方法を指定します。特に指定のない限り、パラメーターは不要です。

* `update_handlers` （配列） - ブロックのキャッシュを無効にするデータベーステーブルのリスト（プレフィックスなし）。データベースを操作するための機能や方法など、CS-Cartツールを使用して、エントリの追加、編集、削除、テーブルの構造の変更を意味します。

たとえば、ユーザーのリストを表示するブロックには、`update_handlers`としてテーブル`users`と`user_profiles`テーブルが必要です。

キャッシュにエントリキーを生成するとき、このブロックに対して異なる変数とパラメーターのシリアル化された値を使用できます。以下に説明する配列内の必須パラメーターをリストします。

* `request_handlers` （array） - HTTPリクエストパラメーター（`$_REQUEST`配列内のキー）の名前のリスト

たとえば、`category_id`および`sort_by`パラメータを指定すると`...|category_id=10|sort_by=price;`が追加されます。これにより、これらのパラメータの値の組み合わせごとにキャッシュ内の異なるエントリを使用できます。

* `session_handlers` （array） - ユーザーのセッション内の変数の名前のリスト（配列内のキー`$_SESSION`）

たとえば、`items_per_page`でパラメータを指定すると、すべての値に対して個別のエントリがキャッシュに存在します`$_SESSION['items_per_page']`。

* `cookie_handlers` （array） - ユーザーのCookieのパラメーター名のリスト（配列によって返されるキー）。`fn_get_session data()`
* `auth_handlers` （array） - 配列内のキーのリスト `$_SESSION['auth']`

`request_handlers`、`session_handlers`、`cookie_handlers`、および`auth_handlers`の値を入力するには、特別なフォームを使用します。

* ネストされた要素にアクセスするには、ドットシンタックスを使用します。\
  例）`$_SESSION['auth']['user_id']`にアクセスする場合は`'session_handlers' => array('auth.user_id')`となります。
* すべての値を`\`\*で選択します。配列内のすべての値を選択します。キャッシュキーが生成されると、シリアル化された配列全体が使用されます。\
  `'session_handlers' => array('*')`
* `比較演算子`

```php
'auth_handlers' => array(
    'user_id' => array('gt', 0),
),
```

上記の例は、$\_SESSION\['auth']\['user\_id']を選択してキャッシュキーに追加しますが、値が0より大きい場合のみ適用されます。

`利用可能な比較演算子`：

* `gt` - より大きい（＞）
* `eq` - 等しい（＝）
* `neq` - 等しくない（≠）
* `lte` - 以下（＜＝）
* `lt` - 未満（＜）
* `gte` - 以上（＞＝）
* `cont` - 文字列を含む
* `ncont` - 文字列を含まない
* `in` - 配列内に存在する
* `nin` - 配列内に存在しない

cacheキーには、呼び出された関数とメソッドの結果も含めることができます。

* `callable_handlers` （array） - パラメーター名と対応する関数のリスト。呼び出された関数の結果は、これらのパラメーターの値として使用されます。

```php
'callable_handlers' => array(
    'currency' => array('fn_get_secondary_currency'),
),
```

上記のエントリ`|currency=RUB`はキャッシュキーに追加されます。\
これは呼び出される関数を記述する方法です。\
`array(Callable[, Args])`

* `Callable`は、関数の名前を含む文字列、または呼び出すことができる式です。\
  `call_user_func()`
* `Args`は、関数に渡される引数をリストする配列です。引数で始まる文字列の場合`$`、グローバル変数（変数の名前として扱われます`$_REQUEST`）または空の変数の一つ`$block_schema`と`$block_data`。
* `$block_schema` —ブロックのスキーマが含まれています
* `$block_data` —データベースのブロックに関する情報が含まれています

例：

```php
'callable_handlers' => array(
    'layout' => array('fn_get_products_layout', array('$_REQUEST')),
    'settings' => array('fn_foo_addon_cache_key_handlers', array('$block_data')),
    ),
```

上記のコードは、呼び出されたすべての関数の結果をキャッシュ内のエントリキーに追加します。

* `disable_cache_when` （array）—このブロックのキャッシュを無効にするルールを指定します。`request_handlers`、`session_handlers`、`cookie_handlers`、`auth_handlers`と`callable_handlers`をキャッシュ設定]セクションのように同じ形式で、これらのパラメータは異なる動作します。

```php
'request_handlers' => array('sort_by', 'items_per_page'),
    'auth_handlers' => array(
        'user_id' => array('gt', 0)
    ),
    'disable_cache_when' => array(
        'request_handlers' => array('sort_by', 'items_per_page'),
        'auth_handlers' => array(
            'user_id' => array('gt', 0)
        ),
    ),
),
```

`cache`と`disable_cache_when`セクションのパラメーターの動作についての違いは次のとおりです。

o cache: the values of $\_REQUEST\['sort\_by'] and $\_REQUEST\['items\_per\_page'] will be serialized and added to the cache key of the block. The value $auth\['user\_id'] will be serialized and added to the key only if the value is greater than 0

* `cache`：`$_REQUEST['sort_by']`および`$_REQUEST['items_per_page']`の値はシリアル化され、ブロックのキャッシュキーに追加されます。`$auth['user_id']`がシリアル化され、値が0より大きい場合にのみキーに追加されます。
* `cache.disable_cache_when`：`$_REQUEST`配列に`sort_by`があり`items_per_page`場合、ブロックはキャッシュしません。また`$auth`配列の`user_id`の値が0より場合、ブロックもキャッシュしません。`callable_handlers`のパラメータもで動作が異なります。

`cache.disable_cache_when`セクション：trueの場合、ブロックはしません。キャッシュ、および他の方法でラウンド。

* `regenerate_cache_when` （array） —このブロックのキャッシュ無効化のルールを記述します。 cache.disable\_cache\_whenと同じように機能します。
* `cache_overrides_by_dispatch` （配列） -個別のディスパッチごとにブロックキャッシュのパラメーターを記述します。`array('dispatch' => cache_params, ...)`

cache\_paramsの配列が指定されたディスパッチのパラメータをキャッシュします。\
配列にcurrentのエントリがある場合、dispatchキャッシュパラメーターは、キャッシュセクションの一般的なブロックキャッシュ設定からではなく、このエントリから取得されます。のキャッシュパラメータのネストされた配列はすべてdispatch、キャッシュセクションと同じパラメータを使用できます。

例：

```php
'cache' => array(
    // これらのキャッシュパラメーターは、カテゴリページを除くすべての場所で使用されます
    'update_handlers' => array('users'),
),
'cache_overrides_by_dispatch' => array(
    // これらのキャッシュパラメーターはカテゴリページで使用されます
    'categories.view' => array(
        'update_handlers' => array('users', 'products'),
    ),
),
```

* `hide_on_locations` （配列） -ブロックを使用できないディスパッチのリスト。たとえば、次のコードを使用して、カートページにブロックを追加することを禁止します。

```php
'hide_on_locations' => array('checkout.cart'),
```

* `single_for_location` （bool） — `true`場合、すべてのディスパッチはこの種類のブロックを1つだけ持つことができます。このパラメーターが指定されていない場合、falseとして解釈されます。
* `multilanguage` （bool） —このブロックが多言語サポートを必要とするかどうかを指定します。trueの場合、ブロックのコンテンツは多言語をサポートします。このパラメーターが指定されていない場合、falseと解釈されます。
