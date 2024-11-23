---
icon: hand-wave
cover: https://gitbookio.github.io/onboarding-template-images/header.png
coverY: 0
layout:
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# はじめに

本ドキュメントは、CS-Cartの構造を理解し、カスタマイズの方法であるアドオンの構造などについて解説しています。

CS-Cartはモジュラアーキテクチャ(組み合わせ型アーキテクチャ)の概念に従っています。 基本的な機能を司っているコア部分と、それを拡張する多数のアドオンがあります。

{% hint style="danger" %}
重要

コア関数やコントローラー、テンプレート、およびスキーマーは直接編集しないことを強くお勧めします。デフォルトの動作を拡張するにはアドオンを使用してください。
{% endhint %}

### MVCについて

#### [MVC: MODEL](https://ja.wikipedia.org/wiki/Model\_View\_Controller)

すべてのデータはデータベースに直接保存されています。 CS-Cartで使用されるデータベースエンジンはMySQLであるためプログラムのコンポーネントはプレースホルダを用いてMySQLからSQLクエリを通してデータを取得します。

CS-Cartの組み込み関数でデータベースに接続する例

```php
<?php
$data = array (
    'payment_id' => 5
);
 
$order_id = 3;
 
db_query('UPDATE ?:orders SET ?u WHERE order_id = ?i', $data, $order_id);// Some code
```

#### MVC: VIEW

CS-Cartはデータを表示する際、[Smarty](http://www.smarty.net/docs/ja/)テンプレートエンジンとJavascriptを使用しています。

Javascriptコードが埋め込まれたSmartyテンプレートの例:

{% code title="" %}
```smarty
{** block-description:my_twitter_addon **}
 
<script src="http://widgets.twimg.com/j/2/widget.js"></script>
<script>
new TWTR.Widget({ldelim}
    version: 2,
    type: 'profile',
    rpp: {$addons.my_twitter_addon.number_of_tweets},
    interval: 6000,
    width: 'auto',
    height: 300,
    theme: {ldelim}
        shell: {ldelim}
            background: '#FFFFFF',
            color: '#373737'
        {rdelim},
        tweets: {ldelim}
            background: '#D9EFF3',
            color: '#373737',
            links: '#005865'
        {rdelim}
    {rdelim},
    features: {ldelim}
        scrollbar: true,
        loop: true,
        live: true,
        hashtags: true,
        timestamp: true,
        avatars: true,
        behavior: 'default'
    {rdelim}
{rdelim}).render().setUser('{$addons.my_twitter_addon.username}').start();
</script>
```
{% endcode %}

#### MVC: CONTROLLER

コントローラーはPHPで書かれています。コアコントローラーは\`controllers\`ディレクトリにあります。\
アドオンは独自のコントローラーをそれぞれのディレクトリ内で定義することができます。