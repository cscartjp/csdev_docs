---
icon: sliders
---

# 設定

CS-Cartには2種類の設定値があります。

* `コア設定` CS-Cart一般に影響し、インストールしたアドオンに関係なく機能します。
* `アドオン設定` それぞれのアドオンの動作に影響します。

設定はRegistryおよびSettingsクラスを使って管理されます。\
設定に関する情報は、データベースの名前が`settings_`で始まるテーブルに保存されます。\
管理者は、管理パネルで`コア設定`と`アドオン設定`を操作できます。

## 管理パネルでの設定の操作

コアおよびアドオンの設定は、管理パネルで表示・編集できます。\
設定は、設定が表示される場所や権限を`edition_type`により定義します。\
（添付資料1：edition\_typeの有効な値を参照）

* `MVE`（マーケットプレイス版）-この設定はマーケットプレイス版にのみ表示され、且つ左上隅のドロップダウンリストで\[ すべてのベンダー]モードが選択されている場合にのみ表示されます。
* `ULT`（СS-Cart通常版）-設定は、すべてのストアモードとストアフロントが選択されたときに表示されます。edition\_typeに応じて、各ストアフロントに設定用の独自の値を設定できます。ストアフロントが選択されたときに使用できない設定は非表示になります。

## コア設定

コア設定は、管理パネルの\[`基本設定`]にあります。\
設定はいくつかのセクションに分けられています。

* 全般
* 表示設定
* 運営会社
* ショップ
* 注文手続き
* Eメール
* サムネイル
* サイトマップ
* アップグレード
* セキュリティ設定
* ログ
* 販売レポート
* 設定ウィザード

＜画像＞

設定または設定のあるセクションは、`edition_type`によって表示される場合と表示されない場合があります（添付資料1：edition\_typeの可能な値を参照）。\
設定は、ストアフロントまたはベンダーによって異なる値を持つことができます。\
通常、コア設定はセクションに属している必要があり、設定がセクションに属していない場合、設定は非表示になります。

## アドオン設定

アドオン設定は、`アドオン`→`アドオンの管理`で管理されています。\
ほとんどのアドオンには独自の設定があり、アドオンの設定を開くには、次の2つの方法のいずれかを使用してください。

* アドオンの名前をクリック
* アドオンにカーソルを合わせると歯車アイコンが右側に表示されます。それをクリックして、設定を選択します。特定のedition\_typeが設定されている場合、設定が利用できない場合があります。

以下は`SEOアドオン`の設定です。

＜画像＞

## コードによる設定の操作

設定は`Registry`および`Settings`クラスを介してコードで管理されます。\
これらのクラスは、設定の値を追加、変更、取得するメソッドを提供します。

## Registry クラス

この`Registry`クラスには、アプリケーションの様々な設定を制御しています。\
このクラスは、設定のキャッシュも実行します（\_var/cache/registry\_ディレクトリを参照）。\
`Registry`クラスで設定を制御する方法は次のとおりです。

* コア設定またはアドオン設定の値の取得：

```php
<?php
Registry::get('settings.[セクション名].[設定名]');
Registry::get('addons.[アドオン名].[アドオンの設定名]');
```

* コア設定またはアドオン設定値の変更：

```php
<?php
Registry::set('settings.[セクション名].[設定名]', '[新しい設定値]');
Registry::set('addons.[アドオン名].[アドオンの設定名]', '[新しい設定値]');
```

## Settingsクラス

この`Settings`クラスは、データベースに保存されているCS-Cartの設定を操作するための拡張APIです。このクラスには多くのメソッドがあり、設定値の読み取り、変更、および削除などが可能です。

たとえば、`Settings`クラスで`elements_per_page`（1ページに表示するその他の要素数）（\[`基本設定`]→\[`表示設定`]）を管理する方法は次のとおりです。

* 設定（の存在）を確認する

```php
<?php
Settings::instance()->isExists('elements_per_page', 'Appearance');
```

* 設定の値を取得する

```php
<?php
Settings::instance()->getValue('elements_per_page', 'Appearance');
```

設定値の変更

```php
<?php
Settings::instance()->updateValue('elements_per_page', $new_value, 'Appearance');
```

