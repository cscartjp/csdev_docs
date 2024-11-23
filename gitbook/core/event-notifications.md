---
icon: volume-high
---

# イベントの通知

Event Notifications - 新しい通知方法

＜画像＞

{% hint style="info" %}
MEMO

本機能は、バージョン4.11.1以降のCS-Cartに実装されています。
{% endhint %}

CS-Cartは注文の作成、注文のステータスの変更、商品のレビューや注文での出荷の作成などの各イベントに関する複数の通知の仕組みを提供します。

これは以下の主要コンポーネントで構成されています。

* `[Events](#events)`（イベント）
* `[Event dispatcher](#event-dispatcher)`（イベントディスパッチャ）
* `[Messages](#messages)`（メッセージ）
* `[Transports](#transports)`（トランスポート）
* `[Receivers](#receivers)`（レシーバー）
* `[Notification settings](#notification-settings)` （通知設定 ）

{% hint style="warning" %}
SAMPLE

Event Notificationsを実装したアドオンの例 https://github.com/cscart/addon-notification-events-example
{% endhint %}

## Events

Events（イベント）は以下の要素で構成されています。

* イベント識別子
* そのイベントに関連する一連のデータ

`例`：\
・イベント「注文の詳細の変更」の識別子は`order.updated`。\
・`fn_get_order_info`関数から受け取った注文に関するデータのセット`$data['order_info']`。

#### 独自のイベントを追加する方法

スキーマ `notifications/events.php` には、すべてのEvents、Receivers、およびTransportsを記述します。\
スキーマは`Tygh::$app['event.events_schema']`サービスを介して利用できます。

スキーマの要素には、次の構造があります。

```php
(string) EventId => [
    'group' => (string) GroupId,
    'name' => [
        'template' => (string) TemplateLanguageVariable,
        'params' => [
            (string) SubstitutionName => (string) Substitution
            ...
        ],
    ],
    'data_provider' => (callable) DataProvider,
    'receivers' => [
        (string) ReceiverId => [
            (string) TransportId => BaseMessageSchema::create([
                'area'            => (string) area,
                'from'            => (string) from,
                'to'              => (callable) DataValue::create(key),
                'template_code'   => (string) template_code,
                ...
                'language_code'   => (callable) DataValue::create(parent_key.key, default_value),
                'data_modifier'   => (callable) function (array $data) {
                    return array_merge($data, $added_data_value);
                }
            ]),
            ...
        ],
        ...
    ],
],
```

* `EventId` - イベントの識別子。`TyghNotificationsEventDispatcher::dispatch` の最初の引数として使用されます。
* `GroupId` - イベントグループの識別子。ナビゲーションをわかりやすくするために、通知設定ページで使用されます。
* `TemplateLanguageVariable` - 通知設定でイベントを表す言語変数の名前。
* `SubstitutionName`および`Substitution`—言語変数をイベントの詳細に適合させるパラメーターの名前とその値。
* `DataProviderNotification\DataProviders\DataProvider` - インターフェイスを実装します。`\Tygh\Notifications\EventDispatcher::dispatch`に渡された`data`に基づいてイベント固有のフィールドを取得するために使用されます。
* `ReceiverId` - Receivers(受信者)の識別子。既存の受信者識別子は、`Tygh::$app['event.receivers_schema']`から取得できます。\
  \_`TransportId` - Transports(トランスポート)の識別子。トランスポートは`\Tygh\Notifications\Transports\ITransport::getId`で提供する必要があります。\
  \_`BaseMessageSchema` - 送信用に準備されたデータを使用して、schemaクラスのインスタンスを作成します。入力パラメータには、通知の送信に必要な処理されたデータを含める必要があります。テキスト(`area`、`from`、`template_code`)または`DataValue`クラスのインスタンスにすることができます。\
  \*`DataValue` - 入力配列からキーでデータを取得できるクラス。キーが配列にない場合は、`default_value`が使用されます。デフォルトでは`null`。
* `data_modifier` - 関数内の`data`に渡されるデータの追加変更を行うことを可能にする呼び出し可能なパラメータ。

