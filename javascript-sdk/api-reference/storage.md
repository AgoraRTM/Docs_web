# 🎄 Storage

## Storage

RTM的Storage功能提供了一套动态数据库机制，可以让开发者动态设置、存储、更新、删除频道属性、用户属性等数据。

### Set Channel Metadata

#### 接口描述

当特定频道的频道属性发生变更（设置、更新、删除等）RTM Storage 服务会触发对应事件通知，订阅此事件的用户可以实时接收到此事件通知并更新自己的业务逻辑。用户可以提供任何合法的一组或多组属性进行设置，每组属性都包含有预定义字段每个字段都是一组键值对。

利用setChannelMetadata() API 接口对频道属性进行设置，如果频道之前没有属性，则自动创建频道属性。如果频道之前已有属性，将会根据需要设置的每组属性中"name"字段进行检索，如果原本频道属性中有"name"属性组则覆盖，如果没有则追加。

当设置成功后RTM SDK会返回一个200状态码和一组包含有操作状态的Response 数组，同时RTM SDK还会触发一个Storage -> channelMetaData -> set事件通知此频道的订阅者。

#### 接口方法

你可以通过以下方式调用`setChannelMetadata()`接口：

```js
rtm.Storage.setChannelMetadata({String channel, Number channelType, Array data, Object Options},function callback)
```

|  Parameters  |      Type      | Required | Default | Description                                                |
| :----------: | :------------: | :------: | :-----: | ---------------------------------------------------------- |
|    channel   |     String     |    Yes   |         | 频道名称                                                       |
|  channelType |     Number     |    Yes   |         | 频道类型，1 表示 nmChannel，2 表示 itChannel.                        |
|     data     |      Array     |    Yes   |         | 需要设置的属性数组。                                                 |
|     name     |     String     |    Yes   |         | 属性名称                                                       |
|     value    |   JSON Object  |    Yes   |         | 属性值                                                        |
|   revision   |     Number     | Optional |    -1   | -1表示此属性可竞争读写最后更新者得，0表示此属性只有在不存在时才创建，正数表示只有版本号匹配时此属性才能被修改成功 |
|    Options   |     Object     | Optional |         | 可选配置项                                                      |
|   lockName   |     String     | Optional |         | 分布式锁名称，如果设置，只有获得此锁的用户才有权限进行此操作。                            |
| addTimeStamp |      Bool      | Optional |  false  | 是否在每个属性中添加服务器时间戳。                                          |
|    addUid    |    Optional    | Optional |  false  | 是否在每个属性中添加编辑者UID。                                          |
|   callback   | (result)=>bool | Optional |         | 回调函数                                                       |

#### 基本用法

设置频道属性

```js
rtm.Storage.setChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "data"    : [
            {
                "name":"Cat_location",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            },
            {
                "name":"Doge_location",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            },
            {
                "name":"Chicken_location",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            }
            ],
    "Options" :{
            "revision":174298270,
            "addTimeStamp":ture，
            "addUid":true,
            includeTotalCount:true
            }},
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

```js
{
    "status": 200,                    // API 接口调用成功
    "response":{
            "opCode": 0,              // 数据操作成功
            "totalCount": 37          // TotalCount 37，当年频道属性包含37个属性组
            }
}

```

### Get Channel Metadata

#### 接口描述

用户可以通过 getChannelMetadata() API 接口获取指定频道的属性，在获取频道属性时可以通过 filter 字段设置过滤条件从而只获取自己关系的属性组。当获取成功后会返回200状态码和一组包含有操作状态和属性数据的Respose数组。

#### 接口方法

你可以通过以下方式调用`getChannelMetadata()`接口：

```js
rtm.Storage.getChannelMetadata({String channel, Number channelType, String filter, Object Options},function callback)
```

|     Parameters    |      Type      | Required | Default | Description                         |
| :---------------: | :------------: | :------: | :-----: | ----------------------------------- |
|      channel      |     String     |    Yes   |         | 频道名称                                |
|    channelType    |     Number     |    Yes   |         | 频道类型，1 表示 nmChannel，2 表示 itChannel. |
|       filter      |     String     | Optional |         | 对返回结果进行过滤，只返回符合条件的属性组。              |
|      Options      |     object     | Optional |         | 返回结果中是否包含附加信息                       |
|  includeTimeStamp |      Bool      | Optional |   true  | 每个属性组是否包含更新时间信息                     |
|     includeUid    |      Bool      | Optional |   true  | 每个属性组是否包含最近修改者UID                   |
| includeTotalCount |      Bool      | Optional |  false  | 是否包含当前频道属性中属性组个数信息                  |
|      callback     | (result)=>bool | Optional |         | 回调函数                                |

#### 基本用法

获取频道全部属性

```js
rtm.Storage.getChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1
    },
    function(Status,response){
        // handle status, response
    }
);
```

获取频道部分属性

```js
rtm.Storage.getChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "filter"  : "name == "cat*",
    "options" : {
              "includeTotalCount":true
                    }
     
    },
    function(Status,response){
        // handle status, response
    }
);
```

获取无效频道属性

```js
rtm.Storage.getChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "filter"  : "name == "boy""
    },
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

