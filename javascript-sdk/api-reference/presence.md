# 🌻 Presence

`Presence` 提供的服务发现能力，让开发者具备实时跟踪用户或设备的在线、离线及临时状态变更能力。`Presence` 为开发者提供以下信息：

* 用户加入或离开特定频道的实时信息
* 谁，以及多少用户订阅或加入某个特性频道的实时信息
* 一个用户订阅或加入了哪些频道的实时信息
* 用户临时状态及其变更事件的实时信息

想要了解 `Presence` 更多特性可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/presence" %}
[Presence](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/presence)
{% endcontent-ref %}

## Who Now

### 接口描述

调用 `whoNow()` API 方法，可以获得特定频道的参与人数，参与人员 userId列表及对应用户临时状态等实时信息。

{% hint style="info" %}
`whoNow() 针对 Message Channel 和 Stream Channel 都适用。`
{% endhint %}

### 接口方法

你可以通过以下方式调用 `whoNow()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.whoNow({ 
    String channel, 
    String channelGroup, 
    unit channelType, 
    Boolean includeUUIDs, 
    Boolean includeState
}): Promise<WhoNowResponse> ;
```
{% endcode %}

|      Parameter     |   Type  | Required |   Defaults  | Description                                                                                  |
| :----------------: | :-----: | :------: | :---------: | -------------------------------------------------------------------------------------------- |
|    **`channel`**   |  String |    Yes   | <p><br></p> | 指定需要获取信息的频道名，                                                                                |
| **`channelGroup`** |  String | Optional | <p><br></p> | 指定需要获取信息的频道组名称。                                                                              |
|   **channelType**  | Boolean |    Yes   | <p><br></p> | <p>频道类型:</p><p>1 代表 <code>Message Channel</code> </p><p>2 代表 <code>Stream Channel</code></p> |
|  **`includeUIDs`** | Boolean | Optional |    `true`   | 如果设置成`false` ，返回结果中将不包含在线成员UID。                                                              |
| **`includeState`** | Boolean | Optional |   `false`   | 如果设置`为ture,返回结果中将包含在线用户的临时状态数据。`                                                             |

### 基本用法

获取指定频道在线用户 `UID` 及用户状态

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

{% hint style="info" %}
&#x20;注意：`includeUIDs` 字段和 `includeState` 具有默认值。当显示的设置 `includeUIDs`  为 `false` 且 `includeState` 为 `true` 时，会返回错误，因为返回值中的 `State` 需与特定的 `UserId` 一一对应。
{% endhint %}

### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
type whoNowStatus = {
    "errorCode": number,                      // 本次操作错误码
    "operation": "RTMWhoNowOperation",        // 本次操作指令
    "reason":string                           // 本次操作成功或失败原因
}
 
// Example of Response
type whoNowResponse = {
    "errorCode": number,                       // 本次操作错误码
    "totalOccupancy": number,                  // 特定频道当前在线用户数
    "occupants": array                         // 在线用户详情列表
}
```
{% endcode %}

## Where Now

### 接口描述

调用 `whereNow()` API 方法，可以获得特定用户（UID) 当前订阅或加入了哪些频道。

{% hint style="info" %}
`whereNow()` 方法返回的结果中同时包含 `Message Channel` 和 `Stream Channel` 信息
{% endhint %}

### 接口方法

你可以通过以下方式调用 `whereNow()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.whereNow ({
    String uid
}): Promise<WhereNowResponse> ;
```
{% endcode %}

| Parameter |  Type  | Required | Defaults | Description   |
| :-------: | :----: | :------: | :------: | ------------- |
| **`uid`** | String | Optional |  当前用户uid | 指定需要查询的用户uid。 |

### 基本用法

获取指定用户现在所在的频道列表:

{% code lineNumbers="true" %}
```javascript
try{
    const result = await rtm.Presence.whereNow({
            uuid: "uuid"
        });
} catch (status) {
    console.log(status);
}
```
{% endcode %}

### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
{
    "errorCode": 0,
    "operation": "RTMWhereNowOperation",
    "reason": "Successful"
}
 
// Example of Response
{  
    "errorCode": 0,
    "totalChannel": 2,
    "channels": [
                    {
                        "channelName":"ch1",
                        "channelType":"Message Channel"
                    },
                    {
                        "channelName":"ch2",
                        "channelType":"Stream Channel"
                    }
                ]
}
```
{% endcode %}

