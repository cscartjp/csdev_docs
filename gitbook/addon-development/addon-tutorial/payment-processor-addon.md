---
icon: credit-card
---

# 支払方法の開発

支払方法（決済）は、いくつかのPHPとTPLファイル、`payment_processors`テーブルのエントリで構成されています。

## payment\_processorsテーブルのフィールドについて

**processor** （string） 支払い方法の名前。すべての言語で同じであるため、このフィールドは英語で入力するか、少なくともラテンアルファベットの文字を使用することをお勧めします

**processor\_script** （srting） 支払い方法のロジックを含むPHPファイルの名前。ここでファイルの名前のみを指定します（例：foo\_bar.php）。ディレクトリやその他の記号をここに置かないでください。

デフォルトでは、そのファイルは`app/payments`から読み込まれます。

支払い方法がアドオンによって追加され、`payment_processors`テーブルのアドオンフィールドが正しく入力されている場合、ファイルは`app/addons/{$addon_name}/payments`から読み込まれます。

**processor\_template** （string） 注文した顧客に表示されるテンプレートの、TPLファイルへの最初のスラッシュなしの相対パス。パスは、`design/themes/{$theme_name}/templates`からの相対パスでなければなりません。例：`views/orders/components/payments/cc.tpl`

処理方法がアドオンによって追加され、カスタムテンプレートが必要な場合は、アドオンのテンプレートフォルダからテンプレートへのパスを指定できます。

以下はPayPalアドオンのテンプレートパスです\
`addons/paypal/views/orders/components/payments/paypal_express.tpl`

レスポンシブテーマは、いくつかの標準テンプレートを含んでいます。これらは`processor_template`フィールドで指定することで使用することができます。また、別の方法として、アドオンによって追加された独自のカスタムテンプレートのベースとしてこれらのテンプレートを使用することもできます。レスポンシブテーマのテンプレートは、`design/themes/responsive/templates/views/orders/components/payments`に保存されます。

**admin\_template** （string） 支払い方法の作成および編集するときに表示されるタブテンプレートのTPLファイルの名前。このテンプレートには、管理者がアクセストークンなどの支払い方法プロパティを指定するフィールドとフォームが含まれる場合があります。

ディレクトリやその他の記号を付けずにTPLファイルの名前を入力します。\
例えば`foo_processor_template.tpl`のようになります。デフォルトでは、このファイルは`design/backend/templates/views/payments/components/cc_processors`からロードされます。

プロセッサーがアドオンによって追加され、`payment_processors`テーブルのアドオンフィールドが正しく入力されている場合、ファイルは`design/backend/templates/addons/{$addon_name}/views/payments/components/cc_processors`からロードされます。\
`{$addon_name}`はアドオンの名前を表します。

**callback** （Y/N）支払いステップの動作を決定します。

Y・・・プロセッサーは、顧客をそのサーバーにリダイレクトせずに、処理サーバーに要求を送信します。例えば、これはcURLを介して実行できます。

N・・・注文を行ったお客様は、支払いを行うために決済の処理サーバーにリダイレクトされます。

type （P/C/B）`processor_template`フィールドで指定されたテンプレートが表示される場所を決定します。

P （Payment gateway）・・・チェックアウト時に\[ 課金オプション]セクションにテンプレートが表示されます。

チェックアウトのディスパッチは`dispatch=checkout.checkout`です。

C （Checkout）・・・テンプレートはカート内に支払いボタンとして表示されます。

B （Both） ・・・テンプレートは上記の両方の場所に表示されます。

**addon** （string） -支払処理方法を追加するアドオンの識別子（例：paypal）

対象のアドオンが無効になっているかインストールされていない場合、このアドオンに依存する支払い方法はストアフロントでは表示されません。また、管理画面での支払い方法の作成時にプロセッサー自体は使用できません。

## 支払方法のスクリプト

例としてcallback が "N"の支払い処理スクリプトを解説します。

支払い方法のロジックのコアは、`payment_processors`テーブルの`processor_script`フィールドで指定されたPHPファイルです。これは、`include`ディレクティブを介して読み込まれるスクリプトです。その後、ファイルのコンテンツが実行されます。

ただし、そのファイルには2つの異なる目的があるため、コンテキスト、つまりスクリプトが含まれていた目的に応じて、スクリプトの2つの部分のうち1つだけを実行できるロジカル分岐を含める必要があります。

この例では、スクリプトは`app/addons/sample_payment/payment`にあります。これを`sample_payment_processor.php`として、必要な最小限のコードを追加します。

```php
<?php
// スクリプトは「include」ディレクティブでインクルードする必要があるため、スクリプトへの直接アクセスを防止します。「BOOTSTRAP」定数は、システムの初期化中に宣言されます。
defined('BOOTSTRAP') or die('Access denied');

//以下は、スクリプトを実行するための2つの異なるコンテキストです
if (defined('PAYMENT_NOTIFICATION')) {
    /**
     * サードパーティのサービスからのレスポンスの受信と処理
     *
     * Available variables:
     * @var string $mode The purpose of the request
     */
} else {
    /**
     * 顧客が「注文を確定」ボタンを押した後の必要なロジックを実行します
     *
     * Availablе variables:
     *
     * @var array $order_info     Full information about the order
     * @var array $processor_data Information about the payment processor
     */
}
```

