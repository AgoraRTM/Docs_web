# 🍁 Channels

`Channels`（频道） 是 RTM 2 实时网络中一种数据传输的管理机制，任何订阅或加入频道的用户都可以在 100ms 内接收到频道中传输的消息和事件，RTM 2 允许客户端订阅数百甚至数千个频道。大多数 RTM 2 API 都将以频道为基础进行发送、接收、加密等行为。

基于声网的能力，RTM 2 的频道分成两种类型以匹配不同的应用场景：

* `Message Channel`：遵循行业最标准的 Pub/Sub （发布/订阅）模式，频道无需提前创建，随用随取，频道内的生产者和消费者数量没有上限。
* `Stream Channel`：遵循行业类似观察者模式的 Room （房间）概念，用户需要进入频道（`Join Channel`）才能进行下一步操作。频道中允许创建不同的 `Topic`，频道内最多容纳 1000 个用户。

想要了解 `Message Channel` 和 `Stream Channel` 更多用法及适用场景，可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/channels" %}
[Channels](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/channels)
{% endcontent-ref %}

## Subscribe Message Channel

### 接收消息

你的 App 是通过设置来接收已订阅频道中的消息和事件的。Event Listener 是你的应用中利用RTM 来接收消息、状态、事件变更等信息的唯一入口。