获取频道属性返回值

```js
{
    "status": 200,
    "response":{
            "opCode": 0,
            "revison":174298270,         //7月4日月研发团队达成一致意见将Document revision 移动至“data” 外部。
            "data":{
 
 
             "Cat_location":             //7月4日月研发团队达成一致意见将返回数据结构进行修改，将"data" 字段由原本的Array修改成Object，同时将原本每个属性的"name" 提取出来，方便用户获得数据后操作。
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                },
 
 
             "Doge_location":
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                },
 
 
             "Chicken_location":
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                }
                    }
            }
}
```

获取部分频道属性返回值

```js
rtm.Storage.getChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "filter"  : "name == "cat*",
    "options" : {
              "includeTotalCount":true
                    }
     
    },
    function(Status,response){
        // handle status, response
    }
);  
```

获取无效频道属性返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
        "opCode":404
                }  // 需要获取的属性不存在
}
```

### Remove Channel Metadata

#### 接口描述

用户可以通过 removeChannelMetadata() 对频道属性或子属性组进行删除。用户可以删除 “name” 数组中指定属性组，也可以省略“name” 字段，从而删除整个频道属性。当删除操作完成后，RTM SDK会发送一个Storage -> channelMetaData -> delete事件，所有订阅频道此频道的用户会收到此时间通知。

#### 接口方法

你可以通过以下方式调用`removeChannelMetadata()`接口：

```js
rtm.Storage.removeChannelMetadata({String channel, Number channelType, Array name, Object Options},function callback)
```

|     Parameters    |      Type      | Required | Default | Description                         |
| :---------------: | :------------: | :------: | :-----: | ----------------------------------- |
|      channel      |     String     |    Yes   |         | 频道名称                                |
|    channelType    |     Number     |    Yes   |         | 频道类型，1 表示 nmChannel，2 表示 itChannel. |
|        name       |      Array     | Optional |         | 需要删除的键值，如果不设定，删除频道所有数据              |
|      Options      |     object     | Optional |         | 返回结果中是否包含附加信息                       |
| includeTotalCount |      Bool      | Optional |  false  | 是否包含当前频道属性中属性组个数信息                  |
|      callback     | (result)=>bool | Optional |         | 回调函数                                |

#### 基本用法

删除频道中指定的属性组

```js
rtm.Storage.removeChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "name": ["cat_location","Doge_location"],
    "options" : {
              "includeTotalCount":true     
                    }  
    },
    function(Status,response){
        // handle status, response
    }
);

```

删除频道所有属性

```js
rtm.Storage.removeChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "options" : {
              "includeTotalCount":true     
                    }
    },
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

