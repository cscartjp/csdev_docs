---
icon: webhook
---

# APIの拡張

アドオンを使用して新しいエンティティーを持つAPIを拡張する方法；

1. アドオンディレクトリに`Tygh/Api/Entities`サブディレクトリを作成
2. `Tygh/Api/Entities` ディレクトリに新しいエンティティーを含んだファイルを作成(例､Things.php)
3. このファイルでは、ネームスペース`Tygh\Api\Entities` の`Api\Entity` クラスから受け継がれた同じ名前のクラスを定義可能です。
4. このクラスは4つの必須メソッド全てを実装する必要があります(CRUD)。

## 基本的なAPIテンプレート

```php
<?php
namespace Tygh\Api\Entities;

use Tygh\Api\AEntity;
use Tygh\Api\Response;

class Things extends AEntity
{
    public function index($id = '', $params = array())
    {
        return array(
            'status' => Response::STATUS_OK,
            'data' => array()
        );
    }

    public function create($params)
    {
        return array(
            'status' => Response::STATUS_CREATED,
            'data' => array()
        );
    }

    public function update($id, $params)
    {
        return array(
            'status' => Response::STATUS_OK,
            'data' => array()
        );
    }

    public function delete($id)
    {
        return array(
            'status' => Response::STATUS_NO_CONTENT,
        );
    }
}
```