## Set State

### 接口描述

使用 setState() 方法，你可以

### 接口方法

你可以通过以下方式调用 `whereNow()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.setState({
    String channel, 
    String channelGroup, 
    unit channelType, 
    Object state
}) : Promise<SetStateResponse>;
```
{% endcode %}

|      Parameter     |  Type  | Required | Description                                     |
| :----------------: | :----: | :------: | ----------------------------------------------- |
|    **`channel`**   | String |    Yes   | 为指定频道设置自定义用户状态。                                 |
| **`channelGroup`** | String | Optional | 为指定频道组设置自定义用户状态                                 |
|  **`channelType`** |   Int  |    Yes   | 频道类型，1代表 Message Channel， 2 代表 Stream Channel.  |
|     **`state`**    | Object |    Yes   | 键/值对格式的 JSON 对象如果指定的键已经存在，它将被新值覆盖。如果指定的键不存在则增加。 |

### 基本用法

设置用户自定义状态：

{% code lineNumbers="true" %}
```javascript
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
{% endcode %}

### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
{
    "errorCode": false,
    "operation": "RTMSetStateOperation",
    "reason": ""
}
// Example of Response
{
    "errorCode":200
}
```
{% endcode %}

## Get State

### 接口描述

### 接口方法

你可以通过以下方式调用 `whereNow()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.getState({
    String uid, 
    String channel , 
    String channelGroup, 
    uint channelType 
}):Promise<GetStateResponse>;
```
{% endcode %}

| Parameter |  Type  | Required | Defaults | Description   |
| :-------: | :----: | :------: | :------: | ------------- |
| **`uid`** | String | Optional |  当前用户uid | 指定需要查询的用户uid。 |

### 基本用法

获取用户自定义状态：

{% code lineNumbers="true" %}
```javascript
try{
    const result = await rtm.Presence.getState({
            uuid: "uid",
            channel: 'ch1',
            channelType: 1
        });
} catch (status) {
    console.log(status);
}
```
{% endcode %}

### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
{
    "errorCode": 0,
    "operation": "RTMGetStateOperation",
    "reason": ""
}
 
// Example of Response
{
    "errorCode":200,
    "state":{
            uid:"my_uid",
            state:{"mode":""happy,"move":"yes"},
            statusCount:2
        }
}
```
{% endcode %}

## Remove State

### 接口描述



### 接口方法

你可以通过以下方式调用 `removeState()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.Presence.removeState({
    String channel, 
    String channelGroup, 
    unit channelType, 
    Array state
}):Promise<SetStateResponse>;
```
{% endcode %}

|      Parameter     |  Type  | Required | Description                                    |
| :----------------: | :----: | :------: | ---------------------------------------------- |
|    **`channel`**   | String |    Yes   | 指定需要删除自定义状态的频道                                 |
| **`channelGroup`** | String | Optional | 指定需要删除自定义状态的频道组。                               |
|  **`channelType`** |  unit  |    Yes   | 频道类型，1代表 Message Channel， 2 代表 Stream Channel. |
|     **`state`**    | states | Optional | 需要删除的自定义状态的键列表，如果不设置，将shan chu 此频道所有状态。        |

{% hint style="info" %}
&#x20;注意：`removeState()` 方法只能删除本端用户的自定义状态。
{% endhint %}

### 基本用法

删除用户自定义状态：

{% code lineNumbers="true" %}
```javascript
try{
    const result = await rtm.Presence.removeState({
            channel: 'ch1',
            channelType: 1,
            state: ["mode"]
        });
} catch (status) {
    console.log(status);
}
```
{% endcode %}

{% hint style="info" %}
注意：**`state`** 字段如果不设置，默认将输出此用户在制定频道的全部自定义状态。
{% endhint %}

### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status
{
    "errorCode": 0,
    "operation": "RTMRemoveStateOperation",
    "reason": ""
}
 
// Example of Response
{
    "errorCode":0,
}
```
{% endcode %}