考えられる両方のコンテキストを見てみましょう。

## 顧客の注文送信後に支払い受理ロジックを実行する方法

スクリプトは、注文が行われた後（つまり、ordersテーブルのエントリが作成された後）に含まれます。このコンテキストでスクリプトが処理する必要がある典型的なタスクは次のとおりです。

* 注文時に顧客が入力したデータを確認します。
* データに不備がある場合、注文ステータスを「失敗」に変更します。
* サードパーティ支払いシステムのサーバーに渡すデータを収集して準備します。
* 支払いシステムからさまざまな使い捨てトークンとアクセスキーを取得します。
* お客様が支払いサービスWebサイトで必要なアクションを実行した後、顧客が取得されるURLを生成します。
* 追加のデータを提供したり、支払いを確認したりできるように、顧客を支払いシステムのWebサイトにリダイレクトします。

支払い処理スクリプトの実行前後に行われるプロセスを調べてみましょう。

* お客様は支払い方法を選択し、\[ 注文を送信 ]ボタンをクリックします。それを行うと、POSTリクエストが`index.php?dispatch=checkout.place_order`に送信されます。
* `checkout.place_order`コントローラは`fn_checkout_place_order()`関数を呼び出し、実行します。
* 他の引数とは別にこの関数は`$_REQUEST`配列を受け入れます。関数がその配列（`$_REQUEST['payment_info']`）内の`payment_info`キーを持つ要素を見つけると、その要素の値を同じキーを持つ`$cart`配列にコピーします（ `$cart['payment_info']`）。

これにより、プロセッサスクリプトは、お客様が支払い方法を選択するときに入力したデータを処理できます。（たとえば、クレジットカード番号など）。`processor_template`テンプレートでこのようなフィールドを宣言できます。

その後、関数は注文を作成する前に必要なさまざまな確認を実行した後に、注文自体を作成します。注文ステータスは`Incomplete`（注文未完了）です。このステータスは、データベース内の`N`によって参照可能です。

支払いプロセスの開始のロジックは、`fn_start_payment()`関数の呼び出しから始まります。\
この関数が実行されると重要な変数が表示されます。これらの変数は、のちに支払い処理スクリプト内で使用できます。

* **$order\_id** （integer） —作成された注文の一意の識別子
* **$order\_info** （array） -注文に関する詳細情報

{% hint style="info" %}
HINT

この情報は`fn_get_order_info()`関数によって取得可能です。
{% endhint %}

* **$processor\_data** （array） -支払い処理に関する情報

{% hint style="info" %}
HINT この情報は`fn_get_processor_data()`関数によって確認できます。
{% endhint %}

* 次に、プロセッサスクリプトが含まれ、includeディレクティブを介して実行されます。
* スクリプトには`$pp_response`変数が宣言されている必要があります。この変数は、後で`fn_start_payment()`関数で使用され、`fn_finish_payment()`関数の呼び出しに渡されます。

## サードパーティのサービスおよび支払いシステムからの応答の受信と処理

このコンテキストでは、スクリプトは支払いシステムから送信されたHTTPリクエストを処理する必要があります。これは、顧客を店舗のウェブサイトに戻すリクエスト、または支払いシステムのサーバーから送信される追加リクエストのいずれかです。

このようなリクエストを受け入れるための`payment_notification`コントローラー（ `callback = "N"`を含む）があります。このコントローラーは、2つの必須のGETパラメーターを想定しています。

* **mode** （string） — モード名

このパラメーターの値は、`$mode`変数のプロセッサースクリプトで使用できます。値には、success、error、redirectなどがあります。

* **payment** （string） —実行するプロセッサスクリプトを含むファイルの名前

名前に.php拡張子を含めることはできません。たとえば、`sample_payment_processor`にすることができます。

`payment_notification`コントローラーが実行されると、次のようになります。

* `PAYMENT_NOTIFICATION`定数が表示されます。その値はtrueです。これにより、論理分岐により、プロセッサスクリプト内の他のコンテキストからこのコンテキストを識別することができます。
* GETパラメーターで渡された支払いプロセッサーを使用する支払い方法がアクティブかどうかを確認するためのチェックが実行されます。
* 支払い処理スクリプトは、includeディレクティブを介して実行されます。

## 支払処理スクリプトの例

callback = "N" とtype = "P"を使用して支払処理スクリプトを作成してみましょう。

これにより、顧客は支払処理サーバーにリダイレクトされ、支払い方法はチェックアウト時に**支払い方法**セクションに表示されます。