#### イベントを登録する方法

* 一意のイベント識別子（EventId）を選択します。
* 新しいイベントが属するグループを選択します。\
  イベントが新しいグループに属している場合は、同じ識別子`GroupId`とイベントグループの名前を値として使用して言語変数を作成します。
* イベントの名前を形成するためのテンプレートを格納する言語変数を作成します(`TemplateLanguageVariable`)。
* イベント名テンプレート(`SubstitutionName`、`Substitution`)の置換のリストを作成します。\
  イベントの名前に置換がない場合は、'params'配列を空のままにする必要があります。
* イベントに関する通知を受信する必要があるユーザータイプの識別子を指定します(`ReceiverId`)。
* `data`から来るイベント固有の`DataProvider`を処理するために、実装された`DataProvider`の名前を指定します。
* 受信者タイプごとに、次のように指定します。
  * 通知の受信方法(TransportId)。
  * 現在の受信者に固有のデータで、`BaseMessageSchema`クラスのインスタンスに処理されます(必要に応じて`DataValue`を使用)。

## Event Dispatcher

ディスパッチャーは、イベントをトリガーするシステムコンポーネントです。イベントをトリガーすると、受信者にメッセージを送信します。

イベントディスパッチャーは、`Tygh::$app['event.dispatcher']`サービスに登録されています。

#### イベントをトリガーする方法

通知を送信する必要がある場所でイベントディスパッチャを呼び出します。

```php
Tygh::$app['event.dispatcher']->dispatch(
        'EventId', 
        [
                'order_info' => $order_info, 
                'user_info' => $user_info, 'settings' => $settings
        ]
);
```

## Messages

メッセージは、イベントディスパッチャに渡されたデータから、スキーマに基づいて形成されます。メッセージには、このメッセージタイプに接続されたトランスポートを介してそのメッセージを送信するために必要なすべてのデータが含まれています。

送信用に準備されたデータの配列は、`events.php`イベントスキーマのルールに基づいて作成されます。

実装例：\
注文の状態に関する通知のためにデータを準備します。この通知は管理者のメールに送信されます。

```php
'receivers' => [
    UserTypes::ADMIN => [
        MailTransport::getId() => MailMessageSchema::create([
            'area'            => 'A',
            'from'            => 'company_users_department',
            'to'              => 'company_users_department',
            'reply_to'        => DataValue::create('user_data.email'),
            'template_code'   => 'activate_profile',
            'legacy_template' => 'profiles/activate_profile.tpl',
            'company_id'      => DataValue::create('user_data.company_id'),
            'language_code'   => Registry::get('settings.Appearance.backend_default_language'),
            'data_modifier'   => function (array $data) {
                return array_merge($data, [
                    'url' => fn_url('profiles.update?user_id=' . $data['user_data']['user_id'], 'A'),
                ]);
            }
        ]),
    ],
],
```

`DataValue`メッセージを形成するためにディスパッチャーに渡された配列からキーでデータを取得できます。\
`data_modifier`で指定された関数を使用すると、データのより複雑な変更を行うことができます。

メッセージスキーマはメッセージを作成する責任があります。イベントに関する処理されたデータを取得し、データの有効性をチェックします。

スキーマは、特定のトランスポート用に実装されています。

* `\Tygh\Notifications\Transports\Mail\MailMessageSchema` - 電子メールに送信される通知のスキーマ。
* `\Tygh\Notifications\Transports\Internal\InternalMessageSchema` - 通知センターに送信される通知のスキーマ。

#### 独自のメッセージを追加する方法

メッセージを追加するには：

* メッセージデータのプロバイダを追加します。プロバイダーは、`\Tygh\Notifications\DataProviders\IDataProvider`インターフェイスを実装するか、既存の基本データプロバイダークラスを拡張する必要があります。
* 特定のトランスポートのイベントスキーマにそのプロバイダーを指定します。
* イベントスキーマで、ディスパッチャーに渡される入力データを処理するためのルールを指定します。

#### メールで送信されるメッセージを追加する方法

