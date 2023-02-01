# 🦜 Topics

Topic 为用户提供了一种在 Stream Channel 中管理数据流的机制。

注意：Topic 仅适用于 Stream Channel。

想要了解 Topic 的更多特性，可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/topics" %}
[https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/topics](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/topics)
{% endcontent-ref %}

### Join Topic

#### 接口描述

调用 `joinTopic` 方法加入 Topic 即可注册为该 Topic 的消息发布者，可以执行发布 Topic 消息的操作。一个 Topic 中可以有多个消息发布者。

成功加入 Topic 后，频道中的远端用户会收到 `action` 类型为 `join` 的 Topic 事件。

注意：

* 该方法不影响你是否订阅该 Topic。
* 请在 `join` 方法后调用 `joinTopic` 方法。
* 每次调用 `joinTopic` 方法只能加入一个 Topic。如需加入多个 Topic，你需要多次调用该方法。单个客户端可以同时加入最多 8 个 Topic。

#### 接口方法

你可以通过以下方式调用 `joinTopic` 方法：

```javascript
joinTopic({
    String topic,
    Number Qos,
    boolean syncWithRTC,
    Objests extraMeta
}):Promise<JoinTopicResponse>;
```

|   Parameter   |   Type  | Required | Defaults |                                         Description                                        |
| :-----------: | :-----: | :------: | -------- | :----------------------------------------------------------------------------------------: |
|    `topic`    |  String |    Yes   | /        |                                          Topic 名称                                          |
| `syncWithRTC` | Boolean | Optional | false    |                           是否按照 RTC 音视频流的时间戳在此 Topic 中同步发送 RTM 消息                           |
|     `Qos`     |  Number | Optional | 0        | <p>后续发送 Topic 消息时的 QoS 保障：</p><ul><li>0: 消息数据到达时不保序</li></ul><ul><li>1：消息数据到达时保序</li></ul> |
|  `extraMeta`  |  Object | Optional |          |                                         Topic 的辅助信息                                        |

#### 基本用法

加入 Topic 示例如下：

```javascript
try{
    const result = await stChannel.joinTopic({
        topic:"gesture",
        Qos:1,
        syncWithRTC:false,
        extraMeta:{}
        });
} catch (status) {
     console.log(status);
}
```

加入 Topic 成功时，返回值示例如下：

```javascript
type joinTopicStatus ={
    "errorCode": number,
    "operation": "RTMJoinTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
type joinTopicResponse = {
    "errorCode": 0,                     // 本次操作错误码
    "channel": "stChannel",             // 频道名称
    "topic": "gesture",                 // Topic 名称
    "meta": ""                          // 加入 Topic 辅助信息
    "uid": "myuid"                      // 用户 uid
}
```

加入 Topic 失败时，返回值示例如下：

```javascript
type joinTopicStatus ={
    "errorCode": number,
    "operation": "RTMJoinTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
type joinTopicResponse = {
    "errorCode": 7,                     // 未加入频道
    "channel": "stChannel",             // 频道名称
    "topic": "gesture",                 // Topic 名称
    "meta": ""                          // 加入 Topic 辅助信息
    "uid": "myuid"
}
```

#### 返回值

```javascript
// Example of Status, used in callback pattern
type joinTopicStatus ={
    "errorCode": number,
    "operation": "RTMJoinTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
type joinTopicResponse = {
    "errorCode": number,                // 本次操作错误码
    "channel": String,              // 频道名称
    "topic": String,                    // Topic 名称
    "meta": Object,                     // 加入 Topic 辅助信息
    "uid": String                       // 用户 uid
}
```

### Publish Topic Message

#### 接口描述

RTM SDK 具备向 Stream Channel 中发送大并发消息的能力，消息会在 100 毫秒内传递到任何地方。调用 `publishTopicMessage` 方法可以向 Topic 的所有订阅者发送消息。

成功调用该方法后，频道中订阅该 Topic 且订阅该消息发布者的用户会收到消息事件。

注意：