删除频道属性返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
            "opCode":0,       // 数据返回成功
            "totalCount":35,          // 频道属性 TotalCount 为35，此频道包含35个属性组
            }
}
```

删除频道所有属性返回值

```js
{
    "status": 200,                // API接口调用成功
    "response":{
            "opCode":0,           // 数据删除成功
            "totalCount":0        // 频道属性 TotalCount 为0，此频道包含0个属性组
            }
}
```

### Update Channel Metedata

#### 接口描述

利用 updateChannelMetadata() API 接口，用户可以对频道属性中已有的属性组进行更新，用户只能更新已存在的属性组，否则会报错。当更新操作完成后，RTM SDK会发送一个Storage -> channelMetaData -> update事件，所有订阅频道此频道的用户会收到此事件通知。

#### 接口方法

你可以通过以下方式调用`updateChannelMetadata()`接口：

```js
rtm.Storage.updateChannelMetadata({String channel, Array data, Object Options},function callback)
```

|     Parameters    |      Type      | Required | Default | Description                                                                      |
| :---------------: | :------------: | :------: | :-----: | -------------------------------------------------------------------------------- |
|      channel      |     String     |    Yes   |         | 频道名称                                                                             |
|    channelType    |     Number     |    Yes   |         | 频道类型，1 表示 nmChannel，2 表示 itChannel.                                              |
|        data       |      Array     |    Yes   |         | 需要更新的属性组数据。                                                                      |
|        name       |     String     |    Yes   |         | 需要更新的属性名称。                                                                       |
|       value       |     Object     |    Yes   |         | 需要更新的属性值。                                                                        |
|      Options      |     object     | Optional |         | 可选配置项                                                                            |
|      revision     |     Number     | Optional |         | 属性的Revision，当目标属性的 revision 等于-1，则此项可忽略。当目标属性的 revision 是正数时，此值只有和目标值一致，数据才会被更新。 |
|      lockName     |     String     | Optional |         | 分布式锁名称，如果设置，只有获得此锁的用户才有权限进行此操作。                                                  |
|    addTimeStamp   |      Bool      | Optional |         | 更新数据时，是否更新对应属性组的时间戳。                                                             |
|       addUid      |      Bool      | Optional |   true  | 更新数据时，是否在对应属性组中添加更新者UID。                                                         |
| includeTotalCount |      Bool      | Optional |  false  | 是否在Response中包含频道属性里属性组数量信息。                                                      |
|      callback     | (result)=>bool | Optional |         | 回调函数                                                                             |

#### 基本用法

更新频道属性

```js
rtm.Storage.updateChannelMetadata({
    "channel" : "Chatroom",
    "channelType":1,
    "data"    : [
            {
                "name":"Cat_location",
                "value":{
                        "x":200,
                        "y":100,
                        "z":500
                        }
            },
            {
                "name":"Doge_location",
                "description":"The Doge pet locations",
                "value":{
                        "x":800,
                        "y":10,
                        "z":800
                        },
                "revision":174298270
            }
            ],
    "Options" :{
            "revision":174298270,
            "addTimeStamp":true,
            "lockName":"leader",
            "addUid":true,
            includeTotalCount:true
            }},
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

更新频道属性成功返回值

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 0,        // 数据操作成功
            "totalCount": 37,         // TotalCount 37，当前频道属性包含37个属性组
            }
}
```

更新不存在的频道属性返回值

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 404,        // 有一组或多组属性不存在
            "totalCount": 36,         // TotalCount 37，当前频道属性包含37个属性组
            }
}

```

