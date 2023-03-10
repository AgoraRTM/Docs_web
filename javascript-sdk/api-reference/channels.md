# ð Channels

`Channels`ï¼é¢éï¼ æ¯ RTM 2 å®æ¶ç½ç»ä¸­ä¸ç§æ°æ®ä¼ è¾çç®¡çæºå¶ï¼ä»»ä½è®¢éæå å¥é¢éçç¨æ·é½å¯ä»¥å¨ 100ms åæ¥æ¶å°é¢éä¸­ä¼ è¾çæ¶æ¯åäºä»¶ï¼RTM 2 åè®¸å®¢æ·ç«¯è®¢éæ°ç¾çè³æ°åä¸ªé¢éãå¤§å¤æ° RTM 2 API é½å°ä»¥é¢éä¸ºåºç¡è¿è¡åéãæ¥æ¶ãå å¯ç­è¡ä¸ºã

åºäºå£°ç½çè½åï¼RTM 2 çé¢éåæä¸¤ç§ç±»åä»¥å¹éä¸åçåºç¨åºæ¯ï¼

* `Message Channel`ï¼éµå¾ªè¡ä¸ææ åç Pub/Sub ï¼åå¸/è®¢éï¼æ¨¡å¼ï¼é¢éæ éæååå»ºï¼éç¨éåï¼é¢éåççäº§èåæ¶è´¹èæ°éæ²¡æä¸éã
* `Stream Channel`ï¼éµå¾ªè¡ä¸ç±»ä¼¼è§å¯èæ¨¡å¼ç Room ï¼æ¿é´ï¼æ¦å¿µï¼ç¨æ·éè¦è¿å¥é¢éï¼`Join Channel`ï¼æè½è¿è¡ä¸ä¸æ­¥æä½ãé¢éä¸­åè®¸åå»ºä¸åç `Topic`ï¼é¢éåæå¤å®¹çº³ 1000 ä¸ªç¨æ·ã

æ³è¦äºè§£ `Message Channel` å `Stream Channel` æ´å¤ç¨æ³åéç¨åºæ¯ï¼å¯ä»¥æ¥çï¼

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/channels" %}
[Channels](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/channels)
{% endcontent-ref %}

## Subscribe Message Channel

### æ¥æ¶æ¶æ¯

ä½ ç App æ¯éè¿è®¾ç½®æ¥æ¥æ¶å·²è®¢éé¢éä¸­çæ¶æ¯åäºä»¶çãEvent Listener æ¯ä½ çåºç¨ä¸­å©ç¨RTM æ¥æ¥æ¶æ¶æ¯ãç¶æãäºä»¶åæ´ç­ä¿¡æ¯çå¯ä¸å¥å£ã

