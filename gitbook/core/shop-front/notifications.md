---
icon: bullhorn
---

# 通知システム

CS-Cartの通知は画面右上隅にポップアップして表示されます。

標準で5つの通知タイプがあります：

* `通知` (code type: N)
* `エラー` (code type: E)
* `警告` (code type: W)
* `チェックアウト時のエラー` (code type: O)
* `情報` (code type: I)

それぞれのタイプには色がつけられています：\
`通知`は緑、`エラー`は赤、`警告`はオレンジ、`チェックアウト時のエラー`はオレンジで右上隅に表示され、`情報`はポップアップのダイアログで表示されます。

＜画像＞

表示フォーマットは通知が呼び出されるエリアのCSSファイルに定義されています。\
通知は関数`fn_set_notification` (_/functions/fn.common.php_) を使用して生成されます。

空のコンテナはそれぞれのページのHTMLに書かれています。\
通知行われると`通知ブロック`はこのコンテナに表示されます。

通知は以下のレンプレートに定義されています：

* 管理パネル: `/backend/templates/common/notification.tpl`
* ショップフロント: `/themes/[theme name]/templates/common/notification.tpl`

## 通知の設定方法

通知には2つの方法があります：

* スクリプトの実行によってページがロードされる前に通知を表示される
* AJAXリクエストと共に、ページが表示された後および特定場合に表示される

1つ目はページがリロードされた際に表示される通知を作成するために使用します。\
2つ目はページをリロードすることなく、いくつかのJavascriptのイベントによって通知する際に使用します。

### まず1つ目の通知の生成過程を見てみましょう：

通知の関数はコントローラーコードから呼び出されます。例えば:

```php
<?php
if ($mode == 'add_to_cart') {
    fn_set_notification('E', fn_get_lang_var('warning'), $msg, true, 'insecure_password');
}
```

`fn_set_notification()` 関数は新しい通知内容を通知用の配列に追加します。

* テンプレーターは\_/backend/templates/common/notification.tpl や/themes/\[theme name]/templates/common/notification.tpl\_ を処理し、`fn_get_notifications()`を介して、それぞれの配列へすべての通知のリストを渡します。
* 通知用の配列が空でなかった場合、通知のHTMLコードが表示されます。

\`\*

### 2つ目はAJAXを使った通知の生成方法です

* ページを表示する際、テンプレーターは`/backend/templates/common/notification.tpl` や`/themes/[theme name]/templates/common/notification.tpl` を処理し、通知用のコンテナ(`<div class=”cm-notification-container”>`)を作成します。このコンテナは空かまたは1つ目の通知設定を含んでいます。
* 設定用関数はAJAXリクエストで実行されるコントローラーコードで呼び出されます。　例えば:

```php
<?php
if (defined('AJAX_REQUEST')) {
    fn_set_notification('E', fn_get_lang_var('warning'), $msg, true, 'insecure_password');
}
```

この関数は以下の場合、通知用配列に新しい要素を追加します。

* コントローラーの実行もしくは`exit()` 関数で処理が明示的に中断された場合、(/Tygh/Ajax.php) のAJAXクラス用のデストラクタが起動します。このデストラクタは通知内容をJSON形式の配列に変換し、この配列をJS関数`response` (_/js/tygh/ajax.js_) に引き渡します。
* この関数`response` は関数`ceNotification` (_/js/tygh/core.js_) を呼び出し、すべての定義されたAJAX通知配列が渡されます。通知の表示は`notification.append` メソッドにより`ceNotification` 関数で実行されます。

関数は通知のHTMLコードが宣言されたテンプレート`/backend/templates/common/notification.tpl` または `/themes/[theme name]/templates/common/notification.tpl` (`<div class=”cm-notification-container”>`)をコンテナに追加します。また、Javascriptの通知の作成を行います。

```javascript
$.ceNotification('show', {
    type: 'E',
    title: _.error,
    message: error_msg
});
```