频道全局Revision不匹配

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 405,            // 全局属性Revision不匹配
            "totalCount": 36,         // TotalCount 37，当前频道属性包含37个属性组
            }
}
```

属性组Revision不匹配

```js
{
    "status": 200,                    // API 接口调用成功
    "response":{
            "opCode": 406,            // 一组或多组Revision不匹配
            "totalCount": 36,         // TotalCount 37，当前频道属性包含37个属性组
            }
}
```

```js
{
    "status": 200,                // API 接口调用成功
    "response":{
            "opCode": 403,        // LockName 不对，无权限操作
            "totalCount": 36,         // TotalCount 37，当前频道属性包含37个属性组
            }
}
```

### 订阅频道属性事件通知

#### 接口描述

频道属性变更（设置、更新、删除）后，RTM 会发送对应的Storage事件通知。

#### 接口方法

用户如果需要收到实时通知提醒，需要保证完成以下几步：

**1.开发者在声网 Console 后台需要开启Storage频道属性事件通知功能，如下所示：**

console上面storage字段解释如下：

|       字段      |  类型  |                                     解释                                    |
| :-----------: | :--: | :-----------------------------------------------------------------------: |
|    Storage    | Bool |                    应用中是否开始 Storage 存储功能，如果开启，则会产生对应的费用。                   |
|     选择存储地区    |  单选  |                 选择 Storage 服务所在区域，区域有：中国、东南亚、日韩新加坡、北美、欧洲等                 |
|    用户属性事件通知   | Bool |                                是否开启用户属性事件通知                               |
|    频道属性事件通知   | Bool |                                是否开启频道属性事件通知                               |
|  用户属性Deltas更新 | Bool | 是否开启用户属性事件 Delta 更新，如果开启，则每次收到的用户属性变更事件中的data数据为 delta 更新数据，否则是用户属性的全量数据。 |
|  频道属性Deltas更新 | Bool | 是否开启频道属性事件 Delta 更新，如果开启，则每次收到的频道属性变更事件中的data数据为 delta 更新数据，否则是频道属性的全量数据。 |
| 禁止获取所有用户属性API | Bool |                       是否禁用 getAllUserMetadata() API                       |
| 禁止获取所有频道属性API | Bool |                      是否禁用 getAllChannelMetadata() API                     |

**2.用户需要设置Event Handler，开启事件监听，如下所示：**

Node.js开启Storage事件监听

```js
rtm.onEvent({
    storage: (storageEvent) => {
        var channelType = m.channelType; //Which channel type, 1 for nmChannel or 2 for itChannel
        var channelName = storageEvent.channel; // The channel
        var channelGroup = storageEvent.channelGroup; // The channel group
        var timetoken = storageEvent.timetoken; // The event timetoken
        var publisher = storageEvent.publisher; // The UID that triggered this event
        var event = storageEvent.event; // The event name that occurred
        var type = storageEvent.type; // The event type that occurred
        var data = storageEvent.data; // The event data that occurred
    },
 
});
```

C++开启Storage事件监听

```
class RTMEventSource {
 
 
    struct StorageEvent{
        int channelType,
        const char* channelName,
        const char* channelGroup,
        const char* timetoken ,
        const char* publisher ,
        const char* event ,
        const char* type ,
        const ChannelMetadata& data,
         
        };
 
 
 
 
    virtual void onStorageEvent( const StorageEvent& Event);
 
 
}
 
 
void RTMEventSource::onStorageEvent( const StorageEvent& Event){
         
        int channelType = Event.channelType;
        const char* channelName = Event.channelName;
        const char* channelGroup = Event.channelGroup;
        const char* timetoken = Event.timetoken;
        const char* publisher = Event.publisher;
        const char* event = Event.event ;
        const char* type = Event.type ;
        const ChannelMetadata& data = Event.data;
     
 
 
    }
```

频道属性事件的 storageEvent.event为channelMetaData, storageEvent.type根据用户的操作不同有set、remove、update三种不同的类型。 当用户订阅频道属性事件后，收到的 storageEvent 数据的 Object 字段格式如下：

```
{
    "channelType":1,
    "channelName": "my-Channel",
    "channelGroup": "my-channel-Group",
    "timetoken": "15119446002445794"
    "publisher": "Tony",
    "event": "channelMetaData",
    "type": "set",
    "data": {}
    }
```

|      字段      |                                  解释                                 |
| :----------: | :-----------------------------------------------------------------: |
|  channelType |                 频道类型，1 表示 nmChannel，2 表示 itChannel.                 |
|  channelName |                              此事件属于哪个频道                              |
| channelGroup |                              此事件属于哪个频道组                             |
|   timetoken  |                            此事件的TimeToken                            |
|   publisher  |                              事件发送者的UID                              |
|     event    |          事件名称：频道属性事件为 channelMetaData，用户属性事件为 userMetadata          |
|     type     |                        事件类型：set、remove、update                       |
|     data     | 事件对应的变更返回数据，根据Console中 Metadata-deltas设置，此项可以是频道属性的全量数据也可以是delta数据。 |

**3.用户需要Subscribe对应的频道，如下所示**

订阅频道属性API调用

```js
rtm.subscribe({
    channels: ['my_channel_1', 'my_channel_2', 'my_channel_3'],
    withMetadata: true                     //显式开启频道属性事件通知，定于此频道中所有频道属性事件。
});
```

完成上述几步后，终端用户就可以实时接收到Storage事件通知了。

### Set User Metadata

#### 接口描述

用户可以将自己的UserMetadata数据存储在 RTM 动态数据库中以增强客户端的使用体验，用户可以设置"name"、"value"这些预先定义好的属性字段，RTM中每个用户属性可以包含多个属性组，例如用户在不同的频道有不同的用户属性展现，则可以将在不同频道的属性保存在此，配合Presence中的State属性，实现在不同频道中有不同属性的展示功能。 当用户变更用户属性时，RTM会实时产生对应的用户属性变更事件，其他客户端可以实时接收到这些事件通知并对数据的变化做出动作。 每个用户的用户属性事件发布在以用户 UID 为前缀加 "-umd" 命名的频道中，例如，你需要接收 UID为 chat-user-9257 用户的用户属性变更事件，你需要首先 Subcribe 名为 "chat-user-9257-umd" 频道。 用户可以通过setUserMetadata() API 接口对用户属性进行设置，如果用户之前没有属性，则自动创建用户属性。如果用户之前已有属性，将会根据需要设置的每组属性中"name"字段进行检索，如果原本用户属性中有"name"属性组则覆盖，如果没有则追加。 当设置成功后 RTM SDK 会返回一个200状态码和一组包含有操作状态的Response数组，同时RTM SDK还会触发一个Storage -> userMetaData-> set事件通知此属性的订阅者。 在设置用户属性的时候，可以通过用户总Revision和每个属性组的Revision对数据的写入做CAS控制。Revision的值分成三种情况：

* \-1: 当 setUserMetadata() 将频道总Revision或每个属性组的Revision设置成 -1 时，所有用户都可以对对应属性进行更新操作，RTM Storage 保留最后一个用户写入的数据。
* 0: 当 setUserMetadata() 中频道总Revision或每个属性组的Revision设置成 0 时，此设置操作只有在当前频道属性或子属性组不存在的时候才会创建，并且创建后在数据库中自动更改成-1.此参数值只影响本次设置操作。
* 正数： 当 setUserMetadata() 将频道总Revision或每个属性组的Revision设置成正数时，只有匹配此版本号的更新操作才会被正确写入。

#### 接口方法

你可以通过以下方式调用`setUserMetadata()`接口：

```js
rtm.Storage.setUserMetadata({String uid, Array data,  Object Options},function callback)

