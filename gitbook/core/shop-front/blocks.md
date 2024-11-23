---
icon: table-cells
---

# ブロック

ブロックとその動作は、`app/schemas/block_manager`ディレクトリにあるスキーマによって制御されます。

* `blocks.php`—ブロックのタイプを説明するメインスキーマ
* `blocks.functions.php` —メインスキーマで使用される関数
* `block_cache_properties.php` —キャッシュをサポートするすべてのブロックにグローバルに適用されるキャッシュパラメーター
* `dispatch_descriptions.php` —ディスパッチと言語に対応する変数のリスト。このファイルはDispatch、場所を追加/編集するときにフィールドのリストを作成する際にのみ必要です
* `templates.php` —ブロックで使用できるSmartyテンプレートで使用可能なパラメーターの説明
* `fillings.php`—ブロックの内容と詳細
* `dynamic_objects.php` —ダイナミックオブジェクト
