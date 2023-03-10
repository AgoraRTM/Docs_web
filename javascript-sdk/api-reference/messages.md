# ð· Messages

RTM SDK åè®¸å®¢æ·ç«¯ä½¿ç¨æ¶æ¯ä¼ éå®æ¶ä¿¡æ¯ï¼åå«å®¢æ·ç«¯å¸æéä¿¡çæ°æ®ã

RTM SDK æä¸¤ç§é¢éç±»åï¼åå«æ¯ Message Channel å Stream Channelãä¸¤ç§é¢éä¸­æ¶æ¯çä¼ éæ¹å¼åéè¦è°ç¨çæ¹æ³ä¸åï¼åºå«å¦ä¸ï¼

* Message Channelï¼å®æ¶é¢éï¼æ¶æ¯éè¿é¢éä¼ éï¼å¯æ©å±æ§å¼ºãæ¬å°ç¨æ·å¯ä»¥è°ç¨ `publish` æ¹æ³å¨é¢éä¸­åéæ¶æ¯ï¼è¿ç«¯ç¨æ·å¯ä»¥è°ç¨ `subscribe` æ¹æ³è®¢éé¢éå¹¶æ¥æ¶æ¶æ¯ã
* Stream Channelï¼æµä¼ è¾é¢éï¼ç¨æ·éè¦åå å¥é¢éï¼ç¶ååå»º Topicï¼æ¶æ¯éè¿ Topic ä¼ éãæ¬å°ç¨æ·å¯ä»¥è°ç¨ `publishTopicMessage` æ¹æ³å¨ Topic ä¸­åéæ¶æ¯ï¼è¿ç«¯ç¨æ·å¯ä»¥è°ç¨ `subscribeTopic` æ¹æ³è®¢é Topic å¹¶æ¥æ¶æ¶æ¯ãè¯¦è§ Topicã

æ¬æä»ç»å¦ä½å¨ Message Channel ä¸­æ¶åæ¶æ¯ã

### Publish message

#### æ¥å£æè¿°

RTM SDK çåºç¡æ¯åéæ¶æ¯çè½åï¼ä½ å¯ä»¥éæ¶éå°åé¢éä¸­åéæ¶æ¯ï¼æ¶æ¯ä¼å¨ 100 æ¯«ç§åä¼ éå°ä»»ä½å°æ¹ãä½ å¯ä»¥ç´æ¥è°ç¨ `publish` æ¹æ³åé¢éçææè®¢éèåéæ¶æ¯ãå³ä½¿æ²¡æè®¢éé¢éï¼ä¹è½å¨é¢éä¸­åéæ¶æ¯ã

æ³¨æï¼

* è°ç¨è¯¥æ¹æ³åï¼ä½ éè¦å¨åå§åæ¶å° `ssl` åæ°è®¾ç½®ä¸º `true`ï¼å¼å¯ SSL/TLS å å¯åè½ã
* ä»¥ä¸åæ³å¯æææåæ¶æ¯æ¶åçå¯é æ§ï¼
  * ä»¥ä¸²è¡çæ¹å¼åéæ¶æ¯ãæ¯æ¬¡åéååæ£æ¥ä¸ä¸æ¡æ¶æ¯æ¯å¦æååéï¼å¦ææåï¼ååéä¸ä¸æ¡æ¶æ¯ï¼å¦æä¸æåï¼åéåä¸ä¸æ¡æ¶æ¯ã
  * æ¶æ¯è´è½½éå¶å¨ 1 KB ä»¥åï¼å¦ååéä¼å¤±è´¥ã
  * åä¸ªå®¢æ·ç«¯ååä¸ªé¢éåéæ¶æ¯çéçä¸éä¸º 60 QPSãå¦æåééçè¶éï¼å°ä¼æé¨åæ¶æ¯è¢«ä¸¢å¼ãå¨æ»¡è¶³è¦æ±çæåµä¸ï¼éçè¶ä½è¶å¥½ã
* RTM ä¸ä¿è¯ææè®¢éèæ¥æ¶å°æ¶æ¯çé¡ºåºä¸åéèåéæ¶æ¯çé¡ºåºä¿æä¸è´ãå¦æä½ æ³è¦ä¿éæ¶æ¯æåºæ§ï¼å£°ç½å»ºè®®ä½ å¨æ¶æ¯è´è½½ä¸­èªå®ä¹æ¶æ¯åºå·ã

#### æ¥å£æ¹æ³

ä½ å¯ä»¥éè¿ä»¥ä¸æ¹å¼è°ç¨ `publish` æ¹æ³ï¼

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
|  `message`  |  Object  |    Yes   |    /    | æ¶æ¯è´è½½ãæ¯æä»»æ JSON å¯åºååæ°æ®ï¼åå«å¯¹è±¡ãæ°ç»ãæ´æ°åå­ç¬¦ä¸²ã |
| `msChannel` |  String  |    Yes   |    /    |     æå®æ¶æ¯åéçé¢éåç§°ãåä¸æ¶é´ä»æ¯æåä¸ä¸ªé¢éåéæ¶æ¯ã     |
|    `meta`   |  Object  | Optional |    /    |                  åæ°æ®ã                 |
|  `callback` | Function | Optional |    /    |                 åè°å½æ°ã                 |

#### åºæ¬ç¨æ³

ç¤ºä¾ 1ï¼åæå®é¢éåé String æ¶æ¯

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

ç¤ºä¾ 2ï¼åæå®é¢éåé JSON åºååæ¶æ¯

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

#### è¿åå¼

```javascript
[200,[1,"Sent","14375189629170609"]]
```

### Receive message

RTM æä¾æ¶æ¯ãç¶æãäºä»¶åæ´ç­ä¿¡æ¯çäºä»¶éç¥ãéè¿è®¾ç½® Event Listenerï¼ä½ å¯ä»¥æ¥æ¶å·²è®¢éé¢éä¸­çæ¶æ¯ã

å³äºå¦ä½æ·»å åè®¾ç½® Listenerï¼å¯ä»¥åè Event Listeners ç« èã