```

|     Parameters    |      Type      | Required |   Default   | Description                                                         |
| :---------------: | :------------: | :------: | :---------: | ------------------------------------------------------------------- |
|        uid        |     String     | Optional | current uid | 用户uid,如果没有设置，默认是当前用的uid.                                            |
|        data       |      Array     |    Yes   |             | 需要设置的用户属性数组。                                                        |
|        name       |     String     |    Yes   |             | 属性名称                                                                |
|       value       |     Object     |    Yes   |             | 属性值                                                                 |
|      Options      |     object     | Optional |             | 可选配置项                                                               |
|      revision     |     Number     | Optional |             | 属性的版本号控制，-1表示此属性可竞争读写最后更新者得，0表示此属性只有在不存在时才创建，正数表示只有版本号匹配时此属性才能被修改成功 |
|    addTimeStamp   |      Bool      | Optional |    false    | 是否在每个属性中添加服务器时间戳。                                                   |
|       addUid      |      Bool      | Optional |     true    | 更新数据时，是否在对应属性组中添加更新者UID。                                            |
| includeTotalCount |      Bool      | Optional |    false    | 是否在Response中包含当前用户属性中属性组个数信息。                                       |
|      callback     | (result)=>bool | Optional |             | 回调函数                                                                |

#### 基本用法

设置用户属性

```js
rtm.Storage.setUserMetadata({
    "uid" : "Tony-9527",
    "data"    : [
            {
                "name":"Room1",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            },
            {
                "name":"Doge_location",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            },
            {
                "name":"Chicken_location",
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270
            }
            ],
    "Options" :{
            "revision":174298270,
            "addTimeStamp":ture,
            "includeTotalCount":true
            }},
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

设置用户属性返回值

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 0,        // 数据操作成功
            "totalCount": 37,   // TotalCount 37，当前频道属性包含37个属性组
            }
}
```

### Get User Metadata

#### 接口描述

用户可以通过 getUserMetadata() API 接口获取指定用户的属性，在获取频道属性时可以通过 filter 字段设置过滤条件从而只获取自己关心的属性组。当获取成功后会返回200状态码和一组包含有操作状态和属性数据的Respose数组。

#### 接口方法

你可以通过以下方式调用`getUserMetadata()`接口：

```js
rtm.Storage.getUserMetadata({String uid, String filter, Object Options},function callback)

