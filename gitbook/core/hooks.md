---
icon: wand-magic-sparkles
description: Hooks
---

# Hooks

## 一般情報

1. フックは、開発者が機能を拡張するために使用します。そのため、可能な限り多くのパラメーターをフックに含めることを推奨します。
2. フックの名前には、関数の名前を含める必要があります。
3. プレフィックスは禁止されています。  サフィックス（接尾辞）のみが許可されます。
4. 多数のフックを含む複雑な関数がある場合、次の例を使用してフックに名前を付けることができます。

### PHP関数:

```php
<?php
function get_cart_product_data(){

}
```

### Hook:

```php
<?php
fn_set_hook （'get_cart_product_data_post_options' 、 $ product [ 'product_id' ]、 $ _pdata 、 $ product ）;
```

5. フックのパラメーターをリストするときは、常に関数が受け入れるパラメーターを最初に配置してください。SQLフックは唯一の例外は変数`params`です。必要なものはすべて変数に含まれています。
6. クラスのフックには、名前に呼び出し元クラスの名前が含まれている必要があります。
7. クラスのフックは、常に最初のパラメーターとしてクラスインスタンスを渡す必要があります。

```php
<?php

class Patterns
{
    public function save($style_id, $style, $uploaded_data)
    {
        ...

        /**
         * アップロードされたパターンファイルを保存する前に実行され、アップロードされたファイルとその場所を変更できます。
         *
         * @param \Tygh\Themes\Patterns $this          Patterns instance
         * @param string                $style_id      Style name
         * @param array                 $style         Style data
         * @param array                 $uploaded_data Uploaded files
         * @param string                $path          Path where patterns will be saved
         * @param string                $rel_path      Relative patterns path
         */
        fn_set_hook('patterns_save', $this, $style_id, $style, $uploaded_data, $path, $rel_path);

        ...
```

## フックを追加する方法と場所

各関数には少なくとも2つのフックが含まれている必要があると想定しています。

1. 関数の先頭にある`get_product_name_pre`ようなプレ・フック。関数が受け入るすべてのパラメーターは、このフックに渡される必要があります。
2. 関数の終わりにある`get_product_name_post`のようなポスト・フック。パラメーターを次の順序でリストします。「関数によって受け入れられたすべてのパラメーター」、次に「関数によって返されたパラメーター」、次に「他の補助的パラメーター」。 また、他の追加のフックがあるかもしれません：
3. `get_product_name`のようなSQLフック。すべてのSQL変数はこのフックに渡されます。パラメーターは、`$fields`、`$condition`、`$sorting`、`$limit`などすべて記述することをお勧めします。不要な場合は、空のままにしておくことができます。例：

```php
<?php

fn_set_hook('get_product_name', $product_id, $lang_code, $as_array, $field_list, $join, $condition);
```

もう一つの例。例として、商品データの選択の関数を使用します。

```php
<?php

fn_set_hook('get_products_before_select', $params, $join, $condition, $u_condition, $inventory_join_cond, $sortings, $total, $items_per_page, $lang_code, $having);
```

一部のフックはこれらの標準に準拠していません。この場合、次のようなコメントが追加されています。

```php
<?php

/ ** 
*非推奨：このフックはバージョン4.xまたは3.3.xで削除されます。
* 代わりにget_product_price_preを使用してください。
* /
```

次に、準拠するフックも追加します。この場合は、`get_product_price_pre`となります。

{% hint style="info" %}
HINT

関数に新しいパラメーターを追加するとき、古いフックを廃止して新しいフックを追加することはありません。代わりに、既存のフックの最後にこのパラメーターを追加します。この場合、パラメーターの正しい順序は無視できます。
{% endhint %}

## フックの標準形式

### PHPのフックと関数

コメントの形式は、PHPDocumentorに準拠します。この形式は、どこでもすべての関数に適用されます。

Doxygenは、これらのドキュメントを生成するために使用されます（[マニュアル](http://www.doxygen.jp/)）。

コメントの書き方：主なルールと推奨事項：

1. コメントは大文字で始まります。コメントの最後にピリオドはありません。
2. 関数の目的を説明する動詞は、三人称単数形で使用されます。たとえば、Gets user dataなどです。
3. 変数の名前と値、ファイルパス、ファイル名、その他の固有名詞を斜体で強調表示します（HTMLタグで使用）。例：Function _foo_(in _foo/bar/functions_) accepts parameter _$bar_
4. 以下の説明では、`class::function`関数は次のように定義されています。クラスのない関数は`::function`として定義されます。例：

```php
<?php

/** ...
*      - period - If defined, get pages by time period. ::fn_create_periods</li>
* ...
*/
```

5. 変数を記述して、この関数で変数が使用される理由を明確にします。

例：

```php
<?php

/**
 * Processes cart data after calculating all prices and other data (taxes, shippings etc)
 *
 * @param array  $cart               Cart data
 * @param array  $cart_products      Cart products
 * @param array  $auth               Auth data
 * @param string $calculate_shipping // 1-letter flag
 *      A - calculate all available methods
 *      E - calculate selected methods only (from cart[shipping])
 *      S - skip calculation
 * @param bool $calculate_taxes       Flag determines if taxes should be calculated
 * @param bool $apply_cart_promotions Flag determines if promotions should be applied to the cart
 */
fn_set_hook('calculate_cart', $cart, $cart_products, $auth, $calculate_shipping, $calculate_taxes, $apply_cart_promotions);
?>

<?php

/**
 * Change SQL parameters for product data select
 *
 * @param int $product_id Product ID
 * @param string $field_list List of fields for retrieving
 * @param string $join String with the complete JOIN information (JOIN type, tables and fields) for an SQL-query
 * @param mixed $auth Array with authorization data
 * @param string $lang_code Two-letter language code (e.g. 'en', 'ru', etc.)
 * @param string $condition Condition for selecting product data
 */
fn_set_hook('get_product_data', $product_id, $field_list, $join, $auth, $lang_code, $condition);
?>
```

{% hint style="info" %}
HINT

コメントは、フックの直前に配置する必要があります。
{% endhint %}

### TPL（テンプレート）フック

Smarty テンプレートの場合

```smarty
{** Dynamic menu item (on the navigation) *}
{hook name="index:dynamic_menu_item"}
...
{/hook}

{** Hooks for CSS styles *}
{hook name="index:styles"}{/hook}
```

{% hint style="info" %}
HINT

コメントの開始タグは、常に2つのアスタリスクと終了タグ（1つのアスタリスク）を使用する必要があります。このように、フックのコメントは通常のコメントと区別されます。
{% endhint %}

### JS（Javascript）フック

例：

```javascript
/**
 * Hook の説明
 */
var hook_data = {
    'append_obj_content': append_obj_content, // int Id of bla bla
    'var_prefix': prefix, // string Prefix of var
    'object_html': unescape(append_obj.html()), // string Object
    'var_id': id, // int ID of var
    'item_id': js_items[id] // int Item ID
};

$.ceEvent('trigger', 'ce.picker_add_js_item', [hook_data]);
```

最初に、イベントの説明を含むコメント、2番目はパラメーターを持つオブジェクトの変数、3番目にイベントの呼び出しを行います。

渡されたパラメーターへのコメントでは、最初の単語は変数のタイプであり、残りは説明です。
