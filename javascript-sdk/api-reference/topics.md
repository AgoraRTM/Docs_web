# 🦜 Topics

&#x20;`Topics` 为用户提供了一种在 Stream Channel 中管理数据流的机制。



{% hint style="info" %}
&#x20;注意：`Topics`  只在 `Stream Channel` 中有效。
{% endhint %}

想要了解 `Topics` 更多特性可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/topics" %}
[Topics](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/topics)
{% endcontent-ref %}

## Join Topic

### 接口描述

### 接口方法

你可以通过以下方式调用 `joinTopic()` 方法：

{% code lineNumbers="true" %}
```javascript
joinTopic({ 
    String topic, 
    Number Qos, 
    boolean syncWithRTC, 
    Objests extraMeta 
}):Promise<JoinTopicResponse>;
```
{% endcode %}

|     Parameter     |   Type  | Required | Defaults |                     Description                     |
| :---------------: | :-----: | :------: | -------- | :-------------------------------------------------: |
|    **`topic`**    |  String |    Yes   |          |                       Topic 名称                      |
| **`syncWithRTC`** | Boolean | Optional | false    | 此Topic中发送的数据是否与共频道的RTC音视频数据流实现数据同步（时间戳对其），默认是false. |
|     **`Qos`**     |  Number | Optional | 0        | <p>Topic 中传输数据的Qos保障：<br>0: 数据到达不保序<br>1：数据到达保序</p> |
|  **`extraMeta`**  |  Object | Optional |          |                    创建Topic 的辅助信息                    |



### 基本用法

加入Topic：

{% code lineNumbers="true" %}
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
{% endcode %}

### 返回值

{% code lineNumbers="true" %}
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
{% endcode %}





## Publish Topic Message

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
publishTopicMessage({
    Object message, 
    String topic, 
    Object meta, 
    Number sendTs 
}):Promise<PublishTopicMessageResponse>;
```
{% endcode %}

### 基本用法

向指定频道发送消息:

{% code lineNumbers="true" %}
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
{% endcode %}

### 返回值

{% code lineNumbers="true" %}
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
{% endcode %}

## Leave Topic

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
leaveTopic({
    String topic 
}):Promise<LeaveTopicResponse>;
```
{% endcode %}



### 基本用法

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

### 返回值

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



## Subscribe Topic

### 接口描述

### 接口方法



{% code lineNumbers="true" %}
```javascript
subscribeTopic({
    String topic, 
    Object filter 
}):Promise<SubscribeTopicResponse>;
```
{% endcode %}

### 基本用法

订阅 Topic 中制定的 UIDs:

{% code lineNumbers="true" %}
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
{% endcode %}

### 返回值

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

## Unsubscribe Topic

### 接口描述



### 接口方法



{% code lineNumbers="true" %}
```javascript
unsubscribeTopics({
    string topic, 
    Object filter
}):Promise<UnsubscribeTopicResponse>;
```
{% endcode %}

### 基本用法

取消订阅指定Topic:

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



### 返回值

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