```

|     Parameters    |      Type      | Required |   Default   | Description              |
| :---------------: | :------------: | :------: | :---------: | ------------------------ |
|        uid        |     String     | Optional | current uid | 用户uid,如果没有设置，默认是当前用的uid. |
|       filter      |     String     | Optional |             | 对返回结果进行过滤，只返回符合条件的属性组。   |
|      Options      |     object     | Optional |             | 可选配置项                    |
|  includeTimeStamp |      Bool      | Optional |    false    | 每个属性组是否包含更新时间信息。         |
|     includeUid    |      Bool      | Optional |     true    | 更新数据时，是否在对应属性组中添加更新者UID。 |
| includeTotalCount |      Bool      | Optional |    false    | 是否包含当前频道属性中属性组个数信息       |
|      callback     | (result)=>bool | Optional |             | 回调函数                     |

#### 基本用法

获取用户全部属性

```js
rtm.Storage.getUserMetadata({
    "uid" : "Tony-9527"
    },
    function(Status,response){
        // handle status, response
    }
);
 
 
// using UUID from the config
rtm.Storage.getUserMetadata();
```

获取用户部分属性

```js
rtm.Storage.getUserMetadata({
    "channel" : "Chatroom",
    "filter"  : "name == "room1"",
    "options" : {
              "includeTotalCount":true
                    }
     
    },
    function(Status,response){
        // handle status, response
    }
);
```

获取无效用户属性

```js
rtm.Storage.getUserMetadata({
    "channel" : "Chatroom",
    "filter"  : "name == "Room4""
    },
    function(Status,response){
        // handle status, response
    }
); 
```

#### 返回值

获取用户全部属性返回值

```js
{
    "status": 200,
    "response":{
            "opCode": 0,
            "revison":174298270,    //根据7月4日与研发团队达成的一致意见，将返回数据格式做适当调整，将Doccument Revision提到外面
            "data":{
                "Room1":            //7月4日月研发团队达成一致意见将返回数据结构进行修改，将"data" 字段由原本的Array修改成Object，同时将原本每个属性的"name" 提取出来，方便用户获得数据后操作。
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                },
 
                "Room2":
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                },
 
 
                "Room3":
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
                }}
            }
}
```

获取用户部分属性返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
            "opCode":0,       // 数据返回成功
            "totalCount":37,          // 频道属性 TotalCount 为37，此频道包含37个属性组
            "revison":174298270
            "data": {
                "Room1":
                {
                "value":{
                        "x":100,
                        "y":10,
                        "z":500
                        },
                "revision":174298270,
                "updated":"2022-05-20T23:11:20.893755",
                "AuthorUid":"Tony"
            }}
            }
}
```

获取无效用户属性返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
        "opCode":404
                }  // 需要获取的属性不存在
}
```

### Remove User Metadata

#### 接口描述

用户可以通过 removeUserMetadata() 对用户属性或子属性组进行删除。用户可以删除 “name” 数组中指定属性组，也可以省略“name” 字段，从而删除整个用户属性。当删除操作完成后，RTM SDK会发送一个Storage -> UserMetaData -> delete事件，所有订阅频道此频道的用户会收到此事件通知。

#### 接口方法

你可以通过以下方式调用`removeUserMetadata()`接口：

```js
rtm.Storage.removeUserMetadata({String uid, Array name, String filter, Object Options},function callback)