## アドオン設定

## addon.xmlで設定を定義する

アドオン設定は、そのアドオンのディレクトリにある`addon.xml`ファイルで定義されています。\
設定のリストが`addon.xml`の`<settings>`セクションに追加されています。\
`<settings>`セクションの属性は次のとおりです。

* `layout`-アドオンの設定でウィンドウの外観を決定します。デフォルトでは、アドオンの設定はポップアップウィンドウに表示されます。`layout="separate"`を指定すると、設定は個別のページで表示されます。
* `edition_type` - CS-Cartのどのエディションで動作するかを指定します。

> 📘
>
> これらのパラメーターは両方ともオプションです。

たとえば、`<settings layout="separate" edition_type="ROOT">`は、\
設定が別のページに表示され、「`すべてのショップモード`」や「`すべてのベンダーモード`」で使用できることを意味します。

## `<settings>`セクションの構造

```xml
<settings layout="separate" edition_type="ROOT,ULT:VENDOR">
        <sections> <!-- アドオンの設定セクション -->
            <section id="section1"> <!-- 設定パネルのセクション。設定のセクションには、次の属性があります。
                id—設定のセクションの識別子(setting_id):
                Registry::get('addons.[addon_id].[setting_id]')
                edition_type—利用できるエディションや権限を設定。複数の場合はカンマで区切る
                -->
                <items> <!-- 設定のアイテム -->
                    <item id="header"> <!-- The settings
                        id—設定の識別子
                        edition_type—利用できるエディションや権限を設定。複数の場合はカンマで区切る
                    　　　　　　-->
                        <type>header</type> <!-- 指定可能な値については別のページを参照:
                              input, textarea, password, checkbox, selectbox,
                              multiple select, multiple checkboxes,
                              countries list, states list,
                              file, info, header, template
                              -->
                        <default_value>radio_1</default_value> <!-- 設定のデフォルト値を指定できます -->
                        <variants> <!-- 次のタイプでは選択肢を設定できます:
                        selectbox, multiple select,
                        multiple checkboxes, combo select -->
                            <item id="radio_1"></item> <!-- id—選択肢の識別子-->
                        </variants>
                </items>
            </section>
</settings>
```

## 設定を操作するための関数

設定の値の選択肢は`variants.functions`および`actions.functions`の2種類の関数を使って変更・追加が可能です。

* `variant.functions`は、データベースにそれらの選択肢を記録せずに、選択肢のリストを生成します。設定の値の選択肢は、設定ページを開くたびに動的に生成されます。\
  これらの機能は、\_\[addon\_name]/schemas/settings\_ディレクトリの中の`variants.functions.post.php`ファイルに記述してください。\
  関数の名称は次のようにする必要があります\
  `fn_settings_variants_addons_[addon_name]_[setting_name]()`

例）\
`my_changes`アドオンに設定値`exampleID`が設定されていると仮定します。\
この設定の値の選択肢を生成するために、\_my\_changes/schemas/settings\_ディレクトリの`variants.functions.post.php`ファイルを作成します。このファイルには、`fn_settings_variants_addons_my_changes_example()`という関数が含まれている必要があります。

* `actions.functions`はアドオンがインストール／アンインストールされるときに呼び出されます。これらの関数は、アドオンのインストール中にデータベースにアドオンの設定を保存するフィールドを生成します。また、アドオンがアンインストールされるときにこれらのフィールドを削除します。

これらの関数は、`[addon_name]/schemas/settings`の`actions.functions.post.php`ファイルに配置する必要があります。関数の名前は次のように命名してください。

my\_changesアドオンにexampleID が設定されていると仮定します。この設定用の関数を作成するには、\_my\_changes/schemas/settings\_ディレクトリに`actions.functions.post.php`ファイルを作成します。このファイルには、`function fn_settings_actions_addons_my_changes_example()`という関数が含まれている必要があります。

## 設定データの保存

コア設定およびアドオン設定に関するデータはデータベースに保存されます。これらデータを含むテーブルの名前は`settings_`で始まります。

この`settings_sections`テーブルには、設定が属するセクションのリストが含まれています。このテーブルには次のフィールドがあります。

