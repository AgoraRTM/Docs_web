# 🌻 Presence

Presence 提供监控临时用户状态及其变化的能力。相比于 Storage 监控的用户状态，区别如下：

|   功能   |                      Presence                      |                Storage                |
| :----: | :------------------------------------------------: | :-----------------------------------: |
|  状态类型  |                       临时用户状态                       |                  用户状态                 |
|  状态存储  | 临时保存。用户订阅频道且一直在线时，状态会持续存在；用户离开频道或断开 RTM 连接时，状态会消失。 | 永久保存。只要用户不主动变更状态，状态会永久保存在 RTM 动态数据库中。 |
|  状态内容  |                       任何自定义状态                      |                任何自定义状态                |
| 状态辅助信息 |                          无                         |          状态版本、状态编辑者 UID 等辅助信息         |

通过 Presence 功能，你可以实时获取以下信息：

* 用户加入或离开频道的实时状态
* 订阅或加入同一频道的所有用户的实时状态
* 一个用户订阅或加入的所有频道的实时信息
* 自定义临时用户状态及其变更信息

在 Message Channel 和 Stream Channel 中均可使用 Presence 功能。

想要了解 `Presence` 更多特性可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/presence" %}
[https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/presence](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/presence)
{% endcontent-ref %}

### Who Now

#### 接口描述

调用 `whoNow` 方法，你可以实时获取指定频道的在线用户数量、在线用户列表、在线用户的临时状态等信息。

#### 接口方法

你可以通过以下方式调用 `whoNow` 方法：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.whoNow({
    String channel,
    String channelGroup,
    unit channelType,
    Boolean includeUIDs,
    Boolean includeState,
    Uint page
}): Promise<WhoNowResponse> ;
```
{% endcode %}

|    Parameter   |   Type   | Required | Defaults | Description                                                                                                                                                                                                       |
| :------------: | :------: | :------: | :------: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|    `channel`   |  String  | Optional |     /    | 频道名称。如果未填写该参数，则 `whoNow` 方法会返回符合 `channelType` 条件的所有频道的在线用户信息。                                                                                                                                                    |
|  `channelType` |  Boolean | Optional |    `1`   | <p>频道类型：</p><ul><li><code>1</code>: Message Channel</li></ul><ul><li><code>2</code>: Stream Channel</li></ul><p>如果未填写该参数，则 <code>whoNow</code> 方法会返回 Message Channel 中符合 <code>channel</code> 条件的所有频道的在线用户信息。</p> |
|  `includeUIDs` |  Boolean | Optional |  `true`  | 是否获取在线用户的 UID。                                                                                                                                                                                                    |
| `includeState` |  Boolean | Optional |  `false` | <p>是否获取在线用户的临时用户状态。</p><p>注意：SDK 需要通过在线用户的 UID 查询对应用户的临时用户状态。如需设置该参数为 <code>true</code>，则需确保 <code>includeUIDs</code> 参数也为 <code>true</code>，否则方法会调用失败。</p>                                                       |
|     `page`     |  String  | Optional |     /    | 页码书签。默认获取第一页的在线用户列表，你可以通过返回值查看是否有下一页。如需获取指定页面的在线用户列表，你需要填写该参数。                                                                                                                                                    |
|   `callback`   | Function | Optional |     /    | 回调函数。                                                                                                                                                                                                             |

#### 基本用法

获取指定频道中在线用户的 UID 及临时用户状态。

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Presence.whoNow({
        channel: "ch1",
        includeUIDs: true,
        includeState: true
    });
} catch(status){
	console.log(status);
}
```
{% endcode %}

#### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
type whoNowStatus ={
    "error": boolean,                   // 本次调用是否出错
    "errorCode": number,                // 错误码
    "operation": "RTMWhoNowOperation",  // 本次操作类型
    "reason":"Error Reason"             // 错误原因
}

// Example of Response or result


type whoNowResponse = {
    totalOccupancy: number,         // 频道在线用户个数
    occupants: Array,               // 在线人员详情
    nextPage: String               // 后一页序号
}
```
{% endcode %}

### Where Now

#### 接口描述

在数据统计、app 调试等场景中，你可能需要了解指定用户订阅或加入的所有频道。调用 `whereNow` 方法，你可以实时获取指定用户所在频道的列表。

注意：每次调用 `whereNow` 方法只能获取一个指定用户所在频道的列表。如需获取多个指定用户所在频道的列表，你需要多次调用该方法。

#### 接口方法

你可以通过以下方式调用 `whereNow` 方法：

```js
rtm.Presence.whereNow({
    uid: rtm.uid},
    function(status, response) {
        console.log(status);
        console.log(response);
});
```

| Parameters |   Type   | Required |  Default  | Description |
| :--------: | :------: | :------: | :-------: | ----------- |
|    `uid`   |  String  | Optional | 当前用户的 UID | 用户的 UID     |
| `callback` | Function | Optional |     /     | 回调函数        |

#### 基本用法

获取指定用户所在频道的列表。

```js
try{
    const resulst = await rtm.Presence.whoNow(
    {
        channel: "ch1",
        channelType:1,
        includeUIDs: true,
        includeState: true
    });
} catch(status){
    console.log(status);
}

```

#### 返回值

```js
// Example of Status
type whereNowStatus ={
    "error": boolean
    "errorCode": number,
    "operation": "RTMWhereNowOperation",
    "reason":"Error Reason"
}