```

|     Parameters    |      Type      | Required |   Default   | Description              |
| :---------------: | :------------: | :------: | :---------: | ------------------------ |
|        uid        |     String     | Optional | current uid | 用户uid,如果没有设置，默认是当前用的uid. |
|        name       |      Array     |  Option  |             | 要 删除的键值，如果不设定，删除频道所有数据   |
|      Options      |     object     | Optional |             | 可选配置项                    |
| includeTotalCount |      Bool      | Optional |    false    | 是否包含当前频道属性中属性组个数信息       |
|      callback     | (result)=>void | Optional |             | 回调函数                     |

#### 基本用法

删除用户属性中指定的属性组

```js
rtm.Storage.removeUserMetadata({
    "uid" : "Chatroom",
    "name": ["Room1","Room2"],
    "options" : {
              "includeTotalCount":true     
                    }
     
    },
    function(Status,response){
        // handle status, response
    }
);
```

删除用户属性中所有属性

```js
rtm.Storage.removeChannelMetadata({
    "uid" : "Tony-9527",   
    "options" : {
              "includeTotalCount":true     
                    }
    },
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

删除用户属性中指定的属性组返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
            "opCode":0,       // 数据返回成功
            "totalCount":32,          // 频道属性 TotalCount 为35，此频道包含35个属性组
            }
}
```

删除用户属性中所有属性返回值

```js
{
    "status": 200,    // API接口调用成功
    "response":{
            "opCode":0,       // 数据删除成功
            "totalCount":0    // 频道属性 TotalCount 为0，此频道包含0个属性组
            }
}
```

### Update User Metedata

#### 接口描述

利用 updateUserMetadata() API 接口，用户可以对用户属性中已有的属性组进行更新，用户只能更新已存在的属性组，否则会报错。当更新操作完成后，RTM SDK会发送一个Storage -> UserMetaData -> update事件，所有订阅频道此频道的用户会收到此事件通知。用户一次可以对一个或者多个属性组进行更新，在更新时可以使用属性全局Revision或属性组Revision对数据更新时间进行CAS控制，用户属性全局Revision和属性组Revision会有以下几种情况：

* \-1: 如果原用户属性的全局Revision或属性组Revision是-1，则用户可以随意对数据进行更新，多人同时更新时，最后一次更新有效。
* 正数：如果原用户属性的全局Revision或属性组Revision是正数，则只有匹配当前版本号的修改才会成功。

#### 接口方法

你可以通过以下方式调用`updateUserMetadata()`接口：

```js
rtm.Storage.updateUserMetadata({String uid, Array data,String filter, Object Options},function callback

```

|     Parameters    |      Type      | Required |   Default   | Description                                                         |
| :---------------: | :------------: | :------: | :---------: | ------------------------------------------------------------------- |
|        uid        |     String     | Optional | current uid | 用户uid,如果没有设置，默认是当前用的uid.                                            |
|        data       |      Array     |    Yes   |             | 需要设置的用户属性数组。                                                        |
|        name       |     String     |    Yes   |             | 属性名称                                                                |
|       value       |     Object     |    Yes   |             | 需要更新的属性值                                                            |
|      Options      |     object     | Optional |             | 可选配置项                                                               |
|       filter      |     String     | Optional |             | 对返回结果进行过滤，只返回符合条件的属性组。                                              |
|      revision     |     Number     | Optional |             | 属性的版本号控制，-1表示此属性可竞争读写最后更新者得，0表示此属性只有在不存在时才创建，正数表示只有版本号匹配时此属性才能被修改成功 |
|    addTimeStamp   |      Bool      | Optional |    false    | 是否在每个属性中添加服务器时间戳。                                                   |
|       addUid      |      Bool      | Optional |     true    | 更新数据时，是否在对应属性组中添加更新者UID。                                            |
| includeTotalCount |      Bool      | Optional |    false    | 是否在Response中包含当前用户属性中属性组个数信息。                                       |
|      callback     | (result)=>bool | Optional |             | 回调函数                                                                |

#### 基本用法

更新用户属性

```js
rtm.Storage.updateUserMetadata({
    "uid" : "Tony-9527",
    "data"    : [
            {
                "name":"Room1",
                "value":{
                        "x":200,
                        "y":100,
                        "z":500
                        }
            },
            {
                "name":"Room2",
                "description":"Room2 locations",
                "value":{
                        "x":800,
                        "y":10,
                        "z":800
                        },
                "revision":174298270
            }
            ],
    "Options" :{
            "revision":174298270,
            "addTimeStamp":ture,
            "addUid":true,
            includeTotalCount:true
            }},
    function(Status,response){
        // handle status, response
    }
);
```

#### 返回值

更新成功返回值

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 0,        // 数据操作成功
            "totalCount": 37          // TotalCount 37，当前用户属性包含37个属性组
            }
}   
```

属性不存在

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 404,        // 有一组或多组属性不存在
            "totalCount": 36          // TotalCount 37，当前用户属性包含37个属性组
            }
}
```

用户全局Revision不匹配

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 405,            // 全局属性Revision不匹配
            "totalCount": 36          // TotalCount 37，当前用户属性包含37个属性组
            }
}
```

属性组Revision不匹配

```js
{
    "status": 200,    // API 接口调用成功
    "response":{
            "opCode": 406,            // 一组或多组Revision不匹配
            "totalCount": 36          // TotalCount 37， 当前用户属性包含37个属性组
            }
}
```

### 订阅用户属性事件通知

#### 接口描述

用户属性变更（设置、更新、删除）后，RTM 会发送对应的Storage事件通知。

#### 接口方法

用户如果需要收到实时通知提醒，需要保证完成以下几步：

**1.开发者在声网 Console 后台需要开启Storage用户属性事件通知功能，如下所示：**

console上面storage字段解释如下：

