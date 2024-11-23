---
icon: compass-drafting
---

# テンプレートフック

テンプレートフックは`hook`タグで囲まれた以下のような部分です:

```smarty
{hook name="section:hook_name"}
...
{/hook}
```

開発者は、この部分にアドオンを使ってコードや処理を補足したり、再定義することができます。

## テープレート内のテンプレート・フック

以下の例は`design/backend/templates/views/order_management/components/totals.tpl`ファイルのテンプレートフックの例です。

```smarty
{hook name="order_management:product_info"}
       {if $cp.product_code}
              <p>{$lang.sku}:&nbsp;{$cp.product_code}</p>
       {/if}
{/hook}
```

## いつテンプレート・フックが使用されるか

テンプレート・フックは既存のテンプレートに追加のデータを表示するために使用されます。例えば、アドオンを使ってストアの管理パネルに表示したい特別なブロックを作成したい場合、このような独自のブロックはテンプレート・フックを使用して追加することが出来ます。

## テンプレート・フックの使用方法

PHPコード・フックと違って、テンプレートフックは別ファイルで宣言する必要はありません。（int.php等）

.tplファイルを適切なディレクトリと適切なファイル名で配置するだけです。

命名規則は次のようになります。

* `管理パネル`: _design/backend/templates/addons/\[addon id]/hooks/\[template name]/\[hook name].\[pre|post|override].tpl_
* `ショップフロント`: _design/themes/\[theme name]/templates/addons/\[addon id]/hooks/\[template name]/\[hook name].\[pre|post|override].tpl_

＜画像＞

## コア・テンプレートのオーバーライド

アドオンで標準のテンプレートを完全に上書きすることが可能です。

### オーバーライド方法：

* 管理パネルでは`design/backend/templates/addons/[addon id]`
* ショップフロントの場合は`design/themes/[theme name]/templates/addons/[addon id]`内に、`overrides`ディレクトリを作成します。
* `overrides`ディレクトリには、コアのテンプレート　ディレクトリと同じ構造を作成します。

例えば、\
`design/backend/templates/addons/[addon id]/overrides/views/index/index.tpl`ファイルは、次のテンプレートを完全にオーバーライドします：　　\
`design/backend/templates/views/index/index.tpl`
