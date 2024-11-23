---
icon: solar-panel
---

# トップパネルとヘッダー

次のステップは、`トップパネル`と`ヘッダー`コンテナ内のブロックを作成します。

これらの変更は`デフォルト`のロケーションで設定します。

編集するには、`デザイン > レイアウト`セクションに移動します。\
`デフォルト`は最初のロケーション・タブです。

## ブロックの編集

`通貨`や`言語`のブロックを置き換えることができます。\
それを行うには、`言語`ブロックを`通貨`ブロックの後へドラッグアンドドロップします。

次に`24時間配送`ブロックを追加します。\
ブロックに新しいグリッドを追加します。特定の場所に新しいグリッドを追加するには、まず`ヘッダー`コンテナにグリッドを追加する必要があります。

`トップメニュー`ブロックとグリッドを移動します：

* `ヘッダー`コンテナにマウスポインターをおき、＋ボタンをクリックし、`グリッドの追加`をクリックします。
* 開いたウィンドウで`横幅`16を選択し、`ユーザ定義のCSSクラス`のフィールドで、_top-menu-grid_ を入力し、`[作成]`をクリックします。
* 新しく作成されたグリッドを `トップメニュー`ブロックにドラッグアンドドロップします。すると、`トップメニュー` ブロックを移動した場所に空の16列のグリッドができます。
* 16列のグリッドのギアアイコンをクリックします。
* 開いたウィンドウで`横幅`選択ボックスで「3」を選択し、`ユーザー定義のCSSクラス`フィールドで\_cart-content-grid\_を入力し、`保存`をクリックします。

\- `カートの内容` ブロックをこのグリッドにドラッグアンドドロップします。

* 空のグリッドのギアアイコンをクリックします。
* 開いたウィンドウの`横幅`選択ボックスで5を選択し、`ユーザー定義のCSSクラス`フィールドで\_search-block-grid\_ と\_cart-content-grid\_ を置き換えて`保存`をクリックします。
* このグリッドに`検索`ブロックをドラッグアンドドロップします。

`ヘッダー` コンテナに空の3列のグリッドができます。これを`毎日配送`ブロックを作成するために使用します。

* 空の3列のグリッドのギアアイコンをクリックします。`ユーザー定義のCSSクラス`フィールドを消して`保存`します．
* ＋アイコンをクリックし、`ブロックの追加` をクリックします。
* 開いたウィンドウで `ブロックの追加`タブに切り替え、`HTMLブロック`ボタンをクリックします。
* `名前`フィールドに「毎日配送」と入力し、`コンテンツ`タブに切り替えます。
* `コンテンツ`フィールドの設定で、`\</>`（ソースコード表示）ボタンをクリックして、フィールドに次のコードをコピーします：

```html
<div class="ty-delivery">
  <i class="ty-delivery__icon"></i> Delivery 24/7
</div>
```

* `登録`ボタンをクリックします。

残りのブロックはそのままにしておきます。

例の中には`マイカウント`はありませんが、それは重要な機能なのでそのままにします。

結果、以下のようになります：

＜画像＞

## トップパネルとヘッダー用のスタイルを作成する

CS-Cartではビジュアルエディタで背景やリンクの色などを変えることが可能です。

管理パネルのビジュアルエディタを開き、`デザイン > テーマ`に移動し、「現在のテーマ」の`ビジュアルエディター`ボタンをクリックします。

＜画像＞

トップパネルの背景を変更するには：

ビジュアルエディタの左側のパネルで`カスタマイズ`セクションのドロップダウンメニューから「背景」を選択します。

* ビジュアルエディタの左側のパネルで`カスタマイズ`セクションのドロップダウンメニューから「背景」を選択します。
* `トップパネル`セクションを探して、`背景色`のフィールドをクリックします。\
  開いたパネルの右下隅に手動で色を定義するための入力フィールドがあります。`#573c27` と入力し色を定義します。

背景のグラデーションを定義することもできます。\
`背景色`フィールドのギアアイコンをクリックします。\
追加の設定のドロップダウンパネルが表示されます。

ここではグラデーションの2つ目の色を選ぶことができ、トップパネルを透明にするかどうかを選択します。

* 同様に、ヘッダーの背景色も定義します。例ではこの色は`#fbfbfb` です。\
  このセクションを`全幅表示`の設定をオンに切り替えます。

＜画像＞

* 全ての変更が適応されたら、`保存`ボタンをクリックします。開いたウィンドウにスタイルの名前を新たに定義し、`[OK]`をクリックします。

## 独自のCSSスタイルを追加する

CS-Cartをインストールした直下の`themes/tutorial/css/`ディレクトを開き、\
\_custom.less\_ファイルを作成します。

これは`themes/tutorial/templates/common/styles.tpl`ファイルにインクルードすることができます。

