---
icon: turtle
---

# 「Hello World」チュートリアル

## はじめに

それぞれのアドオンは`app/addons`ディレクトリ内にアドオン専用のディレクトリがあります。

アドオンの基本的な情報は`addon.xml`ファイルに記述されています。そのXML内に記述されているのは、アドオンのID（識別子）、プライオリティ（動作の優先順位）、バージョン、表示名などです。\
基本的に、アドオンはこのXMLファイルのみで動作します。

※これだけではアドオン一覧に表示されるだけでこのアドオンは何も機能は持っていません。

このようなアドオンはaddon.xml ファイルでの設定と翻訳を定義すると、言語設定をインストールすることができます。

CS-Cartではアドオンを自由に作成することができるということを覚えておいてください。

## Hello, World!

* CS-Cartをインストールしたルートディレクトリから**app/addons**ディレクトリに移動します。
* **hello\_world**というディレクトリを作成し、そのディレクトリに移動して下さい。
* このディレクトリがアドオンの専用のディレクトリとなります。

{% hint style="warning" %}
重要

アドオンのディレクトリ名は、XMLファイルで定義したアドオンIDと同じであることが重要です。そうでないと、アドオンが正常にインストールされません。
{% endhint %}

前にも述べたように、アドオンの基本構造は\_addon.xml\_ ファイルで定義されています。このファイルを作成してみましょう。 XMLファイルでは、いくつか基本的なアドオン属性を定義します：

## addon.xml

* **id** — アドオンの識別子。アドオンのディレクトリ名と同じでなければなりません。 今回の場合は**hello\_world**として下さい。
* **version** — このアドオンのバージョン。1.0に設定して下さい。
* **priority** — 優先順位です。**100500**のように大きい数値を設定します
* **default\_language** — デフォルト言語

> /app/addons/hello\_world/addon.xml

\_addon.xml\_ファイルは以下のようになるはずです：

```xml
<?xml version="1.0"?>
<addon scheme="3.0">
   <id>hello_world</id>
   <version>1.0</version>
   <priority>100500</priority>
   <position>0</position>
   <status>active</status>
   <default_language>en</default_language>
   <auto_install>MULTIVENDOR,ULTIMATE</auto_install>
</addon>
```

[ダウンロード](https://gist.github.com/cscartjp/9f30ea09fba187ee21e7)

***

## 言語ファイル

次に言語用のファイルを用意します。\
以下のディレクトリに次の内容のファイルを設置してください。\
.poファイルについては [こちら](doc:languages-and-translations-by-po-file) をご確認ください。

> /var/langs/en/addons/hello\_world.po

```c
msgid ""
msgstr "Project-Id-Version: tygh"
"Content-Type: text/plain; charset=UTF-8\n"
"Language-Team: English\n"
"Language: en_US\n"

msgctxt "Addons::name::hello_world"
msgid "Hello World"
msgstr "Hello World"

msgctxt "Addons::description::hello_world"
msgid "Say hello to the world."
msgstr "Say hello to the world."
```

```c
msgid ""
msgstr "Project-Id-Version: tygh"
"Content-Type: text/plain; charset=UTF-8\n"
"Language-Team: Japanese\n"
"Language: ja_JP\n"

msgctxt "Addons::name::hello_world"
msgid "Hello World"
msgstr "ハローワールド"

msgctxt "Addons::description::hello_world"
msgid "Say hello to the world."
msgstr "ハローワールド！"
```

[ダウンロード](https://gist.github.com/cscartjp/38ac5da65439e62f20bc)

アドオンをスタートするにはこれで十分です。

管理パネルにログインし【**管理**】→【**アドオン**】→【**利用可能なすべてのアドオンを表示・タブ**】に切り替えます。

＜画像＞

作成した\_Hello World\_アドオンがリストにあるはずです。\
インストール/アンインストールが可能となり、インストール後はアドオンを有効/無効を切り替えることが出来ます。

{% hint style="info" %}
HINT

作成したアドオンがリスト（アドオン管理）に表示されていない場合は、CS-Cartのキャッシュをクリアする必要があるかもしれません。キャッシュをクリアする方法は、ブラウザのアドレスフィールドのURLの最後に`&cc&ctpl`を追加してページにアクセスします。
{% endhint %}

`scheme="3.0"`というに注目してください。（CS-Cart v.4.2.4以降）

このパラメータが無いアドオンは非推奨のマークアップを使用しているとみなされます。\
このパラメーターが無いアドオンはいくつか存在していますが、下位互換が必要な場合のみで利用して下さい。

では、このアドオンに設定をいくつか追加してみましょう。

_addon.xml_ ファイルに`settings` セクションを作成します。\
各設定は、の\_item\_セクションで区切られています。

それぞれの\_item\_は\_type\_や\_default\_value\_などの要素で指定します。

\_addon.xml\_の`<priority>100500</priority>`の後に下記のような`settings`を書いてみましょう:

```xml
<settings edition_type="ROOT,ULT:VENDOR">
    <sections>
        <section id="general">
            <items>
                <item id="some_prop">
                    <type>input</type>
                    <default_value>Hello World!</default_value>
                </item>
                <item id="some_dropdown">
                    <type>selectbox</type>
                    <default_value>blue</default_value>
                    <variants>
                        <item id="red"></item>
                        <item id="green"></item>
                        <item id="blue"></item>
                    </variants>
                </item>
            </items>
        </section>
    </sections>
</settings>
```

[ダウンロード](https://gist.github.com/cscartjp/ac2087e545ed3bdba18c)

言語ファイル（.po）にも追記します。

```c
msgctxt "SettingsOptions::hello_world::some_prop"
msgid "Some Prop"
msgstr "Some Prop"

msgctxt "SettingsOptions::hello_world::some_dropdown"
msgid "Some Dropdown"
msgstr "Some Dropdown"

msgctxt "SettingsVariants::hello_world::some_dropdown::red"
msgid "Red"
msgstr "Red"

msgctxt "SettingsVariants::hello_world::some_dropdown::green"
msgid "Green"
msgstr "Green"

msgctxt "SettingsVariants::hello_world::some_dropdown::blue"
msgid "Blue"
msgstr "Blue"
```

[ダウンロード](https://gist.github.com/cscartjp/255969b24ccd16e849f7)

CS-Cart管理パネルのアドオン管理ページに行き、**Hello World**アドオンを再インストールします。（アンインストール＞インストール）

リストからギアのアイコンをクリックして**設定**を表示します。

それをクリックすると、追加した設定があるアドオンの設定ダイアログが表示されます:

＜画像＞

複雑なことではないですよね？\
しかし、このままでは特に何も機能するものでもありません。

[次のチュートリアル](doc:advanced-add-on-tutorial) では、より複雑で有益なアドオンを作成します。