关于如何添加和设置 Listener，可以参考  [Event Listeners](configuration.md#event-listener) 一节。

### 接口描述

通过调用 `subscribe` 方法，可以让客户端和 RTM 服务器之间建立起 TCP 长连接，客户端可以从监听的回调和事件中获取已订阅的 Message Channel 中消息和状态。

{% hint style="info" %}
`subscribe()` 方法仅针对 `Message Channel` 适用。
{% endhint %}

### 接口方法

你可以通过以下方式调用 `subscribe()` API 接口：

{% code lineNumbers="true" %}
```javascript
rtm.subscribe({
    string channel, 
    string channelGroup, 
    Boolean withPresence, 
    Boolean beQuite, 
    Boolean withMetaData 
}): Promise<SubscribeResponse> ;
```
{% endcode %}

|    Parameter   |   Type  | Required | Defaults | Description                         |
| :------------: | :-----: | :------: | :------: | ----------------------------------- |
|    `channel`   |  String |    Yes   |     无    | 指定订阅的频道名。  只针对`Message Channel` 适用。 |
|  `withMessage` | Boolean | Optional |   true   | 是否订阅此频道中的消息                         |
| `withPresence` | Boolean | Optional |   true   | 是否订阅此频道中远端用户的 `Presence` 事件信息       |
|    `beQuite`   | Boolean | Optional |   false  | 是否禁止本地用户发送 `Presence` 事件（静默加入）      |
| `withMetaData` | Boolean | Optional |   true   | 是否订阅此频道中的频道属性：                      |

### **基本用法**

示例 1：仅订阅频道

```javascript
rtm.subscribe({
    channel: 'my_channel_1'
});
```

示例 2：订阅频道和频道内远端用户的 Presence 事件

```javascript
rtm.subscribe({
    channel: 'my_channel',
    withPresence: true,
    beQuite:false
});
```

示例 3：订阅频道和频道内远端用户的 Presence 事件，但禁止本地用户发送 Presence 事件，即隐身登陆

```javascript
rtm.subscribe({
    msChannel: 'my_channel',
    withPresence: true,
    beQuite:true
});
```

如果远端用户订阅 `my_channel` 频道，你会收到 `join` 事件：

```javascript
{
    "channelType":"msChannel",
    "channelName": "my_channel",
    "channel": "my_channel",
    "subscription": null,
    "actualChannel": null,
    "subscribedChannel": "my_channel-rtmres",
    "action": "join",
    "timetoken": "15119466699655811",
    "occupancy": 2,
    "uid": "User1",
    "timestamp": 1511946669
}
```

如果远端用户取消订阅 `my_channel` 频道，你会收到 `leave` 事件：

```javascript
{
    "channelType":"msChannel",
    "channelName": "my_channel",
    "channel": "my_channel",
    "subscription": null,
    "actualChannel": null,
    "subscribedChannel": "my_channel-rtmpres",
    "action": "leave",
    "timetoken": "15119446002445794",
    "occupancy": 1,
    "uid": "User1",
    "timestamp": 1511944600
}
```

如果远端用户和 `my_channel` 频道的连接超时，你会收到 `timeout` 事件：

```javascript
{
   "channelType":"msChannel",
   "channelName": "my_channel",
   "channel": "my_channel",
   "subscription": null,
   "actualChannel": null,
   "subscribedChannel": "my_channel-rtmpres",
   "action": "timeout",
   "timetoken": "15119519897494311",
   "occupancy": 3,
   "uid": "User2",
   "timestamp": 1511951989
}
```

如果远端用户在 `my_channel` 频道中的自定义状态发生改变，你会收到 `state-change` 事件：

```javascript
{
    "channelType":"msChannel",
    "channelName": "my_channel",
    "channel": "my_channel",
    "subscription": null,
    "actualChannel": null,
    "subscribedChannel": "my_channel-rtmpres",
    "action": "state-change",
    "state": {
        "isTyping": true
    },
    "timetoken": "15119477895378127",
    "occupancy": 5,
    "uid": "User4",
    "timestamp": 1511947789
}
```

如果你设置了定时通知模式，当远端用户在设置的时间段内发生状态变化时，你会收到 `interval` 事件：

```javascript
{
    "channelType":"msChannel",
    "channelName": "my_channel",
    "action":"interval",
    "timestamp":1548340678,
    "occupancy":2,
    "join":["Tony","Zhao"]},
    "timedout":["Robbin"],
    "b":"my-channel-rtmpres"
}
```

示例 4：带用户自定义状态的订阅：

{% code lineNumbers="true" %}
```javascript
var rtm = new RTM({
    /* initiation arguments */
})
rtm.onEvent({
    status: function(statusEvent) {
        if (statusEvent.category === "RTMConnectedCategory") {
            var newState = {
                new: 'state'
            };
            rtm.setState(
                {
                    state: newState
                },
                function (status) {
                    // handle state setting response
                }
            );
        }
    },
    message: function(message) {
        // handle message
    },
    presence: function(presenceEvent) {
        // handle presence
    }
})

rtm.subscribe({
    msChannel: 'ch1', 'ch2', 'ch3'
});
```
{% endcode %}

## Unsubscribe Message Channel

**API 描述**

```javascript
rtm.unsubscribe({
    string msChannel
}): Promise<UnsubscribeResponse>;
```

如果你无需关注某个频道，调用 `unsubscribe` 方法取消订阅该频道。每次调用该方法只能取消订阅一个频道，如需取消订阅多个频道，你需要多次调用该方法。

成功取消订阅该频道后，其他订阅该频道的远端用户会收到 `leave` 事件。

**参数介绍**

|      参数     |   类型   | 是否必填 | 默认值 | 描述                                                                                                 |
| :---------: | :----: | :--: | :-: | -------------------------------------------------------------------------------------------------- |
| `msChannel` | String |  必填  |  无  | <p>指定订阅的频道名称。如需订阅多个频道，将频道名称以字符串数组的方式填入该参数。</p><p><strong>Note</strong>: 单个客户端最多可以同时订阅 100 个频道。</p> |

**基本用法**

```javascript
rtm.unsubscribe({msChannel: "chats.room1"}): Promise<UnsubscribeResponse>;
```

## Create Stream Channel

**API 描述**

```javascript
createStreamChannel({String stChannel}):Promise<CreateStreamChannelResponse>;
```

在使用 Stream Channel 之前，你需要先调用 `createStreamChannel` 方法创建 `StreamChannel` 实例。成功创建 `StreamChannel` 实例后，你可以调用 Stream Channel 相关方法进行加入频道、离开频道、销毁频道、发送消息、订阅消息等操作。

注意： 每次调用该方法只能创建一个 `StreamChannel` 实例，如需创建多个 `StreamChannel` 实例，你需要多次调用该方法。

**参数介绍**

|      参数     |   类型   | 是否必填 | 默认值 |   描述  |
| :---------: | :----: | :--: | :-: | :---: |
| `stChannel` | String |  必填  |  无  | 频道名称。 |

**基本用法**

```javascript
try{
    const Loc_stChannel = await rtm.createStreamChannel({
        stChannel:"Location"
        });
    console.log("Create Stream Channel w/ server response: ", response);
} catch (status){
    console.log("Create Stream Channel failed w/ status: ", status);
}
```

## Join Stream Channel

**API 描述**

```javascript
rtm.streamChannel.join({Boolean withPresence, Boolean beQuite, Boolean withMetaData })
```

成功创建 Stream Channel 后，你可以调用 `join` 方法加入 Stream Channel。

注意：

* 每次调用该方法只能加入一个频道，如需加入多个频道，你需要多次调用该方法。单个客户端可以同时加入最多 100 个频道。
* 用户需要收到成功加入频道的 `onJoinResult` 回调才能继续进行频道相关的操作。

**参数介绍**

|       参数       |    类型   | 是否必填 |  默认值  | 描述                                                                                 |
| :------------: | :-----: | :--: | :---: | ---------------------------------------------------------------------------------- |
| `withPresence` | Boolean |  选填  |  true | <p>是否订阅远端用户的 Presence 事件：</p><ul><li>true：订阅</li><li>false：不订阅</li></ul>           |
|    `beQuite`   | Boolean |  选填  | false | <p>是否禁止本地用户发送 Presence 事件：</p><ul><li>true：禁止</li></ul><ul><li>false：不禁止</li></ul> |
| `withMetaData` | Boolean |  选填  |  true | <p>是否订阅频道属性：</p><ul><li>true：订阅</li></ul><ul><li>false：不订阅</li></ul>               |

**基本用法**

示例 1：仅加入频道

```javascript
Loc_stChannel.join();
```

示例 2：加入频道并订阅频道内远端用户的 Presence 事件

```javascript
Loc_stChannel.join({
    withPresence: true,
    beQuite:false
});
```

示例 3：加入频道并订阅频道内远端用户的 Presence 事件，但禁止本地用户发送 Presence 事件，即隐身登陆

```javascript
Loc_stChannel.join({
    withPresence: true,
    beQuite:true
});
```

如果远端用户订阅 `my_channel` 频道，你会收到 `join` 事件：

```javascript
{
    "channelType":"stChannel",
    "channelName": "my_channel",
    "channelGroup": null,
    "channelTopic": null,
    "action": "join",
    "timetoken": "15119466699655811",
    "occupancy": 2,
    "uid": "User1",
    "timestamp": 1511946669
}
```

如果远端用户取消订阅 `my_channel` 频道，你会收到 `leave` 事件：

```javascript
{
    "channelType":"stChannel",
    "channelName": "my_channel",
    "channelGroup": null,
    "channelTopic": null,
    "action": "leave",
    "timetoken": "15119466699655811",
    "occupancy": 2,
    "uid": "User1",
    "timestamp": 1511946669
}
```

如果远端用户和 `my_channel` 频道的连接超时，你会收到 `timeout` 事件：

```javascript
{
    "channelType":"stChannel",
    "channelName": "my_channel",
    "channelGroup": null,
    "channelTopic": null,
    "action": "leave",
    "timetoken": "15119466699655811",
    "occupancy": 2,
    "uid": "User1",
    "timestamp": 1511946669
}
```

如果远端用户在 `my_channel` 频道中的自定义状态发生改变，你会收到 `state-change` 事件：

```javascript
{
    "channelType":"stChannel",
    "channelName": "my_channel",
    "channelGroup": null,
    "channelTopic": null,
    "action": "state-change",
    "state": {
        "isTyping": true
    },
    "timetoken": "15119477895378127",
    "occupancy": 5,
    "uid": "User4",
    "timestamp": 1511947789
}
```

如果你设置了定时通知模式，当远端用户在设置的时间段内发生状态变化时，你会收到 `interval` 事件：

```javascript
{
    "channelType":"stChannel",
    "channelName": "my_channel",
    "channelGroup": null,
    "channelTopic": null,
    "action":"interval",
    "timestamp":1548340678,
    "occupancy":2,
    "join":["Tony","Zhao"],
    "timedout":["Robbin"],
}
```

示例 4：带用户自定义状态的加入

```javascript
var rtm = new RTM({
    /* initiation arguments */
})
rtm.addListener({
    status: function(statusEvent) {
        if (statusEvent.category === "RTMConnectedCategory") {
            var newState = {
                new: 'state'
            };
            rtm.setState(
                {
                    state: newState
                },
                function (status) {
                    // handle state setting response
                }
            );
        }
    },
    message: function(message) {
        // handle message
    },
    presence: function(presenceEvent) {
        // handle presence
    }
})

Loc_stChannel.join();
```

## Leave Stream Channel

**API 描述**

```javascript
rtm.streamChannel.leave():Promise<LeaveStreamChannelResponse>;
```

如果你不再需要待在某个频道中，你可以调用 `leave` 离开该频道。SDK 会触发如下事件：

* 调用该方法后，SDK 会触发 `onLeaveResult` 回调。
* 成功离开频道后，频道中的远端用户会收到 `leave` 事件。

**基本用法**

```javascript
try{
    const result = await rtm.leave();
    console.log("leave Stream Channel w/ server response: ", response);


} catch (status){
    console.log("leave Stream Channel failed w/ status: ", status);
}
```

## Destroy Stream Channel

**API 描述**

```javascript
rtm.streamChannel.release():Promise<ReleaseStreamChannelResponse>;
```

如果你不再需要某个频道，你可以调用 `release` 方法销毁对应的 `StreamChannel` 实例以释放资源。调用该方法销毁 `StreamChannel` 实例不会销毁该频道，后续可通过再次调用 `createStreamChannel` 和 `join` 重新加入该频道。

注意： 如果不先调用 `leave` 离开频道而直接调用 `release` 销毁频道实例，SDK 会自动调用 `leave` 并触发对应的事件。

**基本用法**

```javascript
try{
    const result = await Loc_stChannel.release();
    console.log("release Stream Channel w/ server response: ", response);


} catch(status) {
    console.log("release Stream Channel failed w/ status: ", status);
}
```
