---
icon: soap
---

# マイクロフォーマット Microformats

マイクロフォーマットとは、特別なCSSセレクタです。Javascriptのコードが特定のイベントを実行する際にこのマイクロフォーマットが適用されている要素に対して処理が行われます。

マイクロフォーマットには以下のような命名規則があります。 （独自の新しいマイクロフォーマットを定義する際に重要となります）：`cm-new-microformat-name`

プレフィックスは、`cm-` (CS-Cart Microformatの略)となり、このCSSスタイルがマイクロフォーマットであることを示します。

標準のCSSスタイルとは異なり、マイクロフォーマットの記述はCSSファイルではなく、Javascriptファイルに記述されます。

マイクロフォーマットを処理するJavascriptコードを検索するには\_/js/\_ディレクトリ内の全てのファイルの文字列`.hasClass('cm-microformat-name')`を検索をする必要があります。

## フォーム

### フィールドの検証

使用法:

```html
<label class="cm-email cm-required" for="elm_id">Field name:</label>
<input type="text" name="test" value="Y" id="elm_id" />
```

パラメーターにより確認された要素に割り当てられたラベル要素の`for`は、フォームフィールドのバリデーションで必要となります。

フィールドのバリデーション用のマイクロフォーマットは`label`要素のクラス属性として定義されており、クラスは複数のバリデーションルールを組み合わせることができます。

間違って入力されたフィールド用にエラーメッセージを使用できるようにするために、ラベルが非表示の場合であってもマイクロフォーマットの記述が必要です。

これらのエラーメッセージでは、以下のプレースホルダーを使用することが出来ます：

### `form_message(msg, lbl, null, extra)`

\*\* \[field]\*\* パラメーターが一つエラーメッセージ作成の関数（_form\_message()_）に渡されると、ラベルの説明文に置き換えられます。\
例えば：

```javascript
var msg = 'The field [field] is not valid';
this.form_message(msg, lbl);
```

パラメーターは、2つまでエラーメッセージ作成関数に渡すことが出来ます。

例:

```javascript
var msg = 'The field [field1] do not match field [field2]';
this.form_message(msg, lbl1, lbl2);
```

**\[extra]** エラーメッセージに表示される追加のデータを指定します。

```javascript
var msg = 'The field [field] should be set to the following format: [extra]';
var extra = '1AB-CF5';
this.form_message(msg, lbl, null, extra);
```

* `cm-required` - この要素は必須項目となります。チェックボックスに適用されているいる場合は、チェックボックスがチェックされます。
* `cm-email` - 有効な電子メールアドレスかどうかを検証します。
* `cm-phone` - 有効な電話番号形式かどうかを検証します。
* `cm-zipcode` - 有効な郵便番号形式かどうかを検証します。郵便番号のフォーマットは`$.ceFormValidator('setZipcode', {...})).`で指定することが出来ます。（各国の郵便番号ルールはそれぞれ異なりますので、「国テーブル」でフォーマットが定義されています。）

例:

```javascript
<script type="text/javascript">
Tygh.$.ceFormValidator('setZipcode', {
    US: {
        regexp: /^(\d{5})(-\d{4})?$/,
        format: '01342 (01342-5678)'
    },
    CA: {
        regexp: /^(\w{3} ?\w{3})$/,
        format: 'K1A OB1 (K1AOB1)'
    },
    RU: {
        regexp: /^(\d{6})?$/,
        format: '123456'
    }
});
</script>
```

* `cm-value-integer` - フォーム要素（inputやtextareaなど）に適用することが出来ます。入力された値が整数であるかを検証します。
* `cm-value-decimal` - フォーム要素（inputやtextareaなど）に適用することが出来ます。入力された値が浮動小数点数であるかを検証します。
* `cm-integer` - 入力された値が数字であるかを検証します。
* `cm-password` - 必ずペアとなる要素に割り当てられ、二つの要素に記入された値が同じかどうか検証します。
* `cm-multiple` - select要素の少なくとも１つの値が選択されているかどうかを検証します。
* `cm-all` - フォームを送信する前に要素のすべてのオプションを選択します。

独自の検証パターンを定義することも出来ます。

例:

```html
<label for="a" class="cm-custom (check)"><input type="input" id="a" value="" />
<script type="text/javascript">

    function fn_check(id)
    {
        return (1 == 1) ? true : 'Not valid';
    }
</script>
```

このファンクションは対象のIDのみをパラメーターとして受け取ります。