|       字段      |  类型  |                                     解释                                    |
| :-----------: | :--: | :-----------------------------------------------------------------------: |
|    Storage    | Bool |                    应用中是否开始 Storage 存储功能，如果开启，则会产生对应的费用。                   |
|     选择存储地区    |  单选  |                 选择 Storage 服务所在区域，区域有：中国、东南亚、日韩新加坡、北美、欧洲等                 |
|    用户属性事件通知   | Bool |                                是否开启用户属性事件通知                               |
|    频道属性事件通知   | Bool |                                是否开启频道属性事件通知                               |
|  用户属性Deltas更新 | Bool | 是否开启用户属性事件 Delta 更新，如果开启，则每次收到的用户属性变更事件中的data数据为 delta 更新数据，否则是用户属性的全量数据。 |
|  频道属性Deltas更新 | Bool | 是否开启频道属性事件 Delta 更新，如果开启，则每次收到的频道属性变更事件中的data数据为 delta 更新数据，否则是频道属性的全量数据。 |
| 禁止获取所有用户属性API | Bool |                       是否禁用 getAllUserMetadata() API                       |
| 禁止获取所有频道属性API | Bool |                      是否禁用 getAllChannelMetadata() API                     |

**2.用户需要设置Event Handler，开启事件监听，如下所示：**

Node.js开启Storage事件监听

```js
rtm.onEvent({
    storage: (storageEvent) => {
        var channel = storageEvent.channel; // The channel
        var channelGroup = storageEvent.channelGroup; // The channel group
        var timetoken = storageEvent.timetoken; // The event timetoken
        var publisher = storageEvent.publisher; // The UID that triggered this event
        var event = storageEvent.event; // The event name that occurred
        var type = storageEvent.type; // The event type that occurred
        var data = storageEvent.data; // The event data that occurred
    },
 
});
```

频道属性事件的 storageEvent.event为UserMetaData, storageEvent.type根据用户的操作不同有set、remove、update三种不同的类型。 当SDK 订阅用户属性事件后，收到的 storageEvent 数据的 Object 字段格式如下：

```
{
 {
    "channel": "chat-user-9257-umd",
    "channelGroup": "chat-usrs",
    "timetoken": "15119446002445794"
    "publisher": "Tony",
    "event": "UserMetaData",
    "type": "remove",
    "data": {}
    }
```

|      字段      |                                  解释                                 |
| :----------: | :-----------------------------------------------------------------: |
|    channel   |                              此事件属于哪个频道                              |
| channelGroup |                              此事件属于哪个频道组                             |
|   timetoken  |                            此事件的TimeToken                            |
|   publisher  |                              事件发送者的UID                              |
|     event    |          事件名称：频道属性事件为 channelMetaData，用户属性事件为 userMetadata          |
|     type     |                        事件类型：set、remove、update                       |
|     data     | 事件对应的变更返回数据，根据Console中 Metadata-deltas设置，此项可以是频道属性的全量数据也可以是delta数据。 |

**3.每个用户的用户属性事件发布在以用户 UID 为前缀加 "-umd" 命名的频道中，例如，你需要接收 UID为 “chat-user-9257” 用户的用户属性变更事件，你需要首先 Subcribe 名为 "chat-user-9257-umd" 频道**

订阅频道属性API调用

```js
rtm.subscribe({
    channels: ['chat-user-9257-umd']
});
```

完成上述几步后，终端用户就可以实时接收到Storage用户属性事件通知了。

```

需要特别注意的是，ChannelMetadata 与 UserMetadata 变更事件都是发布在一个特殊的频道中，不同点在于：
 - ChannelMetadata自动与频道绑定，用户在使用Subscribe API订阅频道时候可以使用 withMetadata参数选择是否同时订阅ChannelMetadata事件，默认是自动订阅的。ChannelMetadata 事件实际上是发布在以频道名加“-cmd”后缀结尾的特殊频道中，例如频道名为“public-chatChannel"，则对应的频道属性事件发布频道名称为“public-chatChannel-cmd”。
 - 以上规则也适用于 Presence 事件通知，用户在使用Subscribe API订阅频道时候可以使用 withPresence参数选择是否同事订阅Presence事件，默认是自动订阅的。Presence 事件实际上是发布在以频道名加“-pres”后缀结尾的特殊频道中，例如频道名为“public-chatChannel"，则对应的 Presence 事件发布频道名称为“public-chatChannel-pres”。
 - 用户属性事件实际上是发布在以用户名加“-umd”后缀结尾的特殊频道中，例如用户名为“chat-user-9257”，则对应的频道属性事件发布频道名称为“chat-user-9257-umd”。用户属性事件频道不自动订阅，用户根据业务需要自主订阅。
```