* `section_id`-セクションの識別子。
* `parent_id`-親セクションの識別子。
* `edition_type`— 設定のセクションが利用可能となる情報。このフィールドは、セクションがCS-Cart通常版/マーケットプレイス版で使用可能かどうか、および管理者が持つ必要があるアクセスレベルを決定します。このフィールドには、1つまたは複数の値をコンマで区切ることで含めることができます。（例：settings\_：）
* `name`-セクションの名称
* `position`-設定セクションでの表示位置。
* `type`-セクションのタイプ：
* `CORE`—コア設定のセクションです。このタイプのすべてのセクションは、管理パネルの\[設定]ページにリストアップされています。（settings.phpコントローラーを参照）
* `ADDON`-アドオン設定を含むセクションです。このタイプのセクションはすべて、ポップアップウィンドウまたは別のページに表示され、アドオンの設定が含まれます（addons.phpコントローラーを参照）。
* `TAB`-設定のサブセクション（タブ）です。コア設定とアドオン設定の両方にタブを設定できます。各サブセクションには、parent\_idフィールドに親セクションのIDがあります。
* `SEPARATE_TAB`-設定のサブセクション（タブ）です。コア設定とアドオン設定の両方にタブを設定できますが、このタブは個別のコンテナとして追加されます。各サブセクションには、parent\_idフィールドに親セクションのIDがあります。

{% hint style="info" %}
HINT

COREまたはADDONタイプのセクションのみがサブセクションを持つことができます。
{% endhint %}

データベースのエントリの例は次の表のとおりです。

| section\_id | parent\_id | edition\_type | name       | position | type |
| ----------- | ---------- | ------------- | ---------- | -------- | ---- |
| 4           | 0          | ROOT,VENDOR   | Appearance | 20       | CORE |

管理パネルでセクションのIDを確認する方法は次のとおりです。

`コア設定`：\[基本設定]ページに移動し、セクションを選択します。セクションの名前は、URLのsection\_idパラメーターとしてブラウザーのアドレスバーに表示されます。\
たとえば、表示設定セクションには`section_id`として`Appearance`があります。URLは次のようになります。\_example.com/admin.php?dispatch=settings.manage§ion\_id=Appearance\_

`アドオン設定`：`アドオン`→`アドオンの管理`に進み、アドオンの歯車アイコンをクリックします。\[アンインストール]アクションにカーソルを合わせると、ブラウザーウィンドウの下部にURLが表示されます。そのURLにはコードでアドオンを参照できる名前のaddonパラメーターが含まれます。

たとえば、「この商品を買った人はこんな商品も買っています」アドオンではアドオンのIDは`customers_also_bought`と名付けられます。URLは次のようになります（この例にはURLの一部しか含まれていないことに注意してください）：\
_example.com/admin.php?dispatch=…\&addon=customers\_also\_bought&…_

## \_settings\_objects\_テーブル

この`settings_objects`テーブルには、コア設定とアドオン設定のリスト、およびそれらの値が含まれています。このテーブルには、次の主要なフィールドがあります。

* `object_id`-設定の識別子。
* `edition_type`— この設定が利用可能になるCS-Cartのエディションや権限に関する情報。
* `name`-設定の名称
* `section_id`-設定が属するセクションの識別子。
* `section_tab_id`-タブの識別子（設定がタブにある場合）。
* `type`-設定のタイプ。
* `value`-設定の値。
* `position`-設定の表示順

データベース内のエントリー例を次に示します— `company_name`（\[`基本設定`]→\[`運営会社`]`会社名`）：

| object\_id | edition\_type   | name          | section\_id | section\_tab\_id | type | value | position |
| ---------- | --------------- | ------------- | ----------- | ---------------- | ---- | ----- | -------- |
| 1          | ROOT,ULT:VENDOR | company\_name | 5           | 0                | I    | 会社名   | 0        |

設定の識別子（object\_id）を確認するには、設定のあるページのコードを表示します。たとえば、`elements_per_page`（1ページに表示するその他の要素数）の識別子を確認しましょう（一般設定表示設定）。

＜画像＞