`通貨`、`言語`、`毎日配達`、`カートの内容`のカスタムスタイルなどをこのファイルに記述します。

## 通貨ブロックのカスタマイズ

デフォルトでは3つの通貨が表示されています。　画面解像度が小さい場合は、ドロップダウンリストとして表示されます。

＜画像＞

デフォルトでドロップダウンリストを表示するには：

* `デザイン > レイアウト`セクション移動し、`通貨`ブロックを探します。
* このブロックのギアアイコンをクリックして「ブロック設定」タブに切り替えます。
* アイテムをドロップダウンリストにする場合は、「ドロップダウン表示を行う最小アイテム数」に1を入力し、`保存`をクリックします。

管理パネルの一般設定 > 通貨セクションで通貨名と通貨記号を定義することができます。

例でも見たように、通貨記号が使われていないのでテンプレートから削除されます。

通貨記号が定義されているテンプレートを見つけるためには2通りの方法があります：

* _レイアウトで使用されるクラスを検索_\
  このクラスがFirefoxやChromeのFirebugやDevToolsを使って調べることができます。

{% hint style="info" %}
HINT

保存されているCSSスタイルはキャッシュされています。キャッシュを自動的にクリアするには、管理パネルの\_デザイン > テーマ\_セクションにある「キャッシュの自動リビルド」オプションを有効にします。
{% endhint %}

`templates/common/select_object.tpl`ファイルで使用されている`ty-select-block__a-item`クラスを検索します。

次のコードは、表示する通貨のアイコンを制御します：

```smarty
{if $items.$selected_id.symbol} ({$items.$selected_id.symbol nofilter}){/if}
```

この箇所をテンプレートから削除し保存します。

* 階層を含んだテンプレートを使用する\
  ブロックのテンプレートは`templates/blocks`に格納されています。`通貨`ブロックには`currencies.tpl`ファイルが使用されています。

さらに`app/schemas/block_manager/blocks.php`スキームに定義されます。

```php
<?php
'currencies' => array(
'content' => array (
        'currencies' => array (
        'type' => 'function',
        'function' => array('fn_block_manager_get_currencies'),
        ),
),
...
'templates' => 'blocks/currencies.tpl',
'wrappers' => 'blocks/wrappers',
),
```

ドロップダウンリストに通貨を表示するコード：

```html
<div class="ty-select-wrapper">{include file="common/select_object.tpl" style="graphic" suffix="currency" link_tpl=$config.current_url|fn_link_attach:"currency=" items=$currencies selected_id=$secondary_currency display_icons=false key_name=$key_name}</div>
```

このコードでは`common/select_object.tpl`ファイルが`include`されています。\
これは、通貨のアイコンが定義されているファイルです。

ここで、スタイルを追加します。

トップパネルの全リンクは白です。なので、以下のスタイルを追加してみましょう：

```css
.top-grid a {
color: #fff;
}
```

`top-grid` - グリッド設定で変更することができるグリッドのためのカスタムクラス。\
ブロックとグリッドのカスタムクラスは、名称にプレフィックス`ty-`がありません。

これは、テンプレート内のクラスとそれらを混同しないようにするのに役立ちます。

例の通貨のテキスト文字は大文字です。

`ty-select-block__a-item` に対してのみスタイルを追加することは正しくありませんし、言語の表示もまたそうです。

`ty-select-block__a-item`がセレクタに配置されたブロックにクラスを使用することをお勧めします。

```css
.top-currencies .ty-select-block__a-item {
text-transform: uppercase;
}
```

## 言語ブロックのカスタマイズ

言語名の近くにある矢印を消すには、CSSを使います：

```css
.top-languages .ty-select-block__arrow {
display: none;
}
```

`クイックリンク`のスタイルを追加します；

```css
.top-quick-links .ty-text-links__a {
font-size: 12px;
}
```

## 「毎日配送」ブロックのカスタマイズ