これらのメッセージには、`Tygh::$app['mailer']`サービス経由でメールを送信するために必要なデータが含まれています。

新しいメールメッセージを作成するには:

* イベントスキーマで、`\Tygh\Notifications\Transports\Mail\MailMessageSchema`に渡されたデータを準備するためのルールを作成します。
* スキーマの`create`メソッドに渡される配列には、次のプロパティが含まれています。
* `to` - メッセージの受信者。
* `from` - メッセージの送信者。
* `reply_to` - メッセージの返信先。
* `template_code` - メールテンプレートのコード。
* `legacy_template` - メールテンプレートの名前（ストアで古いメールテンプレートが使用されている場合）。
* `language_code` - Eメールが送信される言語のコード。
* `company_id` - Eメールが送信されるcompany ID（識別子）。
* `area` - Eメールの送信元：管理パネルまたはストアフロントから。
* `$data` 配列に渡される他のキーは、Eメールテンプレートの置換用のデータです。

#### 通知センターに送信されるメッセージを追加する方法

これらのメッセージには、`Tygh::$app['notifications_center']`サービスを介して通知センターで通知を作成するために必要なデータが含まれています。

新しいメールメッセージを作成するには:

1. イベントスキーマで、`\Tygh\Notifications\Transports\Internal\InternalMessageSchema`で渡されたデータを準備するためのルールを作成します。
2. スキーマの`create`メソッドに渡される配列には、次のプロパティが含まれています。

* title-通知のタイトル。
  * `template` - 言語変数の名前。
  * `params` - 通知タイトルのテンプレート内の置換のリスト。タイトルに置換がない場合は、配列を空のままにする必要があります。
* `$message` - 通知のテキスト
  * `template` - 言語変数の名前。
  * `params` - 通知テキストのテンプレート内の置換のリスト。テキストに置換がない場合は、配列を空のままにする必要があります。
* `severity` - 通知の重大度（`\Tygh\Enum\NotificationSeverity`を参照）。
* `section` - メッセージが表示される通知センターのタブ。
* `tag` - 通知にマークが付けられるタグ。
* `area` - 通知が表示される領域：管理パネルまたはストアフロント。
* `action_url` - 通知をクリックした後にユーザーが誘導されるリンク。
* `timestamp` - 通知が作成された時刻。
* `recipient_search_method` - 通知を作成する必要のあるユーザーを検索する方法（`\Tygh\Enum\RecipientSearchMethods`を参照）。\
  次の検索方法を使用できます。
  * `\Tygh\Enum\RecipientSearchMethods::USER_ID` - ユーザーIDによる検索。
  * `\Tygh\Enum\RecipientSearchMethods::UGERGROUP_ID` - ユーザーグループIDによる検索（通知はグループ内のすべてのユーザーに対して作成されます）。
  * `\Tygh\Enum\RecipientSearchMethods::EMAIL` - ユーザーの電子メールによる検索。
* `recipient_search_criteria` - ユーザー検索条件:
  * `recipient_search_method = \Tygh\Enum\RecipientSearchMethods::USER_ID` - ユーザーID
  * `recipient_search_method = \Tygh\Enum\RecipientSearchMethods::UGERGROUP_ID` - ユーザーグループIDの場合
  * `recipient_search_method = \Tygh\Enum\RecipientSearchMethods::EMAIL` - ユーザーの電子メールの場合

## Transports

トランスポートは、特定のタイプのメッセージの実際の送信を処理します。

実装例：

* `\Tygh\Notifications\Transports\MailMailTransport` - 電子メールにメッセージを送信し、\
  `\Tygh\Notifications\Transports\Mail\MailMessageSchema`に準拠したメッセージを動作します。
* `\Tygh\Notifications\Transports\InternalTransport` - 通知センターにメッセージを送信し、`\Tygh\Notifications\Transports\Internal\InternalMessageSchema` に準拠するメッセージを処理します。

#### 独自のトランスポートを追加する方法

システムで使用されるトランスポートの識別子のリストは、`Tygh::$app['event.transports_schema']`サービスから入手できます。

