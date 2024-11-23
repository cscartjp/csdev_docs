---
icon: gratipay
---

# アドオンの互換性

アドオンのインストール中に、アドオンがCS-Cart / CS-Cartマーケットプレイスのバージョン、PHPバージョン、およびサーバーの拡張機能と互換性があるかどうかを確認するチェックが実行されます。この情報は **addon.xml**の<`compatibility`>セクションに記述されます。

## CS-Cartのエディションとの互換性

### CS-Cartとのバージョンの互換性

以下は、アドオンと特定のバージョンのCS-Cartとの互換性を指定する方法です。

```xml
<compatibility>
    <core_version>
        <min>4.3.2</min>
        <max>4.3.6</max>
    </core_version>
</compatibility>
```

{% hint style="info" %}
HINT

`<min>`と`<max>`の両方を指定する必要はありません。これらは、それぞれサポートする最も古いバージョンと最新バージョンを表しています。
{% endhint %}

この例の設定のアドオンをバージョン4.3.7にインストールしようとすると、次のエラーが表示されます。

{% hint style="warning" %}
エラー

CS-Cartのバージョン（4.3.7）はサポートされていません。最小は4.3.2。最大は4.3.6です。
{% endhint %}

### エディション（通常版／マーケットプレイス版）との互換性

CS-Cartには、通常版（`ULTIMATE`）とECモール・マーケットプレイス版（`MULTIVENDOR`）の2つの製品があります。アドオンを、どちらか、または両方と互換性があるものとして設定できます。

* 1つのエディションのみとの互換性を設定：

```xml
<compatibility>
    <core_edition>MULTIVENDOR</core_edition>
</compatibility>
```

* 複数のエディションとの互換性を設定：

```xml
<compatibility>
    <core_edition>MULTIVENDOR,ULTIMATE</core_edition>
</compatibility>
```

**addon.xml**でエディションの互換性を`MULTIVENDOR`と指定してCS-Cart通常版にこのアドオンをインストールしようとすると、次のエラーが表示されます。

{% hint style="warning" %}
エラー

CS-Cartのエディション（ULTIMATE）はサポートされていません。ECモール・マーケットプレイス版のみインストール可能です。
{% endhint %}

## サーバー環境との互換性

### PHPバージョン

アドオンがサポートするサーバーのPHPバージョンの最小値と最大値を指定できます。

```xml
<compatibility>
    <php_version>
        <min>5.6.6</min>
        <max>7.1.1</max>
    </php_version>
</compatibility>
```

このアドオンをバージョン5.5.9のサーバーにインストールしようとすると、次のエラーが表示されます。

{% hint style="warning" %}
エラー

PHPバージョン（5.5.9-1ubuntu4.14）はサポートされていません。最小：5.6.6。最大：7.1.1です。
{% endhint %}

### PHPのエクステンションとの互換性

アドオンに必要なPHPのエクステンションを指定できます。

```xml
<compatibility>
    <php_extensions>
        <gd>
            <supported>Y</supported>
        </gd>
    </php_extensions>
</compatibility>
```

必要なPHPエクステンション（この場合は`gd`）がインストールされていない場合、次のエラーが表示されます。

{% hint style="warning" %}
エラー

このアドオンの利用には、サーバーにgdエクステンションをインストールする必要があります。
{% endhint %}

```xml
<php_extensions>
    <json>
        <min>3.0</min>
        <max>4.0</max>
    </json>
</php_extensions>
```

必要なPHPエクステンションとそのバージョン

{% hint style="info" %}
HINT

この場合、`<supported>Y</supported>`は追加する必要はありません。
{% endhint %}

PHPエクステンションとそのバージョンが設定と異なる場合、エラーが表示されます。

```xml
<compatibility>
    <php_extensions>
        <json>
            <min>3.0</min>
            <max>4.0</max>
        </json>
    </php_extensions>
</compatibility>
```

{% hint style="warning" %}
エラー

PHPエクステンション「json」のバージョン（1.3.2）はこのアドオンでサポートされていません。最小：3.0。最大：4.0。
{% endhint %}

### このアドオンと競合するPHPエクステンションをチェックする場合

```xml
<compatibility>
    <php_extensions>
        <gd>
            <supported>N</supported>
        </gd>
    </php_extensions>
</compatibility>
```

競合するPHPエクステンション（この場合はgd）がインストールされている場合、エラーが表示されます。

{% hint style="warning" %}
エラー

このアドオンの利用には、サーバーからgd拡張を削除する必要があります。
{% endhint %}