はじめに、このブロックにアイコンを追加します。\
[icomoon.io](https://icomoon.io/) のサービスを使用可能です。アイコンフォントの詳細をご確認下さい。

IcoMoonのアイコンのセットから時計 アイコンを選びます。

＜画像＞

Tutorial theme テーマの\_media/fonts/\_ ディレクトリにフォントを保存します。

`style.css`ファイルから`custom.less`ファイルへアイコンスタイルを移動し、フォントのパスを更新します。

```css
@font-face {
font-family: 'tutorial';
src:url('../media/fonts/tutorial.eot?-vagbvx');
src:url('../media/fonts/tutorial.eot?#iefix-vagbvx') format('embedded-opentype'),
        url('../media/fonts/tutorial.woff?-vagbvx') format('woff'),
        url('../media/fonts/tutorial.ttf?-vagbvx') format('truetype'),
        url('../media/fonts/tutorial.svg?-vagbvx#tutorial') format('svg');
font-weight: normal;
font-style: normal;
}

[class^="tt-icon-"], [class*=" tt-icon-"] {
font-family: 'tutorial';
speak: none;
font-style: normal;
font-weight: normal;
font-variant: normal;
text-transform: none;
line-height: 1;

/* Better Font Rendering =========== */
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
}
.tt-icon-clock:before {
content: "\e601";
}
```

{% hint style="warning" %}
注意

新しいアイコンフォントを作成する際、アイコンクラス名がテンプレートのアイコンクラス名と一致しないように注意してください。プレフィックス`ty-icon-`を使用しないでください。
{% endhint %}

> 🚧 注意
>
> 新しいアイコンフォントを作成する際､アイコンクラス名がテンプレートのアイコンクラス名と一致しないように注意してください。\
> プレフィックス`ty-icon-`を使用しないでください。

`derivery`ブロックの`ty-delivery__icon` に`tt-icon-clock` クラスを追加してください。

```css
/* Delivery
========================================================================== */
.ty-delivery {
color: #573c27;
font-size: 12px;
}
.ty-delivery__icon {
color: #8f979a;
font-size: 21px;
vertical-align: middle;
margin-right: 8px;
}
```

検索フィールドのスタイルを追加します。スタイルのみで、レイアウトは変更しません：

```css
/* Search form
========================================================================== */
input.ty-search-block__input {
border-color: #fad8d3;
color: #979595;
font-size: 12px;
padding-right: 80px;
}
.ty-search-magnifier {
height: auto;
background: #e05b5b;
color: #fff;
top: 1px;
right: -1px;
width: 70px;
bottom: 1px;
font-size: 22px;
}
```

メインメニューのスタイルを追加します：

```css
/* Main menu
========================================================================== */
.ty-menu__items {
    background: none;
}
.ty-menu__item .ty-menu__item-link {
    padding: 8px 8px;
    color: #573c27;
    text-transform: uppercase;
    font-size: 13px;
    min-height: 0;
    margin-right: 10px;
}
.no-touch .ty-menu__item:hover .ty-menu__item-link,
.is-hover-menu .ty-menu__item-link,
.is-hover-menu.ty-menu__item-active .ty-menu__item-link {
    background: #e05b5b;
}
.ty-menu__submenu-items {
    margin-top: 3px;
    border-color: #e05b5b;
}
```

## 「カートの内容」ブロックのカスタマイズ

「カートの内容」ブロックは`templates/blocks/cart_content.tpl`テンプレートが使用されています。

以下のコードは､表示するカートの内容をコントロールします：

```html
<div id="sw_dropdown_{$dropdown_id}" class="ty-dropdown-box__title cm-combination">
    <a href="{"checkout.cart"|fn_url}">
        {hook name="checkout:dropdown_title"}
            {if $smarty.session.cart.amount}
                <i class="ty-minicart__icon ty-icon-basket filled"></i>
                <span class="ty-minicart-title ty-hand">{$smarty.session.cart.amount}&nbsp;{__("items")} {__("for")}&nbsp;{include file="common/price.tpl" value=$smarty.session.cart.display_subtotal}</span>
                <i class="ty-icon-down-micro"></i>
            {else}
                <i class="ty-minicart__icon ty-icon-basket empty"></i>
                <span class="ty-minicart-title empty-cart ty-hand">{__("cart_is_empty")}</span>
                <i class="ty-icon-down-micro"></i>
            {/if}
        {/hook}
    </a>
</div>
```

これを以下の内容に置き換えてください：

```html
<div id="sw_dropdown_{$dropdown_id}" class="ty-dropdown-box__title cm-combination">
    <a href="{"checkout.cart"|fn_url}">
        {hook name="checkout:dropdown_title"}
            {if $smarty.session.cart.amount}
                <span class="ty-minicart-title"><i class="tt-icon-bag"></i><span class="ty-minicart-title__amount">{$smarty.session.cart.amount}</span></span>
                <span class="ty-minicart-title__price">{include file="common/price.tpl" value=$smarty.session.cart.display_subtotal}</span>
            {else}
                <span class="ty-minicart-title empty-cart"><i class="tt-icon-bag"></i></span>
            {/if}
        {/hook}
    </a>
</div>
```

カートのスタイルを追加します：

```css
/* Cart content
   ========================================================================== */
.ty-minicart-title {
    padding: 5px 23px;
    background: #e05b5b;
    color: #fff;
    font-size: 22px;
    vertical-align: top;
    position: relative;
}
.ty-minicart-title__amount {
    position: absolute;
    position: top;
    top: -8px;
    right: -12px;
    font-size: 14px;
    color: #fff;
    background: #ed1c24;
    padding: 0 6px;
    border: 1px solid rgba(85, 32, 32, 0.4);
}
.ty-minicart-title__price {
    display: inline-block;
    vertical-align: top;
    padding: 11px 9px 10px;
    background: #e05b5b;
    color: #fff;
    font-size: 14px;
}
.top-cart-content .ty-dropdown-box__title {
    padding: 0;
}
```

最終的に、それらに要素の正しいインデントとスタイルを追加します。

以下はトップパネルとヘッダーの`custom.less`ファイルの変更後です:

```css
@font-face {
    font-weight: normal;
    font-style: normal;
    font-family: 'tutorial';
    src:url('../media/fonts/tutorial.eot?-vagbvx');
    src:url('../media/fonts/tutorial.eot?#iefix-vagbvx') format('embedded-opentype'),
        url('../media/fonts/tutorial.woff?-vagbvx') format('woff'),
        url('../media/fonts/tutorial.ttf?-vagbvx') format('truetype'),
        url('../media/fonts/tutorial.svg?-vagbvx#tutorial') format('svg');
}

[class^="tt-icon-"], [class*=" tt-icon-"] {
    text-transform: none;
    font-weight: normal;
    font-style: normal;
    font-variant: normal;
    font-family: 'tutorial';
    line-height: 1;
    speak: none;

    /* Better Font Rendering =========== */
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.tt-icon-bag:before {
    content: "\e600";
}
.tt-icon-clock:before {
    content: "\e601";
}

.top-grid a {
    color: #fff;
}
.top-grid .ty-select-block__a:hover,
.top-grid .ty-select-block__a.open {
    background: none;
    color: #fff;
}

.ty-select-block__a-item {
    font-size: 11px;
}

.top-currencies .ty-select-block__a-item {
    text-transform: uppercase;
}
.top-currencies .ty-select-block__arrow {
    color: #fff;
}

.top-languages .ty-select-block__arrow {
    display: none;
}


.top-quick-links .ty-text-links__a {
    font-size: 12px;
}


div.header-grid {
    padding-top: 22px;
}

/* Delivery
   ========================================================================== */
.ty-logo-container {
    margin-top: 15px;
}

/* Delivery
   ========================================================================== */
.ty-delivery {
    margin-top: 12px;
    color: #573c27;
    font-size: 12px;
}
.ty-delivery__icon {
    margin-right: 8px;
    color: #8f979a;
    vertical-align: middle;
    font-size: 21px;
}

/* Search form
   ========================================================================== */
input.ty-search-block__input {
    padding-right: 80px;
    border-color: #fad8d3;
    color: #979595;
    font-size: 12px;
    height: 42px;
}
.ty-search-magnifier {
    top: 1px;
    right: 1px;
    bottom: 1px;
    width: 70px;
    height: auto;
    background: #e05b5b;
    color: #fff;
    font-size: 22px;
}

.tygh-header {
    border-bottom: 3px solid #e05b5b;
}

/* Main menu
   ========================================================================== */
.top-menu-grid {
    padding-top: 50px;
}
.ty-menu__items {
    background: none;
}
.ty-menu__item .ty-menu__item-link {
    padding: 8px 8px;
    color: #573c27;
    text-transform: uppercase;
    font-size: 13px;
    min-height: 0;
    margin-right: 10px;
}
.no-touch .ty-menu__item:hover .ty-menu__item-link,
.is-hover-menu .ty-menu__item-link,
.is-hover-menu.ty-menu__item-active .ty-menu__item-link,
.ty-menu__item.ty-menu__item-active .ty-menu__item-link {
    background: #e05b5b;
    color: #fff;
}
.ty-menu__submenu-items {
    margin-top: 3px;
    border-color: #e05b5b;
}

/* Cart content
   ========================================================================== */
.ty-minicart-title {
    padding: 5px 23px;
    background: #e05b5b;
    color: #fff;
    font-size: 22px;
    vertical-align: top;
    position: relative;
}
.ty-minicart-title__amount {
    position: absolute;
    top: -8px;
    right: -12px;
    font-size: 14px;
    color: #fff;
    background: #ed1c24;
    padding: 0 6px;
    border: 1px solid rgba(85, 32, 32, 0.4);
}
.ty-minicart-title__price {
    display: inline-block;
    vertical-align: top;
    padding: 11px 9px 10px;
    background: #e05b5b;
    color: #fff;
    font-size: 14px;
}
.top-cart-content .ty-dropdown-box__title {
    padding: 0;
}

/* Account
   ========================================================================== */
.ty-account-info__title {
    font-size: 12px;
}
.ty-account-info__user-arrow {
    visibility: hidden;
}
.ty-dropdown-box__title:hover,
.ty-dropdown-box__title.open {
    background: none;
}
```

## カスタマイズ結果

最後に新しく作成された「Tutorial theme」でのショップフロントがどのように見えるかを確認しましょう：

＜画像＞
