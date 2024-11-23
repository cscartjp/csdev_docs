---
icon: wifi
---

# ajaxリクエストの使用方法

AJAXを使ったリクエストについて説明します。

## CS-CartのAJAX 基本コンセプト

CS-CartはjQueryのAjaxメソッドに独自の拡張を加えて構成されています。\
コードは\_/js/ajax.js\_ ファイルに書かれています。

これらを呼び出す手順は以下の通りです:

1. `submitForm` (form のみ) – フォーム内のデータを抜き出し、送信準備をします。
2. `request` – それぞれのコントローラー（dispatchからパース）にAJAXリクエストを送します。jQueryのajaxメソッドを使用します。
3. `response` – `request` の実行が成功すると呼び出されます。実際のサーバーの応答と結果表示します。
4. `inProgress` – AJAXリクエストが進行しているかどうか
5. `clearCache` – レスポンスのキャッシュをクリア

サーバーでAJAXリクエストが行われている時、AJAXリクエストのサブシステムの初期化は`fn_init_ajax` (_/functions/fn.init.php_) 機能によって実行されます。\
AJAXクラス(_/Tygh/Ajax.php_) のオブジェクトが作られます。

AJAXリクエストの前後に必要な変換を実行し、さらに`response` へ変換するためJSON配列を生成します。これらの\_AJAX\_REQUEST\_ はPHPコードで利用することが出来ます。

PHPファイル（コントローラー）で以下のように\_AJAX\_REQUEST\_ を使用すると、AJAXリクエストが渡された場合のみで実行するコードを書くことが出来ます。

### 例:

```php
<?php
if (defined('AJAX_REQUEST')) 
{
  fn_set_notification('E', fn_get_lang_var('warning'), $msg, true, 'insecure_password');
}
```

AJAXリクエストのスクリプトが完了、または終了関数`exit()` が明示的に呼び出されると、\
AJAXクラス(/Tygh/Ajax.php) のディストラクタが実行されます。\
ディストラクターはJSON形式のデータ配列にドキュメントに表示されるべき情報を渡し、その後、Javascriptの関数 `response` (_/js/tygh/ajax.js_) に配列を渡します。

コントローラーは以下の形式でレスポンスを返します。

```json
data:
    notifications:
        id:
            text
            title
        id:
            text
            title
        ...
    html:
        block_id:
            block_content
        block_id:
            block_content
        ...
```

## マイクロフォーマットでAJAXを呼び出す

AJAXリクエストはリンクやフォームを使って送信することができます。どちらの場合もマイクロフォーマット`cm-ajax` はそれぞれの要素に指定する必要があります。

***

## フォームの場合

フォームに`cm-ajax` が書かれている場合、そのコンテンツはAJAXリクエストとしてアクション属性に定義されたURLに送信されます。

そのようなフォームには`result_ids` と呼ばれる非表示の入力要素があります。\
それにはAJAXデータで更新される要素の識別名が含まれていなければなりません。

AJAXフォームの例

```html
<!-- cm-ajaxクラスがついたフォームはindex.phpにAJAXリクエストとして送信されます -->
<form class="cm-ajax" action="index.php" method="post" name="product_form_817">
 
<!-- 更新すべきHTMLエレメントのIDを値とするHiddenフィールド -->
    <input type="hidden" name="result_ids" value="my_id" />
    ...
 
<!-- このボタンが押された場合、index.phpにAJAXリクエストが送信されます -->
    <input id="button_cart_817" type="submit" name="dispatch[checkout.add..817]" value="Add to cart" />
    ...
</form>
```

## リンクの場合

リンクがクリックされた際、`cm-ajax` の書かれたリンクはhref属性で定義されたURLにAJAXリクエストを送信します。

更新する要素のIDは`rev` 属性で指定する必要があります。

AJAXリンクの例:

```php
<a class="cm-ajax" rev="content_usergroups" href="{"profiles.request_usergroup?usergroup_id=`$usergroup.usergroup_id`&amp;status=`$ug_status`"|fn_url}">{$_link_text}</a>
```

***

## その他のマイクロフォーマット

マイクロフォーマット`cm-ajax` はAJAXフォームやリンクのため必要不可欠です。\
CS-Cartには開発者がAJAXを管理出来るるたくさんのオプションマイクロフォーマットがあります。

## AJAXリクエストを直接使用する

明示的にメソッドを呼び出すことはCS-Cartのコーディングガイドラインでは推奨されていません。

すべてのJavascript関数は別々のファイルに書き、CSSセレクタ(id,やclass など)を使用して、HTML要素に割り当てる必要があります。

しかしながら、AJAXリクエストは`$.ceAjax()` メソッドは直接インライン・コールを送ることができます:

```html
<input id="enable_block_1" type="checkbox" name="enable_block_1" value="Y" onclick="$.ceAjax('request', '{$index_script}?dispatch=block_manager.enable_disable&amp;block_id=1&amp;enable=' + (this.checked ? this.value : 'N'), {literal}{method: 'POST', cache: false}{/literal});" />
```

## AJAXを介したコンテナのリロード方法

CS-Cartでは以下のように、コンテナのコンテンツを動的にリロードできます

* `cm-ajax`クラスとその`result_ids`構成部分を含むフォームを送信します（microformatsに関する記事を参照）。
* `cm-ajax`クラスと`data-ca-target-id`属性のリンクを使用します（microformatsに関する記事を参照）。
* `$.ceAjax()`でカスタムリクエストを作成します。
* `id`は開始タグの最後の属性でなければなりません。
* 「マジックコメント」`id`は、終了タグの前にある必要があります。

```html
<div class="foo" data-ca-bar="bar" id="foobar">
    このコンテンツはAJAXでリロードされます
<!--foobar--></div>
```
