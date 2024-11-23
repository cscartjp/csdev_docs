---
icon: microscope
---

# Add-on スキーマー

## addon.xml

このファイルは全てのアドオンにおいて必須となります。

アドオン識別子や名称、説明、リストの順番などのアドオンのパラメーターが記述されています。

[こちら](https://gist.github.com/cscartjp/e2b6d1a7bd57f22946a3)でaddon.xml の全てがコメントされたサンプルファイルをダウンロードすることができます。

管理パネルで\_アドオン管理\_が表示される度に、CS-Cartはapp/addons/ 内の全てのディレクトリをスキャンし、インストール済のアドオンのリストと未インストールのアドオンをリスト化するためにaddon.xml ファイルを読み込みます

アドオンのインストール時にCS-Cartはaddon.xmlのデータをデータベースに保存します。\
（アドオンで作業するたびにファイルから読み込まれる設定を除く）

アドオン管理のページでアドオンを編集する際には、すでにインストールされたアドオンはaddon.xml を呼び出します。

## addon.xmlの場所

addon.xmlファイルはアドオンのルートディレクトリに配置する必要があります：

_app/addons/\[addon\_name]/addon.xml_

## スキーマー

**Scheme 3.0** は、CS-Cartバージョン4.2.4以降で使用されます。

古いスキーマーを持つアドオンは\_【管理パネル】 >【アドオン】>【アドオン管理】\_ で表示されません。適切なスキーマーへの変換なしでは正しく作動しません。

## スキーマーの構造

\_addon.xml\_ファイルの構造は以下に全て記述されています。アドオンを開発したい場合は、このスキームをメモとして使用してください。

## トップレベル

```xml
<addon scheme="3.0" edition_type="ROOT,ULT:VENDOR">
    <!-- Add-on identifier. It must be equal to the catalog name in which the add-on resides -->
    <id>sample_addon_3_0</id>
 
    <!-- Add-on version -->
    <version>1.0</version>
 
    <!-- Add-on native (default) language. An optional parameter; if not specified, the language will be recognized as English (EN)-->
    <default_language>en</default_language>
 
   <!-- Add-on priority. The higher the priority the later the add-on is connected -->
    <priority>100</priority>
 
    <!-- Status to be set after the add-on installation (active/disabled); "disabled" by default -->
    <status>active</status>
 
    <!-- Authors contact information -->
    <!-- All fields are optional -->
    <authors>
        <author>
            <name>CS-Cart</name>
            <email>contact@cs-cart.com</email>
            <url>http://cs-cart.com</url>
            <phone>+1 999 1234 5678</phone>
        </author>
    </authors>
```

属性:

* `scheme` - アドオンのスキーマーバージョン。「3.0」が使用されています。
* `edition_type` - 設定関連の要素で使用可能なオプションの属性\
  １つまたはその他の設定が可能であるというCS-Cartのエディションを定義しています。\
  空のままにすると、親要素の値が使用されます。\
  後者がセットされていないと、値はROOTとみなされます。\
  利用可能な値: ULT:ROOT, ULT:VENDOR, MVE:ROOT, MVE: VENDOR, ROOT そして VENDOR.
* `id` - アドオンの固有の識別子。アドオンのディレクトリ名と等しくなければなりません。
* `version` - アドオンのバージョン
* `default_language` - アドオンのデフォルト言語 。指定がない場合は\_英語(EN)\_となります。
* `name`- デフォルト言語でのアドオン名
* `description` - デフォルト言語でのアドオンの簡単な説明
* `priority` - アドオンの優先順位。優先順位が高ければ、順番として後でアドオンが接続されます。
* `status` - アドオンがインストールされた直後のステータス(有効/無効)を定義します。デフォルトでは【無効】になってます。

***

## 互換性・ブロック

```xml
<!-- Block for the other add-ons compatibilities or system preferences descriptions -->
    <compatibility>
        <!-- Dependencies. These add-ons must be installed in order to install the current one; otherwise an error message will be displayed -->
        <dependencies>discussion,form_builder</dependencies>
 
        <!-- Conflicts. These add-ons will be automatically disabled before the current add-on installation starts and the notification will be displayed-->
        <conflicts>catalog_mode,reward_points</conflicts>
 
        <!-- Minimal/Maximal cart version. Min or Max parameter can be optional -->
        <core_version>
            <min>4.0.3</min>
        </core_version>
 
        <!-- Cart edition: ULTIMATE/MULTIVENDOR -->
        <core_edition>
            ULTIMATE,MULTIVENDOR
        </core_edition>
 
        <!-- Minimal/Maximal PHP version. Min or Max parameter can be optional -->
        <php_version>
            <min>5.3.0</min>
            <max>5.5.0</max>
        </php_version>
 
        <!-- Dependencies on extra extensions -->
        <php_extensions>
            <!-- Checking Extension availability. Must be installed: Y. Not to be installed: N -->
            <calendar>
                <supported>Y</supported>
            </calendar>
 
            <!-- Extension id (phpinfo) and required version -->
            <json>
                <min>1.1.0</min>
                <max>1.2.2</max>
            </json>
        </php_extensions>
    </compatibility>
```

* `compatibility`- 他のアドオンとの互換性を定義するためのブロック
* `dependencies` - このセクションにリストされているアドオンは、このアドオンをインストール際に必須となります。これらのアドオンがインストールされなかった場合はエラーとなります。
* `conflicts` - このセクションにリストされているアドオンは、このアドオンのインストールを行う前に自動的に無効になります。インストールが完了した後にこれらが無効になったことを示す通知が表示されます。

***

## ライセンス・ブロック

```xml
<license></license
```

***

## タブの順番・ブロック

```xml
<tab_order>prepend</tab_order>
```

***

## 設定・ブロック

サンプルは[こちら](https://gist.github.com/cscartjp/e2b6d1a7bd57f22946a3)\
。

* `settings` - アドオンの設定ブロック（オプション）

**属性**:

* `layout` - 設定ページをどのように表示するかを定義します。(popup/separate )デフォルトはpopup
* `edition_type` -Top level セクションの`edition_type`属性を参照してください。
* `sections` - アドオンの設定項目タブのリスト
* section - タブ項目

**属性**

* `id`- タブの識別子。この設定は`Registry::get('addons.[addon_id].[setting_id]')` を使ってアクセスできます。
* `edition_type` - Top level セクションの`edition_type`属性を参照してください。
* `name` - デフォルトの言語での設定タブの名称
* `items` - タブ内の設定項目リスト
* `item` - 設定項目

**属性**

* `id` -設定の識別子
* `edition_type` - **Top level** セクションの`edition_type`属性の記述を参照してください。
* `type` - 要素タイプ：入力、テキストエリア、パスワード、チェックボックス、選択ボックス、複数選択、複数のチェックボックス、国リスト、州のリスト、ファイル、情報、ヘッダー、テンプレート（input, textarea, password, checkbox, selectbox, multiple select, multiple checkboxes, countries list, states list, file, info, header, template）
* `name` - デフォルト言語での設定名
* `translations` - 翻訳ブロックセクションの翻訳ブロックの記述を参照してください。
* `tooltip` - ツールチップ
* `default_value` - デフォルト値　選択ボックスや複数選択のように複数のバリアントを持つアイテムの場合はそのID
* `variants`- 選択ボックス、複数選択、複数のチェックボックス、コンボ選択のための値
* `item`- アイテムの値

**Attributes:**

* `id` - バリアント識別子
* `name` - バリアント名
* `translations` - **翻訳**ブロックセクションに記載されている`Translations`ブロックに似て、唯一`for`属性に使用される。
* `handler` - \_info\_タイプ設定のハンドラー関数。特定された関数の戻り値は出力テキストとして使用されます。

***

## クエリー・ブロック

```xml
<!-- Additional database queries -->
    <queries>
        <!-- If parameter "for" equals "install" or is not set, the query is executed during the add-on installation -->
        <item for="install">
        CREATE TABLE `?:addon_test_123456789` (
            `queue_id` mediumint NOT NULL auto_increment,
            PRIMARY KEY  (`queue_id`)
        ) ENGINE=MyISAM DEFAULT CHARSET=UTF8
        </item>
        <!-- If the "editions" attribute is given, the request will be executed only for the editions that are defined in it (separated with commas) -->
        <item for="install" editions="ULTIMATE,MULTIVENDOR">
            ALTER TABLE `?:addon_test_123456789` ADD company_id INT NOT NULL DEFAULT 0;
        </item>
        <!-- If the parameter "for" equals "uninstall", the query is executed during the add-on uninstallation -->
        <item for="uninstall">DROP TABLE IF EXISTS `?:addon_test_123456789`</item>
    </queries>
```

* `queries` - 追加のデータベースクエリ
* `item`- データベースクエリのアイテム

Attributes:

* `for` - If this parameter is set to install or is not set, the query is executed during the add-on installation; if this parameter is set to uninstall, the query is executed during the add-on uninstallation.

このパラメーターが空の場合は\_install\_となります。クエリはアドオンのインストール時に実行されます。\_uninstall\_と設定されている場合は、アドオンのアンインストール時に実行されるクエリとなります。

* `editions` - CS-Cartエディションのリスト（カンマ区切り）。この属性が与えられた場合、リクエストは指定されたCS-Cartのエディションのみで実行されます。

***

関数・ブロック

```xml
<!-- User-defined functions called on certain events:
            before_install - before the add-on installation
            install - after the installation of the add-on, its templates, settings and language variables but before its activation and cache clearing
            uninstall - before uninstallation
            -->
    <functions>
        <item for="install">fn_google_export_add_features</item>
        <item for="install">fn_google_export_add_feed</item>
        <item for="uninstall">fn_google_export_remove_features</item>
        <item for="uninstall">fn_google_export_remove_feed</item>
    </functions>
```

* `functions` - 特定のイベントで呼び出されるユーザー定義の関数
* `before_install` - アドオンのインストール前に実行
* `install` - アドオンのインストール完了時に実行。テンプレートや設定、言語がインストールされた後。ただしアドオンが有効化されキャッシュがクリアされる前
* `uninstall` - アンインストールの前
* `item` - 関数

**Attributes:**

* `for` - 関数のきっかけとなるイベント名。この関数は特定のイベントが起こった場合に呼び出されます。可能な値：_before\_install, install, uninstall_