* 调用该方法前需先调用 `joinTopic` 加入 Topic。
* 每次调用该方法只能向一个 Topic 发送一条消息。如需向一个 Topic 发送多条消息，你需要多次调用该方法。
* 以下做法可有效提升消息收发的可靠性：
  * 在调用 `joinTopic` 时将 `Qos` 参数设为 `1` 以开启该 Topic 的消息保序能力。
  * 以串行的方式发送消息。每次发送前先检查上一条消息是否成功发送，如果成功，则发送下一条消息；如果不成功，则重发上一条消息。
  * 消息负载限制在 1 KB 以内，否则发送会失败。
  * 单个客户端向单个频道发送消息的速率上限为 60 QPS。如果发送速率超限，将会有部分消息被丢弃。在满足要求的情况下，速率越低越好。
* RTM 不保证所有订阅者接收到消息的顺序与发送者发送消息的顺序保持一致。如果你想要保障消息绝对有序，声网建议你在消息负载中自定义消息序号。

#### 接口方法

你可以通过以下方式调用 `publishTopicMessage` 方法：

```javascript
publishTopicMessage({
    Object message,
    String topic,
    Object meta,
    Number sendTs
}):Promise<PublishTopicMessageResponse>;
```

|  Parameter  |  Type  | Required | Defaults |                                                                                                     Description                                                                                                    |
| :---------: | :----: | :------: | :------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  `message`  | Object |    Yes   |     /    |                                                                                        消息负载。支持任意 JSON 可序列化数据，包含对象、数组、整数和字符串                                                                                        |
|   `topic`   | String |    Yes   |     /    |                                                                                                      Topic 名称                                                                                                      |
| `extraMeta` | Object | Optional |     /    |                                                                                                     Topic 的辅助信息                                                                                                    |
|   `sendTs`  | Number | Optional |     /    | <p>消息发送时间戳：</p><ul><li>0：SDK 会按照 RTC 音视频流的发送时间戳发送消息</li></ul><ul><li>非 0：SDK 按照你实际填写的时间戳发送消息</li></ul><p><br>注意：该参数仅在调用 <code>joinTopic</code> 方法时设置 <code>syncwithRTC</code> 参数为 <code>true</code> 的情况下生效<br></p> |

#### 基本用法

示例 1：向指定频道发送 String 消息:

```javascript
try{
    const result = await stChannel.publishTopicMessage({
        topic:"Gesture",
        message: JSON.stringify({such: 'object'}),
        extraMeta: {
            "cool": "meta"
        }   // publish extra meta with the request
    });
} catch (status){
    console.log(status);
}
```

成功发送消息时，返回值示例如下：

```javascript
{
    "errorCode": 0,
    "timeToken": "16711845465572806"
}
```

示例 2：向指定频道发送 JSON 序列化消息

```javascript
var newMessage = {
    text: 'Hi There!'
}

try{
    const result = await stChannel.publishTopicMessage({
        message: JSON.stringify(newMessage),
        topic:"Gesture"
    });
} catch (status){
    console.log(status);
}
```

#### 返回值

```javascript
// Example of Status, used in callback pattern
type publishTopicMessageStatus ={
    "errorCode": number,
    "operation": "RTMPublishTopicMessageOperation",
    "reason":"Error Reason"
}

// Example of Response
type publishTopicMessageResponse = {
    "errorCode": number,                // 本次操作错误码
    "timeToken": number                 // 发送时间戳
}
```

### Leave Topic

#### 接口描述

如果你不再需要向某个 Topic 发布消息，你可以调用 `leaveTopic` 方法离开该 Topic 并取消注册为该 Topic 的消息发布者，从而释放资源。

成功离开 Topic 后，频道中的远端用户会收到 `action` 类型为 `leave` 的 Topic 事件。

注意：

* 每次调用该方法只能离开一个 Topic。如需离开多个 Topic，你需要多次调用该方法。
* 该方法不影响你是否订阅该 Topic，也不影响其他用户对该 Topic 的任何操作。

#### 接口方法

你可以通过以下方式调用 `leaveTopic` 方法：

```javascript
leaveTopic({
    String topic
}):Promise<LeaveTopicResponse>;
```

|  Parameter |   Type  | Required | Defaults | Description |
| :--------: | :-----: | :------: | :------: | :---------: |
|   `topic`  |  String |    Yes   |     /    |   Topic 名称  |
| `callback` | Boolean | Optional |     /    |     回调函数    |

#### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const result = await stChannel.leaveTopic({
        topic:"Gesture"
    });

    console.log("leave Topic w/ server response: ", result);
} catch (status){
    console.log("leave Topic failed w/ status: ", status);
}
```
{% endcode %}

成功离开 Topic 时，返回值示例如下：

```javascript
{
    "errorCode": 0,                 // 本次操作错误码
    "channel": MyChannel,                   // 频道名称
    "topic": Gesture,                   // Topic 名称
    "uid" "Tony",                       // 用户 UID
}
```

#### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status, used in callback pattern
type leaveTopicStatus ={
    "errorCode": number,
    "operation": "RTMLeaveTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
 type leaveTopicResponse = {
    "errorCode": number,                // 本次操作错误码
    "channel": string,                  // 频道名称
    "topic": string,                    // Topic 名称
    "uid" string,                       // 用户 UID
}
```
{% endcode %}

### Subscribe Topic

#### 接口描述

加入频道后，你可以调用 `subscribeTopic` 方法订阅该频道的 Topic 及 Topic 中的消息发布者。

`subscribeTopic` 为增量方法。例如，第一次调用该方法时，订阅消息发布者列表为 `[UserA,UserB]`, 第二次调用该方法时，订阅消息发布者列表为 `[UserB,UserC]`，则最后成功订阅的结果是 `[UserA，UserB,UserC]`。你可以通过 `getSubscribedUserList` 查询当前已经订阅的消息发布者名单列表。 // TODO getSubscribedUserList 需要写么？

频道中单个 Topic 的消息发布者的数量没有上限，但对于 Topic 订阅者，目前最多只能同时订阅 50 个 Topic，每个 Topic 中最多只能订阅 64 个消息发布者。

注意：

* 每次调用该方法只能订阅一个 Topic。如需订阅多个 Topic，你需要多次调用该方法。
* 如果用户网络连接出现问题，RTM SDK 会自动尝试重新连接，但在你在断连期间订阅的消息会丢失。

#### 接口方法

你可以通过以下方式调用 `subscribeTopic` 方法：

{% code lineNumbers="true" %}
```javascript
subscribeTopic({
    String topic,
    Object filter
}):Promise<SubscribeTopicResponse>;
```
{% endcode %}

| Parameter |  Type  | Required | Defaults |                                                                Description                                                                |
| :-------: | :----: | :------: | :------: | :---------------------------------------------------------------------------------------------------------------------------------------: |
|  `topic`  | String |    Yes   |     /    |                                                                  Topic 名称                                                                 |
|  `filter` | Object | Optional |     /    | Topic 过滤条件。该参数包含一个 `UIDs` 数组（Optional），你可以在 `UIDs` 数组中填写 UID 过滤条件。填写该参数后，SDK 会根据该设置订阅 Topic 中符合条件的消息发布者。如果不填写该参数，SDK 会在 Topic 中随机订阅消息发布者。 |

#### 基本用法

示例 1：订阅 Topic 中指定的消息发布者

```javascript
var UIDs = ["zhangsan","lisi","wangwu"]

try{
    const result = await rtm.subscribeTopic({
                        topic:"Gesture",
                        filter:{
                            UIDs:UIDs},
                    });
    console.log("subscribe Topic w/ server response: ", result);
} catch (status){
    console.log("subscribe Topic failed w/ status: ", status);
}
```

示例 2：随机订阅 Topic 中的 64 个消息发布者

```javascript
try{
    const result = await stChannel.subscribeTopic({
                    topic:"Gesture"
                    });
    console.log("subscribe Topic w/ server response: ", result);


}catch(status){
    console.log("subscribe Topic failed w/ status: ", status);

}
```

订阅成功时，返回示例如下：

```javascript
{
    "errorCode": 0,                             // 本次操作错误码
    "channel": "MyChannel",                     // 频道名称
    "topic": "Gesture",                         // Topic 名称
    "uid" "Tony",                               // 用户 UID
    "succedList": ["Tony1","Tony2","Tony3"],    // 成功订阅的发布者列表
    "failedList": []                            // 失败订阅的发布者列表
}
```

订阅失败时，返回示例如下：

```javascript
{
    "errorCode": 8,
    "operation": "RTMSubscribeTopicOperation",
    "reason":"invalid operations"
}
```

```javascript
{
    "errorCode": 9,
    "operation": "RTMSubscribeTopicOperation",
    "reason":"The count of UIDs Array is exceed the large limited"   // 订阅的 UIDs 列表超过 64 人
}
```

#### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status, used in callback pattern
type subscribeTopicStatus ={
    "errorCode": number,
    "operation": "RTMSubscribeTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
 type subscribeTopicResponse = {
    "errorCode": number,                // 本次操作错误码
    "channel": string,                  // 频道名称
    "topic": string,                    // Topic 名称
    "uid" string,                       // 用户 UID
    "succedList": Array,                // 成功订阅的发布者列表
    "failedList": Array                 // 失败订阅的发布者列表
}
```
{% endcode %}

### Unsubscribe Topic

#### 接口描述

如果你对一个或多个 Topic 不再感兴趣，或者不再需要订阅 Topic 中的一个或多个消息发布者，你可以调用 `unsubscribeTopics` 方法取消订阅该 Topic 或取消订阅该 Topic 中指定的消息发布者。

#### 接口方法

你可以通过以下方式调用 `unsubscribeTopics` 方法：

{% code lineNumbers="true" %}
```javascript
unsubscribeTopics({
    string topic,
    Object filter
}):Promise<UnsubscribeTopicResponse>;
```
{% endcode %}

| Parameter |  Type  | Required | Defaults |                                                                  Description                                                                  |
| :-------: | :----: | :------: | :------: | :-------------------------------------------------------------------------------------------------------------------------------------------: |
|  `topic`  | String |    Yes   |     /    |                                                                    Topic 名称                                                                   |
|  `filter` | Object | Optional |     /    | Topic 过滤条件。该参数包含一个 `UIDs` 数组（Optional），你可以在 `UIDs` 数组中填写 UID 过滤条件。填写该参数后，SDK 会根据该设置取消订阅 Topic 中符合条件的消息发布者。如果不填写该参数，SDK 会取消订阅 Topic 中的所有消息发布者。 |

#### 基本用法

示例 1：取消订阅 Topic 中的指定消息发布者

{% code lineNumbers="true" %}
```javascript
try{
    const result = await rtm.unsubscribeTopics({
                topic:"Gesture",
                filter:{
                    UID:["Tony","Bo"]}
            });
    console.log("unsubscribe Topic w/ server response: ", result);


} catch (status) {

    console.log("unsubscribe Topic w/ server response: ", result);


}
```
{% endcode %}

取消订阅成功时，返回示例如下：

```javascript
{
    "errorCode": 0,                             // 本次操作错误码
    "channel": "MyChannel",                     // 频道名称
    "topic": "Gesture",                         // Topic 名称
    "uid" "Tony",                               // 用户 UID
    "succedList": ["Tony1"],    // 成功订阅的发布者列表
    "failedList": ["Bob"]                           // 失败订阅的发布者列表
}
```

取消订阅失败时，返回示例如下：

```javascript
{
    "errorCode": 8,
    "operation": "RTMUnsubscribeTopicOperation",
    "reason":"invalid operations"
}
```

示例 2：取消订阅 Topic 中的所有消息发布者

```javascript
try{
    const result = await rtm.unsubscribeTopics({
                topic:"Gesture"
            });
    console.log("unsubscribe Topic w/ server response: ", result);

} catch (status) {

    console.log("unsubscribe Topic w/ server response: ", result);

}
```

取消订阅成功时，返回示例如下：

```javascript
{
    "errorCode": 0,                             // 本次操作错误码
    "channel": "MyChannel",                     // 频道名称
    "topic": "Gesture",                         // Topic 名称
    "uid" "Tony",                               // 用户 UID
    "succedList": ["Bob"],                      // 成功订阅的发布者列表
    "failedList": []                            // 失败订阅的发布者列表
}
```

#### 返回值

{% code lineNumbers="true" %}
```javascript
// Example of Status, used in callback pattern
type unsubscribeTopicStatus ={
    "errorCode": number,
    "operation": "RTMUnsubscribeTopicOperation",
    "reason":"Error Reason"
}

// Example of Response
 type unsubscribeTopicResponse = {
    "errorCode": number,                // 本次操作错误码
    "channel": string,                  // 频道名称
    "topic": string,                    // Topic 名称
    "uid" string,                       // 用户 UID
    "succedList": Array,                // 成功取消订阅的发布者列表
    "failedList": Array                 // 失败取消订阅的发布者列表
}
```
{% endcode %}
