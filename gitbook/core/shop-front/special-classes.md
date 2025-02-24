---
icon: file-code
---

# 特別なCSSセレクター

special classes（特別なCSSセレクタ）はCSSのコーディング量とファルのサイズを最小にするために提供されます。これらのCSSセレクタは管理パネルのカスタマイズを行う際に利用されています。

```html
<!-- フロートしたエレメントを生成します -->
<div class="float-left">これはフロートボックスです</div>
<div>これは通常ボックスです</div>
```

標準的なクラスと一緒に使用する場合:

```html
<!-- 通知BOXにフロートしたエレメントを生成します -->
<div class="notice-box float-left">これはフロートボックスです</div>
<div>これは通常ボックスです</div>
```

以下はスペシャルクラスの全リストです：

* `input-text` – テキストを入力する際に使用します。要素の幅はパラメーターのサイズと同じになります。
* `input-textarea` – テキストエリアに使用します。要素の幅はパラメーター`cols`と同じになります。
* `input-textarea-long` – フォーム内のすべての通常のテキストエリアに使用されています。幅はCSSで定義されています。
* `input-text-short` – ポジションや数量などの入力に使用します。 フォームやテーブル内では幅はCSSに(デフォルト：30px)定義されます。
* `input-text-medium` – 商品価格や重さ、商品コードなどの入力で使用します。
* `input-text-large` – タイトルや商品名などの入力で使用します。
* `hand` – マウスが要素の上にある時にマウスカーソルを手の形に変える時に使います。
* `nowrap` – 文字が改行されないようにテーブル内のnowrapパラメーターのかわりに使用します。
* `uppercase` – 文字が大文字になります。
* `lowercase` – 文字が小文字になります。
* `strong` – 文字が太字になります。
* `error-text` – フォント色が赤になります。
* `solid-border` – 要素の回りを1pxのボーダーで囲みます。
* `float-left` – フロート要素が作成され、左側に位置します。他の要素はこの要素の左側に配置されます。
* `float-right` – フロート要素が作成され、右側に位置します。他の要素は右側に配置されます。
* `clear` – フローティングされたブロックの親要素に定義します。これによって次の要素のフロートを解除できます。
* `no-padding` – CSSで指定または継承されたパディング属性をゼロにします。
* `no-margin` – CSSで指定または継承されたマージン属性をゼロにします。
* `valign` – 指定された要素をベースラインの中間に配置します。
* `checkbox` – テーブルやフォーム内のチェックボックスの位置を揃えます。テーブルやフォームのボックスを揃えるために使用します。
* `radio`– テーブルやフォーム内のラジオボタンの位置を揃えます。テーブルやフォームのラジオボタンを揃えるために使用します。
* `no-items` – 「表示するアイテムが見つかりませんでした」という通知のブロックに使用します。「表品が見つかりませんでした」という通知が表示されるブロックをデザインするために使用します。
* `center` – ブロック内のテキストやインライン要素を中央揃えするために使用します。
* `right` – ブロック内のテキストやインライン要素を右揃えするために使用します。ブロック内の右側のテキストやインライン要素を揃えるために使用します。
* `scroll-x` – 水平スクロールを作成します。
* `hidden` – 要素を隠します。`style="display: none;"`の代わりに使用できます。