設定上でマウスの右ボタンをクリックし、要素の検証を選択します。設定の識別子は、`name`属性の角括弧\[]にあります。その値が`object_id`です

＜画像＞

## _settings\_variants_ テーブル

`settings_variants`テーブルには、次の種類の設定の選択肢：selectbox、multiple select、multiple checkboxesのリストが含まれます。管理者は、設定の値として事前に定義された選択肢のいずれかを選択することができます。

たとえば、\[`基本設定`]→\[`ログ`]セクションに\[ `注文`]という設定があります。\
選択された選択肢に関する情報は、`log_type_orders`として保存されています（`settings_objects`テーブルを参照）。この設定で使用可能なすべての選択肢のリストは`settings_variants`に保存されます。

＜画像＞

object\_idフィールドごとに、設定で可能なすべての値のリストを取得できます。テーブルに設定の選択肢がない場合、選択肢は`variant.functions`を介して動的に形成されることを意味します。

## `settings_descriptions`テーブル

この`settings_descriptions`テーブルには、セクションの名前、設定、選択肢、ツールチップの言語データが含まれています。テーブルの主なフィールドは次のとおりです。

* object\_id-設定の識別子。
* object\_type-次のタイプのいずれか：
* V-設定値のバリアントの名前。（settings\_variantsテーブルを参照）
* O-設定の名前（settings\_objectsテーブルを参照）。
* S-セクションの名前（settings\_sectionsテーブルを参照）。
* lang\_code-言語コード。
* value-指定された言語の設定/セクション/値の名前。
* tooltip-設定/セクション/値のツールチップ

このテーブルには、現在インストールされているすべての言語の情報が含まれています。\
特定の値をデータベースで検索するときは`object_type`を`lang_code`や`object_id`と同様に考慮する必要があります。

## 添付資料1：edition\_typeの値

{% hint style="info" %}
HINT

アドオンには、`edition_type`をカンマで区切って複数の値を含めることができます。値を指定しない場合、`ROOT`と同じように動作します。
{% endhint %}

| 値                | 説明                                                                                                      |
| ---------------- | ------------------------------------------------------------------------------------------------------- |
| `NONE`           | 設定はインターフェイスに表示されず、編集できません。                                                                              |
| `ROOT`           | 設定はインターフェイスに表示され、編集可能ですが、「全てのストア/全てのベンダー」モードでのみです。                                                      |
| `VENDOR`         | 「全てのストア/全てのベンダー」モードと、特定のストアフロント/ベンダーが選択されたときに表示されます。カンマの後にULT:NONEを追加すると、ストアフロントが選択されている場合にのみ設定を編集できます。 |
| `MVE:NONE`       | マーケットプレイス版には表示されません。設定をCS-Cart通常版に表示するには、カンマの後に別の値、ULT:ROOT.などを追加する必要があります。                             |
| `MVE:ROOT`       | マーケットプレイス版に表示されますが、「全ての出品者」モードでのみ表示されます。                                                                |
| `ULT:NONE`       | CS-Cart通常版には表示されません。マーケットプレイス版にのみ設定を表示するには、カンマの後に、例えば ROOT や MVE:ROOTのような別の値を追加する必要があります。               |
| `ULT:ROOT`       | CS-Cart通常版に表示されますが、「全てのショップ」モードが選択されている場合のみです。                                                          |
| `ULT:VENDOR`     | この設定は、「全てのショップ」モードと特定のショップフロントが選択されたときに表示されます。カンマの後に ULT:NONEを追加すると、ストアフロントが選択されている場合にのみ設定を編集できます。      |
| `ULT:VENDORONLY` | 設定はインターフェイスに表示されませんが、コードを使用して特定のストアフロント向けに表示されます。                                                       |

## 添付資料2：設定用の値

設定のタイプは、パラメーターの`addon.xml`ファイルで指定されます（セクションの構造を参照）。

例として、`new_setting`の設定を追加しましょう。これは`checkbox`タイプで`N`（チェックボックスをオフにした）をデフォルト値とします。この設定は、アドオンの`addon.xml`ファイルに追加する必要があります。実際のコードは次のようになります。