まず始めに`app/addons/sample_payment/payments`に`sample_payment_processor.php`ファイルを作成し、必要な最小限のコードを追加します。

```php
<?php
// Preventing direct access to the script, because it must be included by the "include" directive.
defined('BOOTSTRAP') or die('Access denied');

// Here are two different contexts for running the script.
if (defined('PAYMENT_NOTIFICATION')) {
    /**
     * Receiving and processing the answer
     * from third-party services and payment systems.
     *
     * Available variables:
     * @var string $mode The purpose of the request
     */
} else {
    /**
     * Running the necessary logics for payment acceptance
     * after the customer presses the "Submit my order" button.
     *
     * Availablе variables:
     *
     * @var array $order_info     Full information about the order
     * @var array $processor_data Information about the payment processor
     */
}
```

たとえば、次のコードを追加してみましょう。

```php
<?php
// Preventing direct access to the script, because it must be included by the "include" directive.
defined('BOOTSTRAP') or die('Access denied');

// Here are two different contexts for running the script.
if (defined('PAYMENT_NOTIFICATION')) {

     fn_print_r("Processing the answer");

} else {

     fn_print_r("Sending data");

}
```

このプロセッサーを使用した支払い方法を選択し、チェックアウト時に注文を送信をクリックすると、「データを送信しています」というメッセージが表示され、続いて「注文を処理しています」というメッセージが表示されます。

例えば、`app/payments`などの支払処理コードを使用することができます。

## 支払方法の説明を追加する

作成した支払処方法について説明を追加できます。追加した説明は、\[ 管理]→\[支払方法]で支払方法を作成または編集するときに\[ プロセッサ]フィールドの下に表示されます。

支払いプロセッサの説明は、`language_values`テーブル内の動的に作成された言語変数です。

言語変数の名前は`processor_description _ {$ processor_script}`です。\
`{$processor_script}`は.php拡張子のない`payment_processors`テーブルの`processor_script`フィールドの値です。

たとえば、`processor_script`の値が`foo_bar_processor.php`の場合、言語変数の名前は`processor_description_foo_bar_processor`になります。

この言語変数と支払方法の説明が`language_values`テーブルに追加されると、`foo_bar_processor`の説明が\[管理]パネルに表示されます。

この言語変数の値は、次の3つの方法で編集できます。

* 管理パネルの\[管理]→\[言語]→\[言語変数]
* アドオンのインストール中に`language_values`テーブルへのSQLクエリを実行する。
* via a migration

## IFRAMEモード

一部の決済では、iframeで読み込まれたページを利用してお客様が支払いゲートウェイとやり取りを行うことができます。これを利用することで、お客様はあなたのショップを離れることなく、決済を行うことができます。

> 📘
>
> Skrill QuickCheckout（`skrill_qc.php`）とSkrill eWallet（`skrill_ewallet.php`）を確認してください。これらの支払いプロセッサは、iframeのモードをサポートしています。\
> [GitHubにはこれらのアドオンの例があります](https://github.com/cscart/addons/tree/master/iframe\_payment)。

決済ゲートウェイをiframeモードで動作させるには、支払いプロセッサに`iframe_mode`という設定があり、その値が`Y`に設定されている必要があります。

iframeモードで決済するサンプルを以下で解説します。

```html
<input
   type="hidden"
   name="payment_data[processor_params][iframe_mode]"
   value="Y"
/>
```

お客様が支払いのステップでiframeを使った支払方法を選択すると、\[注文を送信 ]ボタンは表示されません。そのため、`checkout.post.php`コントローラー内のプロセッサーは実行されません。（この場合、お客様はチェックアウト時にメルマガを購読することはできません。）

まだ確定されていない一時的な注文番号は`order_id`の代わりに注文を識別するのに役立ちます。\
一時的な注文番号は、`TIME`定数と`user_id`から作成されます。

iframe内の支払いゲートウェイは`checkout.php`コントローラーの`process_payment`モードを介してロードされます。プロセッサーのスクリプトはincludeディレクティブを介して読み込まれます。

プロセッサスクリプトは、一時的な注文番号やセッション識別子など、必要なすべての情報を支払いゲートウェイに送信します。

ゲートウェイからの支払い通知を受信すると、注文が行われ、注文に関する情報がセッションから取得されます。

支払い通知を受け取った後、以下の処理を行う必要があります。

•注文を手動で発注済みとしてマークします。\
一時的な注文番号と注文ID（`order_id`）の相関を保存します。

`payment_notification`の受信時に`order_data`テーブルに追加されるエントリは次のとおりです。

```json
{
    "data": {
        "h-0": "order_id",
        "h-1": "type",
        "h-2": "data",
        "0-0": "作成された注文の識別子  \n（注文ID）",
        "0-1": "S  \nE",
        "0-2": "`TIME` 定数  \n一時的な注文番号"
    },
    "cols": 3,
    "rows": 1,
    "align": [
        "left",
        "left",
        "left"
        ]
}
```