独自のトランスポートを追加するには：

1. トランスポートがシステムに登録される識別子(`TransportId`)を選択します。
2. `\Tygh\Notifications\Transports\ITransport`インターフェイスを実装するクラスを作成します。
3. そのクラスの`getId()`メソッドで選択した識別子を指定します。
4. そのトランスポートの新しいプロバイダーを`Tygh::$app['event.transports.{TransportId}']`に登録します。
5. event.transport.TransportIdを識別子として、トランスポートの名前を値とした言語変数を作成します。

## Receivers

すべてのイベントには、イベントについて通知される可能性のある受信者のグループがあります。たとえば、注文状況の変更は、お客様、店舗管理者、およびその商品の出品者に通知を送信できます。

#### 独自のレシーバーを追加する方法

受信者の識別子のリストは、`Tygh::$app['event.receivers_schema']`サービスから入手できます。

新しいタイプのレシーバーを追加するには：

1. `get_notification_rules`フックポイントを使って、受信者の識別子を`$force_notification`配列に追加します。
2. `event.receiver.ReceiverId`を識別子として、受信者タイプの名前を値とした言語変数を作成します。
3. 受信者をイベントスキーマに追加し、これらの受信者に通知を配信するトランスポートを指定します。

## Notification settings

デフォルトではイベントが`Tygh::$app['event.events_schema']`に存在する場合、イベントはすべてのトランスポートを介してすべての受信者に通知する必要があると仮定されます。この動作は通知設定によって変更されます。それらは、どの受信者がイベントに関する通知を取得する必要があるか、およびどのトランスポートを介して取得する必要があるかを記述します。

{% hint style="info" %}
HINT

通知設定は、`一般設定→通知`で指定されています。 通知はイベントごと、トランスポートごとに、すべてのタイプの受信者に設定できます。
{% endhint %}

通知を設定するためのページには、関連するデータのみが表示されます。

以下のページは表示されません：

* 受信者のないイベント。
* レシーバーがどのイベントにも接続されていない場合。
* イベントを受信者に送信しないトランスポート。

`notification_settings`ルールへの変更は、データベースのテーブルに保存されます。\
それらは`Tygh::$app['event.notification_settings']`サービスから入手できます。

#### 通知ルールのオーバーロード

オーバーロード（設定の上書き）を使用すると、通知設定で必要な場合でも、特定の受信者にイベント通知を送信しないようにすることができます。

オーバーロードのセットは、`\Tygh\Notifications\Settings\Ruleset`クラスのオブジェクトであり、`Tygh::$app['event.notification_settings.factory']`ルールファクトリによって作成されます。イベントがトリガーされると、一連のオーバーロードがパラメータの1つとして渡されます。

例：注文編集ページには、「顧客に通知」、「注文部門に通知」、「ベンダーに通知」のチェックボックスがあります。以下のようなルールを記述すると、通知設定で必要な場合でも注文ステータスの変更に関する通知を送信しないようにすることができます。

```php
$notification_rules = Tygh::$app['event.notification_settings.factory']->create([
    UserTypes::CUSTOMER => false,
    UserTypes::ADMIN    => true,
    UserTypes::VENDOR   => true,
]);

Tygh::$app['event.dispatcher']->dispatch(
    'order.updated',
    $order_info,
    $notification_rules
);
```

#### 新しい通知システムの使用を開始する方法

1. アドオン内で、`mailer`サービス（`Tygh::$app['mailer']->send()`）または非推奨の`TyghMailer`クラス（`\Tygh\Mailer::sendMail()`）を介してメールが送信されるすべての場所を検索します。
2. このような状況に合わせてイベントを作成します。この章の「`独自のイベントを追加する方法`」を参照してください。
3. （オプション）通知センターの通知を介してユーザーに通知するための代替メカニズムを実装します。
4. 電子メールの送信を、 `event.dispatcher`サービスを介したイベントのトリガーに置き換えます：\
   `Tygh::$app['event.dispatcher']->dispatch()`