// Example of Response or result
type whereNowResponse = {
    totalChannel: number,           // 频道个数
    channels： Array                 // 频道详情
}
```

### Set State

#### 接口描述

为满足不同业务场景对用户状态的设置需求，RTM 提供 `setState` 方法自定义临时用户状态。用户可以为自己添加分数、游戏状态、位置、心情、连麦状态等自定义状态。

设置成功后，只要用户保持订阅指定频道并一直在线，设置好的自定义状态就会在频道中持续存在。`setState` 方法设置的是临时用户状态，当用户离开频道或断开与 RTM 的连接时，状态会消失。如果需要在重新加入频道或者重新连接时恢复用户状态，你需要实时在本地缓存该数据。如果你希望永久保存用户状态数据，声网推荐改用 Storage 模块的 `setUserMetadata` 方法。

如果用户修改了临时用户状态，SDK 会实时触发 `state-change` 类型的 Presence 事件。你可以通过订阅频道并配置对应参数来接收该事件。

#### 接口方法

你可以通过以下方式调用 `setState` 方法：

```js
try{
    const result = await rtm.Presence.setState(
    {
        state: {"mood":"pumped", "isTyping":false},
        channel: "chats.room1",
        channelType:1,
        channelGroup "cg_user123_friends"
    });
} catch (status) {
    console.log(status);
}
```

|   Parameters  |   Type   | Required | Default | Description                                                                                                   |
| :-----------: | :------: | :------: | :-----: | ------------------------------------------------------------------------------------------------------------- |
|   `channel`   |  String  |    Yes   |    /    | 频道名称                                                                                                          |
| `channelType` |   unit   |    Yes   |    /    | <p>频道类型：</p><ul><li><code>1</code>: Message Channel</li></ul><ul><li><code>2</code>: Stream Channel</li></ul> |
|    `state`    |  Object  | Optional |    /    | 键/值对格式的 JSON 对象，支持的数据类型为 int、float 和 string。如果指定的键已经存在，则该键的值会被新值覆盖。如果指定的键不存在，则 SDK 会新增该键/值对。                  |
|   `callback`  | Function | Optional |    /    | 回调函数                                                                                                          |

#### 基本用法

```js
var newState = { "mood":"pumped", "isTyping":false};

try{
    cosnt result = await rtm.Presence.setState(
    {
        state: newState,
        channel: 'ch1',
        channelType: 1
    });
} catch(status){
    console.log(status);
}
```

#### 返回值

```js
// Example of Status
{
    error:false
    errorCode: false,
    operation: "RTMSetStateOperation",
    reason: ""
}

// Example of Response
{
    timeToken:"14920301569575101"
}
```

### Get User State

#### 接口描述

如需获取指定频道中指定用户的临时用户状态，你可以调用 `getState` 方法。

#### 接口方法

你可以通过以下方式调用 `getState` 方法：

```js
try{
    const result = await rtm.Presence.getState(
    {
        uid: "anotherClientUUID",
        channel: "chats.room1",
        channelType: 1
    });
} catch(status) {
    console.log(status)
}
```

|   Parameters  |   Type   | Required |  Default  | Description                                                                                                   |
| :-----------: | :------: | :------: | :-------: | ------------------------------------------------------------------------------------------------------------- |
|     `uid`     |  String  | Optional | 当前用户的 UID | 用户的 UID                                                                                                       |
|   `channel`   |   Array  | Optional |     /     | 频道名称。                                                                                                         |
| `channelType` |   unit   |    Yes   |     /     | <p>频道类型：</p><ul><li><code>1</code>: Message Channel</li></ul><ul><li><code>2</code>: Stream Channel</li></ul> |
|   `callback`  | Function | Optional |     /     | 回调函数                                                                                                          |

#### 基本用法

```js
try{
    const result = await rtm.Presence.getState(
        {
            uuid: "uid",
            channel: 'ch1',
            channelType: 1
        });
} catch (status) {
    console.log(status);
}
```

#### 返回值

```js
// Example of Status
{
    error: false,
    errorCode: 0,
    operation: "RTMGetStateOperation",
    reason: ""
}

// Example of Response
{
    "state":{
            uid:"my_uid",
            state:{"mode":""happy,"move":"yes"},
            statusCount:2
            }
}
```

### Remove User State

#### 接口描述

当不再需要某个临时用户状态时，你可以调用 `removeState` 方法删除自己的一个或多个临时状态。

#### 接口方法

你可以通过以下方式调用 `removeState` 方法：

```js
rtm.Presence.removeState({String channel, String channelGroup, unit channelType, Array states}):Promise<SetStateResponse>;
```

|   Parameters  |   Type   | Required | Default | Description                                                                                                   |
| :-----------: | :------: | :------: | :-----: | ------------------------------------------------------------------------------------------------------------- |
|   `channel`   |  String  |    Yes   |    /    | 频道名称                                                                                                          |
| `channelType` |   unit   |    Yes   |    /    | <p>频道类型：</p><ul><li><code>1</code>: Message Channel</li></ul><ul><li><code>2</code>: Stream Channel</li></ul> |
|    `state`    |   Array  | Optional |    /    | 键/值对列表                                                                                                        |
|   `callback`  | Function | Optional |    /    | 回调函数                                                                                                          |

#### 基本用法

```js
try{
    const result = await rtm.Presence.removeState(
        {
            channel: 'ch1',
            channelType: 1,
            states: ["mode"]
        });
} catch (status) {
    console.log(status);
}
```

#### 返回值

```js
// Example of Status
{
    error:false,
    errorCode: 0,
    operation: "RTMRemoveStateOperation",
    reason: ""
}

// Example of Response
{
    timeToken:"14920301569575101"
}
```