å³äºå¦ä½æ·»å åè®¾ç½® Listenerï¼å¯ä»¥åè  [Event Listeners](configuration.md#event-listener) ä¸èã

### æ¥å£æè¿°

éè¿è°ç¨ `subscribe` æ¹æ³ï¼å¯ä»¥è®©å®¢æ·ç«¯å RTM æå¡å¨ä¹é´å»ºç«èµ· TCP é¿è¿æ¥ï¼å®¢æ·ç«¯å¯ä»¥ä»çå¬çåè°åäºä»¶ä¸­è·åå·²è®¢éç Message Channel ä¸­æ¶æ¯åç¶æã

{% hint style="info" %}
`subscribe()` æ¹æ³ä»éå¯¹ `Message Channel` éç¨ã
{% endhint %}

### æ¥å£æ¹æ³

ä½ å¯ä»¥éè¿ä»¥ä¸æ¹å¼è°ç¨ `subscribe()` API æ¥å£ï¼

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
|    `channel`   |  String |    Yes   |     æ     | æå®è®¢éçé¢éåã  åªéå¯¹`Message Channel` éç¨ã |
|  `withMessage` | Boolean | Optional |   true   | æ¯å¦è®¢éæ­¤é¢éä¸­çæ¶æ¯                         |
| `withPresence` | Boolean | Optional |   true   | æ¯å¦è®¢éæ­¤é¢éä¸­è¿ç«¯ç¨æ·ç `Presence` äºä»¶ä¿¡æ¯       |
|    `beQuite`   | Boolean | Optional |   false  | æ¯å¦ç¦æ­¢æ¬å°ç¨æ·åé `Presence` äºä»¶ï¼éé»å å¥ï¼      |
| `withMetaData` | Boolean | Optional |   true   | æ¯å¦è®¢éæ­¤é¢éä¸­çé¢éå±æ§ï¼                      |

### **åºæ¬ç¨æ³**

ç¤ºä¾ 1ï¼ä»è®¢éé¢é

```javascript
rtm.subscribe({
    channel: 'my_channel_1'
});
```

ç¤ºä¾ 2ï¼è®¢éé¢éåé¢éåè¿ç«¯ç¨æ·ç Presence äºä»¶

```javascript
rtm.subscribe({
    channel: 'my_channel',
    withPresence: true,
    beQuite:false
});
```

ç¤ºä¾ 3ï¼è®¢éé¢éåé¢éåè¿ç«¯ç¨æ·ç Presence äºä»¶ï¼ä½ç¦æ­¢æ¬å°ç¨æ·åé Presence äºä»¶ï¼å³éèº«ç»é

```javascript
rtm.subscribe({
    msChannel: 'my_channel',
    withPresence: true,
    beQuite:true
});
```

å¦æè¿ç«¯ç¨æ·è®¢é `my_channel` é¢éï¼ä½ ä¼æ¶å° `join` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·åæ¶è®¢é `my_channel` é¢éï¼ä½ ä¼æ¶å° `leave` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·å `my_channel` é¢éçè¿æ¥è¶æ¶ï¼ä½ ä¼æ¶å° `timeout` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·å¨ `my_channel` é¢éä¸­çèªå®ä¹ç¶æåçæ¹åï¼ä½ ä¼æ¶å° `state-change` äºä»¶ï¼

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

å¦æä½ è®¾ç½®äºå®æ¶éç¥æ¨¡å¼ï¼å½è¿ç«¯ç¨æ·å¨è®¾ç½®çæ¶é´æ®µååçç¶æååæ¶ï¼ä½ ä¼æ¶å° `interval` äºä»¶ï¼

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

ç¤ºä¾ 4ï¼å¸¦ç¨æ·èªå®ä¹ç¶æçè®¢éï¼

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

**API æè¿°**

```javascript
rtm.unsubscribe({
    string msChannel
}): Promise<UnsubscribeResponse>;
```

å¦æä½ æ éå³æ³¨æä¸ªé¢éï¼è°ç¨ `unsubscribe` æ¹æ³åæ¶è®¢éè¯¥é¢éãæ¯æ¬¡è°ç¨è¯¥æ¹æ³åªè½åæ¶è®¢éä¸ä¸ªé¢éï¼å¦éåæ¶è®¢éå¤ä¸ªé¢éï¼ä½ éè¦å¤æ¬¡è°ç¨è¯¥æ¹æ³ã

æååæ¶è®¢éè¯¥é¢éåï¼å¶ä»è®¢éè¯¥é¢éçè¿ç«¯ç¨æ·ä¼æ¶å° `leave` äºä»¶ã

**åæ°ä»ç»**

|      åæ°     |   ç±»å   | æ¯å¦å¿å¡« | é»è®¤å¼ | æè¿°                                                                                                 |
| :---------: | :----: | :--: | :-: | -------------------------------------------------------------------------------------------------- |
| `msChannel` | String |  å¿å¡«  |  æ   | <p>æå®è®¢éçé¢éåç§°ãå¦éè®¢éå¤ä¸ªé¢éï¼å°é¢éåç§°ä»¥å­ç¬¦ä¸²æ°ç»çæ¹å¼å¡«å¥è¯¥åæ°ã</p><p><strong>Note</strong>: åä¸ªå®¢æ·ç«¯æå¤å¯ä»¥åæ¶è®¢é 100 ä¸ªé¢éã</p> |

**åºæ¬ç¨æ³**

```javascript
rtm.unsubscribe({msChannel: "chats.room1"}): Promise<UnsubscribeResponse>;
```

## Create Stream Channel

**API æè¿°**

```javascript
createStreamChannel({String stChannel}):Promise<CreateStreamChannelResponse>;
```

å¨ä½¿ç¨ Stream Channel ä¹åï¼ä½ éè¦åè°ç¨ `createStreamChannel` æ¹æ³åå»º `StreamChannel` å®ä¾ãæååå»º `StreamChannel` å®ä¾åï¼ä½ å¯ä»¥è°ç¨ Stream Channel ç¸å³æ¹æ³è¿è¡å å¥é¢éãç¦»å¼é¢éãéæ¯é¢éãåéæ¶æ¯ãè®¢éæ¶æ¯ç­æä½ã

æ³¨æï¼ æ¯æ¬¡è°ç¨è¯¥æ¹æ³åªè½åå»ºä¸ä¸ª `StreamChannel` å®ä¾ï¼å¦éåå»ºå¤ä¸ª `StreamChannel` å®ä¾ï¼ä½ éè¦å¤æ¬¡è°ç¨è¯¥æ¹æ³ã

**åæ°ä»ç»**

|      åæ°     |   ç±»å   | æ¯å¦å¿å¡« | é»è®¤å¼ |   æè¿°  |
| :---------: | :----: | :--: | :-: | :---: |
| `stChannel` | String |  å¿å¡«  |  æ   | é¢éåç§°ã |

**åºæ¬ç¨æ³**

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

**API æè¿°**

```javascript
rtm.streamChannel.join({Boolean withPresence, Boolean beQuite, Boolean withMetaData })
```

æååå»º Stream Channel åï¼ä½ å¯ä»¥è°ç¨ `join` æ¹æ³å å¥ Stream Channelã

æ³¨æï¼

* æ¯æ¬¡è°ç¨è¯¥æ¹æ³åªè½å å¥ä¸ä¸ªé¢éï¼å¦éå å¥å¤ä¸ªé¢éï¼ä½ éè¦å¤æ¬¡è°ç¨è¯¥æ¹æ³ãåä¸ªå®¢æ·ç«¯å¯ä»¥åæ¶å å¥æå¤ 100 ä¸ªé¢éã
* ç¨æ·éè¦æ¶å°æåå å¥é¢éç `onJoinResult` åè°æè½ç»§ç»­è¿è¡é¢éç¸å³çæä½ã

**åæ°ä»ç»**

|       åæ°       |    ç±»å   | æ¯å¦å¿å¡« |  é»è®¤å¼  | æè¿°                                                                                 |
| :------------: | :-----: | :--: | :---: | ---------------------------------------------------------------------------------- |
| `withPresence` | Boolean |  éå¡«  |  true | <p>æ¯å¦è®¢éè¿ç«¯ç¨æ·ç Presence äºä»¶ï¼</p><ul><li>trueï¼è®¢é</li><li>falseï¼ä¸è®¢é</li></ul>           |
|    `beQuite`   | Boolean |  éå¡«  | false | <p>æ¯å¦ç¦æ­¢æ¬å°ç¨æ·åé Presence äºä»¶ï¼</p><ul><li>trueï¼ç¦æ­¢</li></ul><ul><li>falseï¼ä¸ç¦æ­¢</li></ul> |
| `withMetaData` | Boolean |  éå¡«  |  true | <p>æ¯å¦è®¢éé¢éå±æ§ï¼</p><ul><li>trueï¼è®¢é</li></ul><ul><li>falseï¼ä¸è®¢é</li></ul>               |

**åºæ¬ç¨æ³**

ç¤ºä¾ 1ï¼ä»å å¥é¢é

```javascript
Loc_stChannel.join();
```

ç¤ºä¾ 2ï¼å å¥é¢éå¹¶è®¢éé¢éåè¿ç«¯ç¨æ·ç Presence äºä»¶

```javascript
Loc_stChannel.join({
    withPresence: true,
    beQuite:false
});
```

ç¤ºä¾ 3ï¼å å¥é¢éå¹¶è®¢éé¢éåè¿ç«¯ç¨æ·ç Presence äºä»¶ï¼ä½ç¦æ­¢æ¬å°ç¨æ·åé Presence äºä»¶ï¼å³éèº«ç»é

```javascript
Loc_stChannel.join({
    withPresence: true,
    beQuite:true
});
```

å¦æè¿ç«¯ç¨æ·è®¢é `my_channel` é¢éï¼ä½ ä¼æ¶å° `join` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·åæ¶è®¢é `my_channel` é¢éï¼ä½ ä¼æ¶å° `leave` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·å `my_channel` é¢éçè¿æ¥è¶æ¶ï¼ä½ ä¼æ¶å° `timeout` äºä»¶ï¼

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

å¦æè¿ç«¯ç¨æ·å¨ `my_channel` é¢éä¸­çèªå®ä¹ç¶æåçæ¹åï¼ä½ ä¼æ¶å° `state-change` äºä»¶ï¼

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

å¦æä½ è®¾ç½®äºå®æ¶éç¥æ¨¡å¼ï¼å½è¿ç«¯ç¨æ·å¨è®¾ç½®çæ¶é´æ®µååçç¶æååæ¶ï¼ä½ ä¼æ¶å° `interval` äºä»¶ï¼

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

ç¤ºä¾ 4ï¼å¸¦ç¨æ·èªå®ä¹ç¶æçå å¥

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

**API æè¿°**

```javascript
rtm.streamChannel.leave():Promise<LeaveStreamChannelResponse>;
```

å¦æä½ ä¸åéè¦å¾å¨æä¸ªé¢éä¸­ï¼ä½ å¯ä»¥è°ç¨ `leave` ç¦»å¼è¯¥é¢éãSDK ä¼è§¦åå¦ä¸äºä»¶ï¼

* è°ç¨è¯¥æ¹æ³åï¼SDK ä¼è§¦å `onLeaveResult` åè°ã
* æåç¦»å¼é¢éåï¼é¢éä¸­çè¿ç«¯ç¨æ·ä¼æ¶å° `leave` äºä»¶ã

**åºæ¬ç¨æ³**

```javascript
try{
    const result = await rtm.leave();
    console.log("leave Stream Channel w/ server response: ", response);


} catch (status){
    console.log("leave Stream Channel failed w/ status: ", status);
}
```

## Destroy Stream Channel

**API æè¿°**

```javascript
rtm.streamChannel.release():Promise<ReleaseStreamChannelResponse>;
```

å¦æä½ ä¸åéè¦æä¸ªé¢éï¼ä½ å¯ä»¥è°ç¨ `release` æ¹æ³éæ¯å¯¹åºç `StreamChannel` å®ä¾ä»¥éæ¾èµæºãè°ç¨è¯¥æ¹æ³éæ¯ `StreamChannel` å®ä¾ä¸ä¼éæ¯è¯¥é¢éï¼åç»­å¯éè¿åæ¬¡è°ç¨ `createStreamChannel` å `join` éæ°å å¥è¯¥é¢éã

æ³¨æï¼ å¦æä¸åè°ç¨ `leave` ç¦»å¼é¢éèç´æ¥è°ç¨ `release` éæ¯é¢éå®ä¾ï¼SDK ä¼èªå¨è°ç¨ `leave` å¹¶è§¦åå¯¹åºçäºä»¶ã

**åºæ¬ç¨æ³**

```javascript
try{
    const result = await Loc_stChannel.release();
    console.log("release Stream Channel w/ server response: ", response);


} catch(status) {
    console.log("release Stream Channel failed w/ status: ", status);
}
```
