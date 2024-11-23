---
icon: language
---

# 言語変数

CS-Cart（通常版・マーケットプレイス版）やアドオンは、言語変数によって、国際化やローカライズが可能です。

あらゆる言語のテキストは、PHPコードやSmartyテンプレートファイルに直接記述されていません。言語の表示については、特別な関数が呼び出されます。言語変数のID（名前）が関数に渡され、ユーザーの環境に応じた言語でテキストが返されます。

## 言語変数の表記について

## POファイル

CS-Cart（通常版・マーケットプレイス版）とそのアドオンの翻訳データは、POファイルに保存されます。各言語には、言語変数の値を格納する個別のPOファイルが必要です。

POファイルは一般的に広く使用されている形式です。\
ローカライズに関連する多くのサービスは、POファイルをインポート・エクスポートできます。\
たとえば、Crowdinを使用してCS-Cartを翻訳することが可能です。

{% hint style="info" %}
HINT

POファイルの詳細については、[GNUプロジェクトのWebサイトをご覧ください](https://www.gnu.org/software/gettext/manual/html\_node/PO-Files.html)（英語）。
{% endhint %}

POファイルは、CS-Cartをインストールしたディレクトリのvar/langs以下のディレクトリにあります。これらのファイルは専用のアプリケーション、または一般的なテキストエディターで編集することが可能です。

## 言語変数の構造

英語のPOファイル内の言語変数は次のようになります。

```shell
msgctxt "Languages::email_marketing.subscription_confirmed"
msgid "Thank you for subscribing to our newsletter"
msgstr "Thank you for subscribing to our newsletter"
```

以下はフランス語のPOファイルです。同じ言語変数があります。

```shell
msgctxt "Languages::email_marketing.subscription_confirmed"
msgid "Thank you for subscribing to our newsletter"
msgstr "Merci de votre inscription à notre newsletter"
```

日本語では以下のようになります。

```shell
msgctxt "Languages::email_marketing.subscription_confirmed"
msgid "Thank you for subscribing to our newsletter"
msgstr "メールマガジンの購読誠にありがとうございます。"
```

* `msgctxt` - メッセージコンテキスト。CS-Cartやアドオンでは、このメッセージコンテキストを使用して、SmartyテンプレートとPHPコード（プレフィックスを除く）に表示される言語変数の名前を保存します。

POファイルには、文字列の翻訳、アドオンの名前と説明、さまざまな設定とセクションの名前が保存されます。そのため、異なるタイプのデータの翻訳には異なる接頭辞が付いています。PHPまたはTPLファイルに表示される言語変数の名前は「Languages::プレフィックス」で始まる必要があります。

* `msgid` - 翻訳元の言語の言語変数の値です。ここには英語で値を指定することをお勧めします。
* `msgstr` - 翻訳元のテキストからターゲット言語への翻訳です。翻訳元の言語のPOファイルでは、`msgstr`は`msgid`と同じになります。

## プレースホルダー

商品名や各種値など、変更可能なデータを言語変数に含める必要がある場合があります。この場合は、次のように角括弧で囲まれたプレースホルダーを使用します。

```shell
msgctxt "Languages::admin_text_letter_footer"
msgid "E-shop of [company_name]."
msgstr "E-shop of [company_name]."
```

Smartyテンプレートでプレースホルダーがどのように使用されるかを確認してください。

## 複数形

多くの言語では、単数形と複数形が異なる場合があります。例えば、次の2つのフレーズを考えてみましょう。

* (The delivery will take) 1 day.
* (The delivery will take) 2 days.

異なるフォームで言語変数を作成する場合は、`|`記号を使用して`msgid`および`msgstr`の値を区切ります。また、番号に置き換えられる`[n]`プレースホルダーを追加します。2つの形式の言語変数の例を次に示します。

```shell
msgctxt "Languages::n_days"
msgid "[n] day|[n] days"
msgstr "[n] day|[n] days"
```

一部の言語（例えばロシア語など）には複数形が複数あります。\
ターゲット言語に元の言語よりも複数形がある場合の例：

* `msgid`は元の言語と同じままです。
* `msgstr`には、ターゲット言語のすべての形式の説明があります。

以下はロシア語の言語変数です。一つの言語変数に2つの複数形があります。

```shell
msgctxt "Languages::n_days"
msgid "[n] day|[n] days"
msgstr "[n] день|[n] дня|[n] дней"
```

複数の単語形式をリストにする場合、単数形が常に最初に記述されます。\
さまざまな言語のフォームがリストされる順序は、言語の複数規則に準拠しています。

## 言語変数の使用方法

## PHPコード内

コア関数`__`は、言語変数の値を表示するためにPHPコード内で使用されます。

```php
<?php
function __($var, $params = array(), $lang_code = CART_LANGUAGE);
```

必須の引数は、言語変数の名前です。2番目の引数はプレースホルダーの値を渡します。3番目の引数ではターゲット言語を指定します。

> 📘
>
> デフォルトでは、ターゲット言語はユーザーの（環境の）ロケールによって決定されます。

`__`関数の例:

```php
<?php
$confirmed_text = __('email_marketing.subscription_confirmed');
fn_set_notification('I',$confirmed_text, $msg);
```

## Smartyテンプレート内

`__`を使用して、言語変数をSmartyテンプレートに挿入できます。\
文字列を中括弧で囲みます。以下のコードは、Smary内でhelloという言語変数を挿入する方法です。

```smarty
{__("hello")}
```

このような文字列は、テンプレートのコンパイル中にSmartyラッパークラスの`__`メソッドが呼び出され、置き換えられます。次に、そのクラスは上記のコア関数を呼び出します。

## プレースホルダー

以下は、テンプレート内で使用されたプレースホルダーを持つ言語変数の例です。

```smarty
<p>
   {__("admin_text_letter_footer", ["[company_name]" => $settings.Company.company_name])}
</p>
```

## 複数形

複数形の言語変数を使用する場合、`__`関数の2番目の`[n]`パラメーターでプレースホルダーの代わりに置換された数値を渡します。

```php
<?php
$return[$service_code]['delivery_time'] = __("n_days", array($shipment->GuaranteedDaysToDelivery));
```

渡された番号に応じて、適切なフォームが自動的に選択されます。\
英語の場合の例：

* 0 days
* 1 day
* 3 days

## 言語変数の編集

POファイルを変更する以外にも言語変数を編集する方法があります。

* \[管理]パネルで、\[`一般設定`] →\[`言語`]→\[\`言語変数]\*\*に移動します。
* REST API経由でlangvarsエンティティを使用します。
