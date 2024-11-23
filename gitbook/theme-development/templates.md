---
icon: browser
---

# テンプレート

## Smarty3

CS-Cartは`Smarty 3`テンプレートエンジンを使用しています。\
Smartyは、表示部分とアプリケーションコードを分けることが可能です。

Smartyについてさらに詳しい情報は[こちら](http://www.smarty.net/docs/ja/)で確認することが出来ます。

このチャプターでは開発者にとって、非常に興味深いものになるでしょう。\
テンプレートが使用することができる構造を詳しく説明します。

## テンプレート構造

テンプレートは\_design/themes/テーマ名/templates\_ディレクトリにあります。\
テンプレートは以下の構造になっています：

* `addons` ディレクトリ - アドオン用テンプレートを格納しています。
* `blocks` ディレクトリ - ブロック設定のレイアウトで使用されるテンプレートを格納。これらはブロックを定義することができるテンプレート とラッパー です。
* `buttons` ディレクトリ - ボタン用のテンプレートを格納。ボタンの役割によって用意されています。
* `common` ディレクトリ - 複数の箇所で使用する共通の機能を持ったテンプレートを格納。例えば、パンくず、カレンダー、ページネーション用のテンプレートです。
* `pickers` ディレクトリ - 商品ページやとカテゴリーページで使用するダイアログのテンプレートを格納。
* `views` ディレクトリ -各コントローラー用のテンプレートを格納
* `404.tpl` ファイル - 404ページ用のテンプレートファイル。
* `demo_theme_selector.tpl` ファイル - `ビジュアルエディタ`用のテンプレートファイル。
* `index.tpl` ファイル - メインのテンプレートファイル。このファイルは以下の内容が含まれています。
* _DOCTYPE_ - 必須要素。 `<!DOCTYPE html>`を使用することをお勧めします。
* Title - ページタイトル
* \_meta.tpl\_ファイルの読み込み
* \_common/styles.tpl\_ファイルの読み込み
* _common/scripts.tpl_ ファイルの読み込み
* ビジュアルエディタ、デモパネル、翻訳、テンプレートモードの各ファイルの読み込み
* `meta.tpl` ファイル - サイトのメタ情報が書かれたファイル：description, keywords, viewport等

\`\*

以下はCS-Cart Version 4.2.4の`responsive`テーマの各ファイルです。

```html
{*  To modify and rearrange content blocks in your storefront pages
    or change the page structure, use the layout editor under Design->Layouts
    in your admin panel.

    There, you can:

    * modify the page layout
    * make it fluid or static
    * set the number of columns
    * add, remove, and move blocks
    * change block templates and types and more.

    You only need to edit a .tpl file to create a new template
    or modify an existing one; often, this is not the case.

    Basic layouting concepts:

    This theme uses the Twitter Bootstrap 2.3 CSS framework.

    A layout consists of four containers (CSS class .container):
    TOP PANEL, HEADER, CONTENT, and FOOTER.

    Containers are partitioned with fixed-width grids (CSS classes .span1, .span2, etc.).

    Content blocks live inside grids. You can drag'n'drop blocks
    from grid to grid in the layout editor.

    A block represents a certain content type (e.g. products)
    and uses a certain template to display it (e.g. list with thumbnails).
*}
<!DOCTYPE html>
<html lang="{$smarty.const.CART_LANGUAGE}">
<head>
{capture name="title"}
{hook name="index:title"}
{if $page_title}
    {$page_title}
{else}
    {foreach from=$breadcrumbs item=i name="bkt"}
        {if !$smarty.foreach.bkt.first}{$i.title|strip_tags}{if !$smarty.foreach.bkt.last} :: {/if}{/if}
    {/foreach}
    {if !$skip_page_title && $location_data.title}{if $breadcrumbs|count > 1} - {/if}{$location_data.title}{/if}
{/if}
{/hook}
{/capture}
<title>{$smarty.capture.title|strip|trim nofilter}</title>
{include file="meta.tpl"}
<link href="{$logos.favicon.image.image_path}" rel="shortcut icon" />
{include file="common/styles.tpl" include_dropdown=true}
{include file="common/scripts.tpl"}
</head>

<body>
{if $runtime.customization_mode.design}
    {include file="common/toolbar.tpl" title=__("on_site_template_editing") href="customization.disable_mode?type=design"}
{/if}
{if $runtime.customization_mode.live_editor}
    {include file="common/toolbar.tpl" title=__("on_site_live_editing") href="customization.disable_mode?type=live_editor"}
{/if}
{if "THEMES_PANEL"|defined}
    {include file="demo_theme_selector.tpl"}
{/if}

<div class="ty-tygh {if $runtime.customization_mode.theme_editor}te-mode{/if} {if $runtime.customization_mode.live_editor || $runtime.customization_mode.design}ty-top-panel-padding{/if}" id="tygh_container">

{include file="common/loading_box.tpl"}
{include file="common/notification.tpl"}

<div class="ty-helper-container" id="tygh_main_container">
    {hook name="index:content"}
        {render_location}
    {/hook}
<!--tygh_main_container--></div>


{if $runtime.customization_mode.design}
    {include file="common/template_editor.tpl"}
{/if}
{if $runtime.customization_mode.theme_editor}
    {include file="common/theme_editor.tpl"}
{/if}
{hook name="index:footer"}{/hook}
<!--tygh_container--></div>
</body>

</html>
```

```html
{hook name="index:meta"}
{if $display_base_href}
<base href="{$config.current_location}/" />
{/if}
<meta http-equiv="Content-Type" content="text/html; charset={$smarty.const.CHARSET}" data-ca-mode="{$store_trigger}" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" />
{hook name="index:meta_description"}
<meta name="description" content="{$meta_description|html_entity_decode:$smarty.const.ENT_COMPAT:"UTF-8"|default:$location_data.meta_description}" />
{/hook}
<meta name="keywords" content="{$meta_keywords|default:$location_data.meta_keywords}" />
{/hook}
{$location_data.custom_html nofilter}
```

```php
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>{__("page_not_found")}</title>
    {literal}<style type="text/css">
    .ty-exception {
        font: normal 13px Arial, Tahoma, Helvetica, sans-serif;
        position: absolute;
        top: 10%;
        left: 50%;
        margin: 0 0 0 -480px;
        width: 960px;
    }
    .ty-exception p {
        margin: 0;
        padding: 0px 0px 30px 0px;
        color: #808080;
        font-size: 110%;
    }
    .ty-exception-code {
        float: left;
        display: inline-block;
        margin-right: 30px;
        padding: 50px 30px 40px;
        -webkit-border-radius: 9px;
        -moz-border-radius: 9px;
        border-radius: 9px;
        background-color: #f9f9f9;
        background-image:-moz-radial-gradient(50% 50%,circle closest-side,rgb(255,255,255) 0%,rgb(246,246,246) 100%); 
        background-image:-webkit-gradient(radial,50% 50%,0,50% 50%,94,color-stop(0, rgb(255,255,255)),color-stop(1, rgb(246,246,246)));
        background-image:-webkit-radial-gradient(50% 50%,circle closest-side, rgb(255,255,255) 0%,rgb(246,246,246) 100%);
        background-image:-ms-radial-gradient(50% 50%,circle closest-side, rgb(255,255,255) 0%,rgb(246,246,246) 100%);
        background-image:radial-gradient(50% 50%,circle closest-side, rgb(255,255,255) 0%,rgb(246,246,246) 100%);
        -webkit-box-shadow:inset 0px 1px 10px 0px rgba(0,0,0,0.05);
        -moz-box-shadow:inset 0px 1px 10px 0px rgba(0,0,0,0.05);
        box-shadow:inset 0px 1px 10px 0px rgba(0,0,0,0.05);
        color: #bfbfbf;
        font: normal bold 86px Arial, sans-serif;
        line-height: 70px;
        -ms-filter:"progid:DXImageTransform.Microsoft.Alpha(Style=2)";
        filter:progid:DXImageTransform.Microsoft.Alpha(Style=2);
    }
    .ty-exception-code em {
        display: block;
        text-align: center;
        font: normal normal 26px Arial, sans-serif;
    }
    .ty-exception h1 {
        margin: 0;
        padding: 0px 0px 25px 0px;
        font: normal bold 25px Arial, sans-serif;
    }
  </style>{/literal}
</head>
<body>
    <div class="ty-exception">
        <span class="ty-exception-code"> {$exception_status} <em>{__("exception_error")}</em> </span>
    <h1>{__("exception_title")}</h1>
    <p>
        {if $smarty.const.HTTPS === true}
            {assign var="return_url" value=$config.https_location|fn_url}
        {else}
            {assign var="return_url" value=$config.http_location|fn_url}
        {/if}
        
        {if $exception_status == "403"}
            {__("access_denied_text")}
        {elseif $exception_status == "404"}
            {__("page_not_found_text")}
        {/if}
    </p>
    <p>{__("exception_error_code")}
        {if $exception_status == "403"}
            {__("access_denied")}
        {elseif $exception_status == "404"}
            {__("page_not_found")}
        {/if}
    </p>
    </div>
</body>
</html>
```

## アドオン用のテンプレート

アドオン用のテンプレートは`templates/addons`ディレクトリにあります。

## アドオンのテンプレートの構造

* `blocks`ディレクトリ -アドオンブロックンテンプレート。レイアウトエディターでブロックとして選択可能となります。 `blocks/product_tabs`以下のファイルは商品タブで選択可能になります。
* `hooks` ディレクトリ - テンプレートフック用のテンプレート
* `views` テンプレート - 独自に追加されたテンプレート

アドオンディレクトリではこれらの他にもアドオンを正常に作動させるために必要な他のディレクトリやファイルを自由に配置できます。`ソーシャルボタン`アドオンの`providers` ディレクトリなどを参照して下さい。

## Blocks テンプレート

ブロック用のテンプレートは\_templates/block\_ ディレクトリにあります。\
ブロックの役割によって異なったテンプレートが適応されます。

テンプレートアプリケーションは\_app/schemas/block\_manager/blocks.php\_ ファイルに定義されています。

例えば、カテゴリーブロックでは

```php
'templates' => 'blocks/categories'
```

`blocks/categories`ディレクトリから全てのテンプレートに適応が可能ということ意味します。

ファイル名は言語変数によって以下のように定義されます：

* ファイル内のコードに次のパートをもつ場合：`{block-description:name_of_template}`、`name_of_template`が使用されます。
* ファイルにこのような記述がない場合は、テンプレートファイル名に基づいて言語変数が設定されます。

## Views テンプレート

CS-Cartでは、Model-View-Controller (MVC)のアーキテクチャーパターンが使用されています。

`views` ディレクトリはサイトのテンプレート群を含みます。`views`ディレクトリ以下のディレクトリ名称はコントローラー名などによって定義されます。

ディレクトリにあるファイルはコントローラーで定義されたモードで使用されます。

また、コントローラが定義するディレクトリ以下には`components`ディレクトリがあります。そこに保存されているテンプレートは、追加機能を実行します。

以下のページで使用されるテンプレートは、どのように定義するのか見てみましょう：

> index.php?dispatch=categories.view\&category\_id=166

* `categories` - データを受信するために参照されるコントローラー。カテゴリーのデータが必要なので、viewsディレクトリに\_categories\_ディレクトリが必要となります。
* `view` - categoriesコントローラーに書かれたモード名。モード名と同じファイルを作成します。（view.tpl）
* `category_id` - 表示したいカテゴリーのIDとなります。（コントローラーに渡す引数）
