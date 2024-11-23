# Pre\&Post コントローラー

Pre-controllers（プレ・コントローラー）とPost-controllers（ポスト・コントローラー）は 標準のコントローラーが実行された前後に呼び出される（アドオンファイルの構造内での名前や場所に応じて）特別なPHPファイルです。

例:

- 「添付ファイルアドオン」の_/controllers/backendや/controllers/frontend_ディレクトリで次のようなファイルをみつけることができます: `products.post.php`

```text
/app/addons/attachments/controllers/backend/products.post.php
```

これらのディレクトリ内にあるファイルはアドオン専用のコントローラーか標準コントローラー用のpre-/post-controllers どちらかです。

- pre- または post-controller ファイルの名前は以下のような構造(命名規則)となります。:

```http
[controller名].[pre/post].php
```

“products”はCS-Cartのコアコントローラー(標準コントローラー)なので、pre- または post-controllerを宣言することができます。ファイル名最後の「.post」 は、標準の“products”コントローラーが動作した後に実行されることを示しています。

```php products.post.php
<?php
...
if ($mode == 'update') {
    // Assign attachments files for products
    $attachments = fn_get_attachments('product', $_REQUEST['product_id'], 'M', DESCR_SL);

    Registry::set('navigation.tabs.attachments', array (
        'title' => __('attachments'),
        'js' => true
    ));

    Registry::get('view')->assign('attachments', $attachments);
}
```

標準コントローラーの実行前に動作させたい場合は、.postを.preに置き換えて下さい。

- products.**post**.php ・・・ productsコントローラーの後に実行される
- products.**pre**.php ・・・ productsコントローラーの前に実行される