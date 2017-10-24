# notice
微信 SDK 模板消息

模板消息仅用于公众号向用户发送重要的服务通知，只能用于符合其要求的服务场景中，如信用卡刷卡通知，商品购买成功通知等。不支持广告等营销类消息以及其它所有可能对用户造成骚扰的消息。

关于使用规则，请注意：

1. 所有服务号都可以在功能->添加功能插件处看到申请模板消息功能的入口，但只有认证后的服务号才可以申请模板消息的使用权限并获得该权限；
2. 需要选择公众账号服务所处的2个行业，每月可更改1次所选行业；
3. 在所选择行业的模板库中选用已有的模板进行调用；
4. 每个账号可以同时使用多个模板。
5. 当前每个模板的日调用上限为 10 万次【2014年11月18日将接口调用频率从默认的日1万次提升为日10万次，可在MP登录后的开发者中心查看】。

关于接口文档，请注意：

1. 模板消息调用时主要需要模板 ID 和模板中各参数的赋值内容；
2. 模板中参数内容必须以 ".DATA" 结尾，否则视为保留字；
3. 模板保留符号 "\{\{ \}\}"。
4. 模板里文字换行使用\n即可。


### 获取实例

```php
<?php
use Yii;

$notice = Yii::$app->wechat->notice;
```

### API

* `boolean setIndustry($industryId1, $industryId2)` 修改账号所属行业；
* `array getIndustry()` 返回所有支持的行业列表，用于做下拉选择行业可视化更新；
* `string addTemplate($shortId)` 添加模板并获取模板ID；
* `collection send($message)` 发送模板消息, 返回消息ID；
* `array getPrivateTemplates()` 获取所有模板列表；
* `array deletePrivateTemplate($templateId)` 删除指定ID的模板。

非链接调用方法：

```php
$messageId = $notice->send([
        'touser' => 'user-openid',
        'template_id' => 'template-id',
        'url' => 'xxxxx',
        'data' => [
            //...
        ],
    ]);
```

链式调用方法:

```text
设置模板ID：template / templateId / uses
设置接收者openId: to / receiver
设置详情链接：url / link / linkTo
设置模板数据：data / with

以上方法都支持 `withXXX` 与 `andXXX` 形式链式调用
```

```php
$messageId = $notice->to($userOpenId)->uses($templateId)->andUrl($url)->data($data)->send();
// 或者
$messageId = $notice->to($userOpenId)->url($url)->template($templateId)->andData($data)->send();
// 或者
$messageId = $notice->withTo($userOpenId)->withUrl($url)->withTemplate($templateId)->withData($data)->send();
// 或者
$messageId = $notice->to($userOpenId)->url($url)->withTemplateId($templateId)->send();
// ... ...
```

### 示例:

#### 模板

```text
{{ first.DATA }}
商品明细：
名称：{{ name.DATA }}
价格：{{ price.DATA }}
{{ remark.DATA }}
```

发送模板消息：

```php
$userId = 'OPENID';
$templateId = 'ngqIpbwh8bUfcSsECmogfXcV14J0tQlEpBO27izEYtY';
$url = 'http://overtrue.me';
$data = array(
         "first"  => "恭喜你购买成功！",
         "name"   => "巧克力",
         "price"  => "39.8元",
         "remark" => "欢迎再次购买！",
        );
$result = $notice->uses($templateId)->withUrl($url)->andData($data)->andReceiver($userId)->send();
var_dump($result);
// {
//      "errcode":0,
//      "errmsg":"ok",
//      "msgid":200228332
//  }
```