* `cm-regexp` - 正規表現で書かれたフォーマットに正しいかを検証します。 正規表現は`$.ceFormValidator('setRegexp', {})`で定義されます。

例:

```html
<label for="a" class="cm-custom (check)"><input type="input" id="a" value="" />
<script type="text/javascript">

    function fn_check(id)
    {
        return (1 == 1) ? true : 'Not valid';
    }
</script>
```

* `cm-numeric` - checks whether the value is a number with the help of autoNumeriс plugin.\
  jQueryの「autoNumeriс」プラグインを使って、値が数字かどうかを検証します。

[autoNumeriсプラグインのドキュメント](http://www.decorplanit.com/plugin/)

例:

```html
<label for="a" class="cm-regexp"><input type="input" id="a" value="" />
<script type="text/javascript">
    regexp['a'] = {regexp: "^[A-Za-z]+$", message: "Please, use only alphabetical signs"};
</script>
```

***

## フォーム処理

```html
<form class="cm-ajax">
    ...
</form>
```

マイクロフォーマットはformタグのCSSクラスとして使用されます。\
クラスは組み合わせることができます。

* `cm-ajax` - このフォームはAJAXを使って送信されます。 このフォームには`result_ids`というhidden要素が自動で追加されます。

```html
<input type="hidden" name="result_ids" value="id1, id2" />
```

* `cm-ajax-full-render` - `cm-ajax`と一緒でのみ使用できます。ページ全体を強制的にリロードします。
* `cm-no-ajax` - フォームの送信ボタンにこのクラスが割り当てられている場合、ボタンをクリックした際には、`cm-ajax`クラスがフォームにある場合でも、フォームは通常の方法（GET/POST）で送信されます。このイベントはAJAXでformが送信される前後で使用します。これを宣言するには以下のルールを使用してください：
* `Pre-call`: `'ce.formpre_[FORM_NAME]',` \[form, clicked\_elm]\`.
* `Callback`: `'ce.formpost_[FORM_NAME]',` \[form, clicked\_elm]\`.
* `form` - 送信されるフォームオブジェクト
* `clicked_elm` - 強制的に送信されるフォームオブジェクト
* `[FORM_NAME]` - 送信されるフォーム名

例:

```javascript
$.ceEvent('on', 'ce.formpre_add_to_cart_form', function(form, elm) {
  // Some code here
});
```

* `cm-check-changes` - フォームを送信する前に保存されていない変更があるかチェックします。 もし保存されていない変更箇所があれば、警告が表示されます。これは、管理パネル内の全てのPOSTメソッドのフォームにデフォルトで割り当てられています。

```javascript
if (_.area == 'A') {
   frms.filter('[method=post]').addClass('cm-check-changes');
```

* `cm-disable-empty` - フォームに割り当てられます。空のフィールドは送信されません。例えば、商品検索フォームにおいて未指定のパラメーターの送信をキャンセルします。
* `cm-disable-empty-files` - フォームに割り当てられます。空のファイル送信フィールドを送信しません。
* `cm-failed-field` - フォームが送信され後、間違って入力されたエラーのあるデータを強調するために自動的にフォームフィールドに割り当てられます。
* `cm-no-hide-input` - フォームに\_cm-disable-empty\_クラスが指定されていても、空の値を送信することができます。
* `cm-trim` - label要素に割り当てられます。バリデーションチェックする際に、入力の値から前後のスペースを削除します。

***

## フォームのリセット

* `cm-reset-link` - このクラスで要素をクリックする際に、フォームの値がデフォルト値に設定されます。 商品検索フォームで使用されます。

***

## フォームフィールドの選択

* `cm-select-tex`t - クリックされた際にこのクラスをもつ要素は、ハイライト表示されます。 (textarea, input要素) バッファリング用のコピーを簡単にします。

***

## フォームのリロード

* `cm-reload-form` – form要素に割り当てられます。このフォームが変更された際、GETリクエストを使用してリロードされます。

***

## フォーム内のピッカー

* `cm-ajax-content-input` - 例えば、ピッカー内で検索リクエストを行う際に自動補完を可能にします。自動補完用のAJAXリクエストは入力が停止した後、500ミリ秒遅れて送信されます。受信したコンテンツ（AJAX）は`REV`属性に書かれているコンテナにロードされます。自動補完のパターンは`value`パラメータから取得されます。　

例: マーケットプレイス版でピッカーを使って出品者を選択する

　　　　　

* `cm-ajax-content-more` - 非表示となっている要素が見えるようになる時に（例えば多数のオプションを持つドロップダウンリスト）、追加のコンテンツがロードされます。

　　　　　

例: マーケットプレイス版で多数の出品者をピッカー内で選択する場合

* `cm-cancel` - このマイクロフォーマットを持つボタンがクリックされた時、全てのピッカーフィールドはデフォルト値が設定されます。
* `cm-clone` - ピッカーを閉じることなくピッカー内にアイテムを追加する際に使用します。（AJAXリクエストなしで）

例: 「キャンペーン」を管理するページでピッカー内に複数の商品やカテゴリー追加する際に空の要素に複製されたアイテムを追加します。

* `cm-dialog-opener` - このマイクロフォーマットは、ダイアログを開かせたい要素（「カテゴリーを開く」等）に割り当てられます。ダイアログの中身は`REV`パラメータで定義します。
* `cm-dialog-closer`- すでに開いているダイアログを閉じさせる要素に割り当てます。\
  例: ピッカー内の「キャンセル」ボタン
* `cm-dialog-keep-in-place` - ※まだ実装されていません。
* `cm-dialog-auto-open` - ページを訪れた際にこのクラスが適用されたダイアログボックスは自動でオープンします。\
  例：管理パネルに初回にアクセスした場合に自動で開かれる設定ウィザードなどで使用されています。
* `cm-dialog-auto-size` - このマイクロフォーマットは、`cm-dialog-opener`クラスと共に使用します。このクラスが使用されている場合、ダイアログボックスの幅と高さはコンテンツに自動調整されます。
* `cm-dialog-auto-width` - `cm-dialog-opener`と共に使用します。ダイアログボックスの幅はコンテンツに自動調整されます。
* \

* `cm-dialog-switch-avail` - ダイアログボックス内で選択した全てのチェックボックスを選択解除します。
* `cm-js-item` - このクラスは、ピッカーからフォームに要素を追加する際（例：ギフト券への追加）、追加された要素が属するコンテナに割り当てられます。
* `cm-picker-options` - 商品オプションがピッカーからの商品に適用された際に適用されます。

\[block:html] { "html": "\<tbody id="{$data\_id}" class="{if !$item\_ids}hidden{/if} cm-picker-options">" } \[/block]

***

## input\[type=submit]要素以外をクリックしてフォームを送信する方法

使用法:

```html
<input type="radio" name="a" value="b" class="cm-submit" />
```

マイクロフォーマットは要素のCSSクラスとして定義されます。クラスは組み合わせることができます。

* `cm-submi`t - この要素が割り当てられたform要素は、その要素がクリックされた後に送信されます。

例：ボタン以外の要素のクリックでフォームを送信するためには：

1. `cm-sumbit`を要素に割り当てる
2. `data-ca-dispatch`属性でフォームが送信されるデdispatch（\[controller.mode）を指定します。

※送信する要素がフォームの外にある場合のみ、`data-ca-target-form`属性で名前やIDを返します。

***

## 新しいウィンドウや親ウィンドウでフォームを送信する

例:

```html
<input type="submit" name="a" value="b" class="cm-new-window" />
<input type="submit" name="a" value="b" class="cm-parent-window" />
```

* `cm-new-window` - クリックされた際に新規のウィンドウが開きフォーム内容がそこに送信されます。
* `cm-parent-window` - クリックされた際にフォームの内容は親ウィンドウに送られます。

***

## フォームの送信を禁止する

使用方法:

```html
<input type="submit" name="a" value="b" class="cm-no-submit" />
```

* `cm-no-submit` - この要素が割り当てられているフォームは、submit要素がクリックされても送信されません。

***

## フォームバリデーションをスキップする

使用方法:

```html
<input type="submit" name="a" value="b" class="cm-skip-validation" />
```

* `cm-skip-validation` - submit要素をクリックすると、この要素の適用されたフォームはバリデーションを行うことなく送信されます。

## フォームを隠す

* `cm-hide-inputs` - このクラスが適用されたinput要素の値はplain textで表示されます。（編集はできません）　　　　　　　　\
  例: マーケットプレイス版CS-Cartで出品者に表示は行うが編集できない項目など
* `cm-hide-save-button` - このクラスが割り当てられているボタンは非表示になります。（ボタンの保存は必須ではありません）

## フォーム内のチェックボックス

利用方法:

```html
<input type="checkbox" name="check_all" value="Y" class="cm-check-items" />
...
<input type="checkbox" name="product_ids[]l" value="1" class="cm-item" />
<input type="checkbox" name="product_ids[]l" value="2" class="cm-item" />

<a href="#" name="check_all" class="cm-check-items on">Check all</a>/<a href="#" name="check_all" class="cm-check-items off">Uncheck all</a>
```

上記の例では二種類の異なるチェックボックスをコントロールする要素があります。

* 通常のチェックボックス\
  \-リンクのチェックボックス

コントロール要素には`check_all`とクラスと`check-items`クラスがなければならりません。

コントロール要素がリンクの場合、クラスの\_on\_と\_off\_も決められています。（全てのチェックボックスをコントロールする）

チェックボックスを管理できる要素には、\_class\_を持っていなければなりません。\
\_process-items\_クラスは、フォームを送信するボタンに割り当てられます。

ボタンがクリックされた際にチェックボックスが対応するグループは、チェックボックスが\_on\_か\_off\_を調べます。

いずれのチェックボックスもonでない場合は、メッセージが表示されます。

> 📘 ヒント
>
> フォームに複数のチェックボックスがあり、それらを別々にコントロールする必要がある場合、`cm-check-items`クラス、`cm-item`クラス、`cm-process-items`クラスにユニークなサフィックスが追加されます。

```html
<input type="checkbox" name="check_all" value="Y" class="cm-check-items-group" />
...
<input type="checkbox" name="product_ids[]l" value="1" class="cm-item-group" />
```

## その他の要素

## リンク

リンクをクリックした際に、AJAXリクエストを実行できるマイクロフォーマットがあります。

使用方法：

```html
<a class="cm-ajax" href="http://cs-cart.com" data-ca-target-id="pagination_contents">Ajax link</a>
```

* `cm-ajax` - AJAXリクエストはリンクをクリックした際に実行されます。
* `cm-comet` - フォームはCometモデル（[wikipedia](http://ja.wikipedia.org/wiki/Comet) ）を利用してリフレッシュされます。
* `cm-delete-row`- このクラスを持つ要素がクリックされた場合、最も近い親要素「tr」が削除されます。 テーブルで行を削除する時に使用されます。
* `cm-row-item` - このクラスはテーブルの行に割り当てられます。`cs-delete-row`と共にコンテナの識別に使用します。
* `cm-ajax-cache` - AJAXリクエストをキャッシュする事ができ、`cm-ajax`と共に使用されます。
* `cm-ajax-force` - 二度目のajaxレスポンスからjavascirptコードを実行できます。`cm-ajax`と共に使用されます。

***

既知のIDの要素をクリックする必要がある場合、`cm-external-click`クラスのリンクを使用できます。クリックされるべき要素のIDはリンクの\_data\_ca\_scroll\_パラメータに指定されています。

```html
<a class="cm-external-click" data_ca_scroll="external_elm">Push me</a>
```

* `cm-external-focus` - このクラスを持っている要素がクリックされた際、外部の要素にフォーカスが移動します。移動する外部の要素のIDはREVパラメータで定義されています。
* `cm-smart-position` - コンテナを配置するために使用します。（例えば、管理パネルの通貨リスト）

## 通知

* `cm-auto-hide` - このクラスによって通知は自動的に消滅します。 タイムアウトまでの時間は\_基本設定\_ -> \_表示設定\_で設定することができます。
* `cm-notification-close` - このクラスは、通知を「閉じる」ボタンに割り当てられています。通知自体をクリックすると、フォームから削除されるか通知を削除するAJAXリクエストが送信されます。　
* `cm-notification-container` - このクラスは、通知コンテナに割り当てられます。

## その他のエンティティ

* `cm-confirm` - このクラスを持つリンクがクリックされた際にアクションの確認が表示されます。
* `cm-noscript` - このクラスを持つ要素はJavascriptサポートがブラウザで有効になってない場合のみ表示されます。
* `cm-focus` - ページがロードされた際、このクラスを持つ要素にフォーカスが移ります。\
  例：ログイン入力フォームがロードされた際、フォーカスはログインフィールドにあります。
* `cm-opacity` - テーブルから削除された行に割り当てられ、その行は半透明になります。\
  例: 商品画像を複製し、その“行” を削除します。ページがリロードされ、削除が実行されるまでその“行”が半透明のままです。
* `cm-uploaded-image` - アップロードされた画像のdiv要素に割り当てられています。アップロードされた画像のカウントに使用します。
* `cm-wysiwyg` - textarea要素に割り当てられています。その要素にwysiwygエディタを提供します。
* `cm-row-status-[current status code]` - テーブルの行に割り当てられます（要素）\
  \[current status code]は、アイテムの現在のステータスコードを示します。(e. 「a」は有効、「d」は無効 ...)
* `cm-autocomplete-off` - フィールドから自動入力の機能を削除します。パスワード入力フィールドで使用されています。

***

## ポップアップ

マイクロフォーマットの`ポップアップ・ボックス`が使用出来ます。エリア外をクリックするとそのポップアップを閉じることができます。

使用方法:

```html
<div class="cm-popup-box">
...
</div>
```

ポップアップしたエリア内の要素をクリックで隠すためには、それらの要素に\_cm-popup-switch\_クラスを定義する必要があります。

フォーマット:

```html
<div class="cm-popup-box">
<strong class="hand cm-popup-switch">Close</strong>
...
</div>
```

* `cm-select-option` - 管理パネル内のbootstrapのポップアップに使用されます。
* `cm-popover` - bootstrapのポップオーバーの初期化を行います。 [http://getbootstrap.com/2.3.2/javascript.html#popovers](http://getbootstrap.com/2.3.2/javascript.html#popovers)

## 要素の組み合わせ

**cm-combination** は表示/非表示させたいコンテナに使用し、その状態を示しています。

これは例えば、管理パネル内の商品やカテゴリーに対する「高度な検索」に適用されます。\
‘state’によって、コンテナのモード（表示/非表示の状態）に応じて違う画像の表示を意味します。

```html
<img src="" id="on_cat" class="cm-combination" />
<img src="" id="off_cat" class="cm-combination" />
<a href="#" id="sw_cat" class="cm-combination">
...
<div id="cat">
</div>
```

追加される要素は、異なったプレフィックスを持ったIDを使用します。プレフィックスには3タイプあります。

* `on_` - クリックされた際に、コンテナを開く要素に適用(_通常はプラス・’アイコン_).
* `off_` - クリックされた際に、コンテナーを閉じる要素に適用(_通常はマイナス・アイコン_).
* `sw_`- クリックでコンテナの内容を切り替える要素に適用 (通常はリンク・アイコン)

***

* `cm-uncheck` - \_cm-combination\_と共に使用し、\_cm-combination\_の適用されたidで定義されたチェックボックスの状態を切り替えます。
* `cm-switch-availability` - IDがsw\_elem\*のcm-switch-availabilityと関連付けられた入力要素（checkboxやradio、text）の状態を切り替えます。

有効になっているときに、クリックされた要素(\_cm-switch-availability\_の適用されいる) を作成するには\_cm-switch-inverse\_を使用してアイテムを切り替えます。

checkboxやradioブロックを表示/非表示にするには、\_cm-switch-visibility\_を使用します。checkboxまたはradio以外のブロックに使用される場合、\_cm-switched\_は条件を定義します。

フォーマット例:

```html
<input type="checkbox" id="sw_company_redirect" checked="checked" class="cm-switch-availability cm-switch-inverse cm-switch-visibility" />
```

* `cm-select-with-input-key` - テキストエリアでセレクタを接続します。セレクタの値が変更された際、それをテキストエリアに移動させ、そのエリアを無効にします。

ロケーションで使用する場合、\_dispatch\_を選択します。

## 要素の組み合わせ

要素の組み合わせ（例：ツリー表示の表示/非表示）にこのマイクロフォーマット\_cm-combinations\_が使用されます。

```html
<img src="" id="on_cat" class="cm-combinations" />
<img src="" id="off_cat" class="cm-combinations hidden" />
```

ここで使用されるIDはこれらの２つの要素に対してのみです。

また、組み合せをグループ化することが出来ます。（同じページに複数のツリーがある場合など）その際はsuffixを追加することが必要です。

```html
<img src="" id="on_abc" class="cm-combinations-a" />
<img src="" id="off_abc" class="cm-combinations-a hidden" />
...
<img src="" id="on_cat" class="cm-combination-a" />
<img src="" id="off_cat" class="cm-combination-a" />
<a href="#" id="sw_cat" class="cm-combination-a">
...
<div id="cat">
</div>
```

この例の場合、上のイメージをクリックした際、グループAの組み合せのみが表示/非表示になります。

## タブ

* `cm-js` - このクラスを持つdiv要素は内部にタブのリストを持つSmartyが生成されます：`cm-js`クラスを持つ li要素と特定のID（例：description）

タブがクリックされた際、_content_ + タブのID\_というIDを持つdiv要素が検索されて表示されます。コンテナ内の他のdiv要素は非表示になります。

* `cm-j-tabs` - `cm-js`タブのコンテナに使用されます。タブコンテナの検索や初期化に使用されます。
* `cm-tabs-content` - 非表示にできる保存ボタンのあるタブに割り当てられます。(`cm-hide-save-button`)
* `cm-toggle-button` - このクラスは、div要素に割り当てられます。`cm-hide-save-button`クラスを持つdivのタブが選択された際、`div`に配置されたボタンは非表示になります。

## 保存の状態

* `cm-save-state` - コンテナの保存状態を有効にするには、このマイクロフォーマットがコンテナをオープン/クローズする全ての要素に割り当てられる必要があります。 このとき、状態の変化に、この要素のIDと接続されるクッキーが作成されます。 デフォルト状態はコンテナは _非表示_ です。デフォルトの状態がコンテナを【_表示_】するべき場合は、マクロフォーマットは\_a\_ です。 クッキーの確認と要素を隠す処理はテンプレートに書かれている必要があります。
* `cm-save-fields` - このクラスを持つ表示されている入力コンテナは配列にシリアライズされAJAXリクエストに渡されます．

## 追加のマイクロフォーマット

* `cm-skip-avail-switch` - このマイクロフォーマットは、`switchAvailability`に使用します。(特定の全ての要素のオン/オフを切り替えます)。要素がこのクラスを持っている場合、後ろには切り替わりません。
* `cm-skip-check-items` - assigned to a form to allow state change check skipping for its children elements. 子要素のチェックをスキップする場合に、このマイクロフォーマットをフォームに割り当てます。
* `cm-track` - タブコンテナを割り当てます。最後に有効になったタブが送信された後に開かれます。
* `cm-save-and-close` - `return_to_list`を持つ非表示のフィールドを追加します。「保存して閉じる」ボタンに使用します。
* `cm-promo-popup` - フリーモードのCS-Cartでフルバージョンの機能を利用しようとした際に警告ポップアップを開きます。
* `cm-update-for-all-icon` - ストアフロントの共有を可能にします。フィールドは編集できるように作られています。
* `cm-sticky-scroll` - 使用されるブロックを固定します。ページの上端からこのクラスが付いたブロックまでスクロールされた場合、ブロックの位置が固定されます。 `data-ce-padding`は、固定されたフィールドが画面の上端で特定されるまでの距離となります。

例:

```html
<div class="subnav cm-sticky-scroll" data-ce-top="100" data-ce-padding="20" >
```

ブラウザのウィンドウが100pxスクロールされた際、パネルは上端から20pxの距離で固定されます。

* `cm-range-slider` - 範囲選択スライダーの初期化を行います。 (jQuery UI Slider)
* `m-colorpicker` - カラーピッカー ([http://bgrins.github.io/spectrum/)の初期化を行います。](http://bgrins.github.io/spectrum/\)%E3%81%AE%E5%88%9D%E6%9C%9F%E5%8C%96%E3%82%92%E8%A1%8C%E3%81%84%E3%81%BE%E3%81%99%E3%80%82)

## ロケーション

* `cm-location-billing`
* `cm-location-search`
* `cm-location-shipping`

`cm-location-`を持つ全てのクラスの配列は都道府県・州（state/province）を定義するために使用されます。

`cm-location-`の後の値は対象を区別するために使用されます。

都道府県・州（state/province）のリストを含む選択ボックスは`for`属性に定義されます。

* `cm-country` - 国名を定義するために `cm-location-\*` と共に使用します。その国の都道府県・州（state/province）はこのマクロフォーマットにしたがって更新されます。
* `cm-state` - ラベルが割り当てられます。\
  `for`属性で指定された要素は都道府��・州（state/province）のリストが利用可能な場合、または入力フィールド以外の場合にリストとしてどちらも表示されます。

## ヒントやツールチップ

* `cm-hint` - ヒントを表示したい入力フィールドやテキストエリアはこのクラスに割り当てます。表示したいヒントのテキストはタイトル属性に定義して下さい。

もしタイトル属性が無い場合、ヒントのテキストは「value」属性から取得されます。\
フィールドにフォーカスが移ると、ヒントは非表示になります。\
フィールドが空の場合。フォーカスが外れると、ヒントは再び表示されます。

ヒントがフィールド内に表示された場合、フィールド名のプレフィックスとして`hint_`がつけます。このプレフィックスはテキストが入力された場合に取り除かれます。

例:

```html
<input type="text" name="field" id="a" size="20" title="Please, input your name here" value="" class="input-text cm-hint" />
```

* `cm-hint-focused` - 入力フィールドにフォーカスが当たるとヒントが隠れていることを示しています。`cm-hint`と使用する際、内部ヒントが表示されなくなります。
* 例）\
  管理パネルの\_注文の追跡\_フィールド
* `cm-tooltip` ツールチップを持つ要素に割り当てられます。ツールチップテキストはタイトル属性に定義します。

## 管理パネル

## 整形リスト

Such list can be seen, for example, on the currency edit page: when a row is dragged, its position is changed.\
例えば、次ようなリストがあります。\
通貨編集ページ：行がドラッグされると、ポジションが変更されます。

* `cm-sortable`はソート可能なコンテナに割り当てられます。(cm-sortable-row)\
  例：管理パネルの通貨リスト
* `cm-sortable-id-*/` - `cm-sortable`コンテナの特定の行のIDを指定します。`cm-sortable-id-`の後の値はリクエストに引き渡され、リストの変更に使用されます。
* `cm-sortable-row` - ドラッグ可能なテーブルの行に割り当てられます。その行は`cm-sortable`コンテナ内にある必要があります。

## ファイル・アップローダー

* `cm-fu-file` - ファイルアップローダーから更新されたファイルブロックに割り当てられます。ファイルがない場合、ブロックは表示されません。ファイルがある場合は、表示されます。
* `cm-fu-no-file` - ファイルのアップロードが可能な要素に割り当てられます。任意のファイルアップローダーを例として下さい。

## クイック・メニュー

* `cm-add-link` - クイックボックスを使用してクイックメニューセクションにに新しいリンクを追加します。
* `cm-add-section` - クイックボックスを使用してクイックメニューに新しいセクションを追加します。
* `cm-delete-section` - クイックメニューの「選択」または「リンク」の削除ボタンに割り当てられます。
* `cm-qm-name` - 編集モードのクイックメニューアイテムに割り当てられます。クイックボックスにセクションデータを渡すために使用されます(リンク検索はこのクラスを使用して実行されます)。
* `cm-update-item` - \_クイックメニュー\_アイテム編集リンクに割り当てられます。アイテムのパラメータダイアログはクリックで展開されます。

## イメージ・ギャラリー

* `cm-image-gallery` - 画像ギャラリーの初期化
* `cm-previewer` - 画像をクリックした際に、より大きな画像が開くリンクに割り当てられます。ページは遷移しません。大きな画像は、hrefパラメーターで定義されてます。

```html
<a id="det_img_link_1553_140" rel="preview[product_images]" rev="preview[product_images]" class="cm-previewer" href="/professional/images/detailed/0/detailed_image_1386.jpg" title="img.jpg">
  <img class=" "  id="det_img_1553_140" src="/professional/images/thumbnails/0/120/img.jpg" width="120"  alt="img"  border="0" />
</a>
```

* `cm-thumbnails-mini` -商品詳細ページのサムネイル・ギャラリーの画像に割り当てられます。 画像がクリックされた際、サムネイルの指定と`cm-cur-item`クラスを割り当てるために使用します(`cm-cur-item`は`cm-thumbnails-mini`を持つすべてのアイテムから取り除かれます)。

## コンテンツのリアルタイム編集モード

* `cm-cur-template` - デザインモードで編集中の現在のテンプレートに割り当てられます。\
  また、編集中のテンプレートの変更を識別するために使用されます。デザインモードでのみ使用されます。
* `cm-item-modified` - デザインモードのテンプレートの編集に割り当てられます。このクラスが割り当てられたテンプレートの変更があった場合(エディタの左側にあるテンプレート・ツリーを使用)、既存の変更を知らせるメッセージが表示されます。
* `cm-lang-link` - 言語の変更のリンクに割り当てられます。言語は`name`属性で定義されます。
* `cm-select-list` - 言語選択のドロップダウンリストに割り当てられます。`cm-lang-link`要素のコンテナとして使用されます。 \
  例：翻訳モードで翻訳ダイアログが開かれている言語リスト

## 数量入力

* `cm-decrease` - `cm-value-change`で使用されます。入力値を減らすリンクに割り当てられます。入力値を１つずつ減らします。整数でない値は0に置き換えられます。

例：ショップフロントの数量フィールド付近の上/下の矢印

* `cm-increase` - `cm-value-changer`コンテナで使用されるます。入力値を増すリンクに割り当てられます。入力値を1つずつ増やします。整数でない数字は0に置き換えられます。
* `cm-value-changer` - 入力と`cm-increase/cm-decrease`ボタンの親コンテナに割り当てられます。

例：ショップフロントの商品数量フィールド

## ノードの複製

* `cm-first-sibling` - このクラスを持つ行は削除できませんので、削除アイコンは無効になっています。（複製元）
* `cm-image-field` - 要素の複製で画像数を正確に複製するための正規表現を定義します。

例：商品オプションを複製。ぞれぞれのオプションに指定された画像に対して、このクラスは画像に関連するフィールドに割り当てられます。

## ラッパー

* `cm-hidden-wrapper` - コンテンツがない場合に非表示になるラッパーに割り当てられます。すなわちブロックコンテンツの中身が空の場合、ラッパーは示されません。

## ページネーション

* `cm-history` - リンクに割り当てられます。このマイクロフォーマットを持つリンクがクリックされた際、現在のページがブラウザの閲覧履歴に保存されます。 jQueryの「history」プラグインが使用されています。
* `cm-back-link` - ブラウザの履歴を利用して以前のページに戻るページナビゲーションの描画結果のコンテナ。AJAXリクエストの後にスクロールするために使用されます。
* `cm-pagination-container` - ページナビゲーションの描画された結果のコンテナ。AJAXリクエストの後にスクロールするために使用されます。

## AJAX

* `cm-ajax` - このマイクロフォーマットが設定されたリンクがクリックされた際に、AJAXリクエストが実行されます。
* `cm-comet` - コメットモデルを使用してリフレッシュされたフォームに割り当てられます。
* 例：データベースのバックアップフォーム
* `cm-ajax-cache` - AJAXリクエストをキャッシュでき、`cm-ajax`と共に使用されます。
* `cm-ajax-force` - 2度目のAJAXレスポンスからJavascriptのコードを実行出来ます。`cm-ajax`と共に使用されます。
* `cm-ajax-close-notification` - used for the notifications that do not disappear on page change or on timer. When the close button is clicked, a close notification AJAX-request is sent.

通知が行われ、それが閉じられるタイマーまたはボタンに適用されます。閉じるボタンがクリックされた際、通知を閉じるためのAJAXリクエストを送信します。

* `cm-ajax-full-render` - `cm-ajax`と一緒にのみ使用出来ます。全体ページをリロードさせます。
* `cm-no-ajax` - このクラスがフォームの送信ボタンに割り当てられている場合にボタンがクリックされると、`cm-ajax`がフォームに割り当てられていた場合でも、通常の方法でフォームが送信されます。（AJAXを強制的に利用しない）
* `cm-ajax-content-input` - 例えば、検索リクエストに自動補完を可能にするためのピッカーで使用されま

自動補完の為のAJAXリクエストは入力が止まった後、500ミリ秒遅れで送信します。 受けとった検索候補は`rev`属性に記載されたコンテナにロードされます。自動補完のパターンは値のパラメータから取得されます。

例：出品者を選択する際に、マーケットプレイスのピッカーを表示する。

* `cm-ajax-content-more` - （大きなセレクトボックス）要素が表示されるようになった時、追加のコンテンツがロードされます。

例：選択のオプションの数が多い場合、出品者の選択はマーケットプレイスのピッカーを介します。

## AJAXでアップロードされた商品コンテンツ

* `cm-reload` - オプション変更で変更されたブロックに割り当てられます。\
  その後、このクラスをもつ全ての要素が更新されます。例は、

```php
{* Price *}
{capture name="price_`$obj_id`"}
<span class="cm-reload-{$obj_prefix}{$obj_id} price-update"
  id="price_update_{$obj_prefix}{$obj_id}">
...
```

オプションが変更され更新されると、価格は随時更新されます。

## カスタマイズ・モード

* `cm-template-box` - カスタマイズモードでのレンプレートとして存在するコンテナに割り当てられます。テンプレートを管理し、テンプレートの入れ子構造のレベルを決めるために使用されます。
* `cm-template-icon` - カスタマイズモードがオンになっている時に、テンプレートの編集アイコンに割り当てられます。\
  マウスホバーでテンプレートの適応範囲を強調します。 (`cm-template-over`を使用)。 マウスのホバーが外れると、ハイライトが無効になります。
* `cm-template-over` - 選択されたテンプレートの使用により表示されるコンテナに割り当てられます(カーソルがそれぞれ`cm-template-icon`アイコンの上にある)。 CS-Cartがカスタマイズモードの時、選択されたテンプレートの適応範囲にハイライトが使用されます。
