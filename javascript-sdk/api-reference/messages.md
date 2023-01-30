# 🌷 Messages

RTM SDK 允许客户端使用消息传递实时信息，包含客户端希望通信的数据。

RTM SDK 有两种频道类型，分别是 Message Channel 和 Stream Channel。两种频道中消息的传递方式和需要调用的方法不同，区别如下：

* Message Channel：实时频道，消息通过频道传递，可扩展性强。本地用户可以调用 `publish` 方法在频道中发送消息，远端用户可以调用 `subscribe` 方法订阅频道并接收消息。
* Stream Channel：流传输频道，用户需要先加入频道，然后创建 Topic，消息通过 Topic 传递。本地用户可以调用 `publishTopicMessage` 方法在 Topic 中发送消息，远端用户可以调用 `subscribeTopic` 方法订阅 Topic 并接收消息。详见 Topic。

本文介绍如何在 Message Channel 中收发消息。

### Publish message

#### 接口描述

RTM SDK 的基础是发送消息的能力，你可以随时随地向频道中发送消息，消息会在 100 毫秒内传递到任何地方。你可以直接调用 `publish` 方法向频道的所有订阅者发送消息。即使没有订阅频道，也能在频道中发送消息。

注意：

* 调用该方法前，你需要在初始化时将 `ssl` 参数设置为 `true`，开启 SSL/TLS 加密功能。
* 以下做法可有效提升消息收发的可靠性：
  * 以串行的方式发送消息。每次发送前先检查上一条消息是否成功发送，如果成功，则发送下一条消息；如果不成功，则重发上一条消息。
  * 消息负载限制在 1 KB 以内，否则发送会失败。
  * 单个客户端向单个频道发送消息的速率上限为 60 QPS。如果发送速率超限，将会有部分消息被丢弃。在满足要求的情况下，速率越低越好。
* RTM 不保证所有订阅者接收到消息的顺序与发送者发送消息的顺序保持一致。如果你想要保障消息有序性，声网建议你在消息负载中自定义消息序号。

#### 接口方法

你可以通过以下方式调用 `publish` 方法：

```javascript
rtm.publish(
  {
    msChannel: "my_channel",
    message: {"text": "Hello World!"}
  },
  function(status, response) {
    console.log(status);
    console.log(response);
  }
);
```

|  Parameter  |   Type   | Required | Default |              Description              |
| :---------: | :------: | :------: | :-----: | :-----------------------------------: |
|  `message`  |  Object  |    Yes   |    /    | 消息负载。支持任意 JSON 可序列化数据，包含对象、数组、整数和字符串。 |
| `msChannel` |  String  |    Yes   |    /    |     指定消息发送的频道名称。同一时间仅支持向一个频道发送消息。     |
|    `meta`   |  Object  | Optional |    /    |                  元数据。                 |
|  `callback` | Function | Optional |    /    |                 回调函数。                 |

#### 基本用法

示例 1：向指定频道发送 String 消息

```javascript
rtm.publish(
    {
        message: {
            such: 'object'
        },
        msChannel: 'my_channel',
        meta: {
            "cool": "meta"
        }   // publish extra meta with the request
    },
    function (status, response) {
        if (status.error) {
            // handle error
            console.log(status)
        } else {
            console.log("message Published w/ timetoken", response.timetoken)
        }
    }
);
```

示例 2：向指定频道发送 JSON 序列化消息

```javascript
var newMessage = {
    text: 'Hi There!'
}
rtm.publish(
    {
        message: newMessage,
        msChannel: 'my_channel'
    },
    function(status, response) {
        if (status.error) {
            console.log("publishing failed w/ status: ", status);
        } else {
            console.log("message published w/ server response: ", response);
        }
    }
);
```

#### 返回值

```javascript
[200,[1,"Sent","14375189629170609"]]
```

### Receive message

RTM 提供消息、状态、事件变更等信息的事件通知。通过设置 Event Listener，你可以接收已订阅频道中的消息。

关于如何添加和设置 Listener，可以参考 Event Listeners 章节。