```xml
<sections>
   <section id="general">
       <items>
           <item id="new_setting"> //新しい設定値
           <type>checkbox</type> // ここで種類を設定します
           <default_value>N</default_value> //デフォルト値
            </item>
         </items>
   </section>
</sections>
```

では次の値を指定できます（括弧内の文字は、これらのタイプがデータベースに保存される際の`Type`の値です）。

* `selectable_box`（`B`）-2つのリストで構成される複数選択ボックス：1つのリストは可能な値、もう1つのリストは選択された値
* `сheckbox`（`С`）—チェックボックス
* `hidden`（`D`）-ユーザーには表示されない非表示の設定
* `template`（`E`）-カスタムテンプレートのファイル名。テンプレートは、`design/backend/templates/addons/[addon名]/settings`ディレクトリに配置する必要があります。設定の代わりにテンプレートのコンテンツが表示されますが、これはそのアドオンがアクティブな場合のみです。
* `file`（`F`）-ファイル選択のフィールド（アップロードなど）
* `checkboxes`（`G`）-複数の選択肢を選択できるチェックボックスのリスト。`variant.function`の関数を介して選択肢が追加されます。これを利用して`selectbox`（`K`）設定の選択肢のリストを作成できます。
* `header`（`H`）-設定のあるブロックまたはコンテンツの見出し
* `input`（`I`）-テキストを入力するための入力フィールド

```xml
<type>input</type>
```

* `selectbox`（`K`）-選択肢の中からいずれかを選択できるドロップダウンリスト：使用可能な選択肢は、選択した`checkboxes`（`G`）設定の値に依存します。

```xml
<type>selectbox</type>
```

* `multiple_select`（`M`）-スクロール可能な値のリストと、複数の値から選択する機能。このリストを追加するとき、`variants`パラメーターで可能な値を指定します。

```xml
<item id="multiple_select">
   <type>multiple select</type>
   <variants>
       <item id="select_box_1"></item>
       <item id="select_box_2"></item>
       <item id="select_box_3"></item>
   </variants>
</item>
```

* `multiple_checkboxes`（`N`）-複数のチェックボックスをチェックする機能を持つチェックボックスのリスト。リストを追加するとき、チェックボックスは`variants`パラメーターで指定されます
* `info`（`O`）- `<handler>`パラメータで渡された関数の結果が表示されます。例として設定で呼び出される`fn_get_information`関数の出力を表示してみましょう。

```xml
<item id="information">
   <type>info</type>
   <handler>fn_get_information</handler>
</item>
```

* `password`（`P`）-パスワードを入力するためのフィールド。入力したすべての記号は\*でマスク表示されます。

```xml
<type>password</type>
```

* `radiogroup`（`R`）—ラジオボタンのグループ。一度に選択できるのは1つだけです。ボタンの値は、variantsパラメーターで指定されます。

```xml
<item id="radiogroup">
   <type>radiogroup</type>
   <default_value>radio_2</default_value>
   <variants>
       <item id="radio_1"></item>
       <item id="radio_2"></item>
   </variants>
</item>
```

* `selectbox`（`S`）-選択肢の中から1つだけ選択できるドロップダウンリストです。リストを追加するときは、variantsパラメーターで可能なバリアントを指定します。

```xml
<item id="selectbox">
   <type>selectbox</type>
   <variants>
       <item id="select_box_1"></item>
       <item id="select_box_2"></item>
   </variants>
</item>
```

* `textarea`（`T`）-複数行のテキストを入力するためのフィールド
* `input`（`U`）-数字のみを入力するフィールドです。数字以外は削除されます。
* `states_list`（`W`）-都道府県または地域を選択するためのドロップダウンリスト
* `countries_list`（`X`）—国を選択するためのドロップダウンリスト
* `permanent_template`（`Z`）-カスタムテンプレートのファイル名。アドオンが無効になっていても、常に利用可能です。ファイルは\_design/backend/templates/addons/\[addon名]/settings\_ディレクトリに配置する必要があります。たとえば`permanent_template.tpl`のコンテンツを表示する`permanent_template`設定を追加してみましょう。

```xml
<item id="permanent_template">
    <type>permanent_template</type>
    <default_value>permanent_template.tpl</default_value>
</item>
```
