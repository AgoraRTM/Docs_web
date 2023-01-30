# 🚀 Locks

RTM 为用户提供一整套分布式锁的能力，以满足用户在访问共享资源的时候解决竞争问题， 如果不同的系统或同一个系统的不同主机之间共享了某个临界资源，往往需要互斥来防止彼此干扰，以保证一致性。

分布式锁可以和 RTM 其他的特性接口（例如 Stroage)接合使用，以配合用户业务逻辑的实现.

想要了解 Locks 更多特性，可以查看：

{% content-ref url="https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/locks" %}
[Locks](https://app.gitbook.com/s/bnykHfZqz9P3h9dunQm4/user-guide/locks)
{% endcontent-ref %}

## Set Lock

### 接口描述



### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.setLock({
    string channel, 
    uint channelType, 
    string lockName, 
    number ttl
}):Promise<SetLockResponse>;
```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.setLock({
        channel:"my_channel",
        channelType:1,
        lockName: "my_lock",
        ttl: 30
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
type setLockStatus ={
    "errorCode": number,
    "operation": "RTMSetLockOperation",
    "reason":"Error Reason"
}
  
// Example of Response
 type setLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    lockName: String                // 锁名
}
```
{% endcode %}

## Acquire Lock

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.acquireLock({
    string channel, 
    uint channelType, 
    string lockName, 
    Boolean retry
}):Promise<AcquireLockResponse>;
```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.acquireLock({
        channel:"my_channel",
        channelType:1,
        lockName: "my_lock",
        retry:false
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
type acquireLockStatus ={
    "errorCode": number,
    "operation": "RTMAcquireOperation",
    "reason":"Error Reason"
}
  
// Example of Response 
type acquireLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    lockName: String                // 锁名
}
```
{% endcode %}

## Release Lock

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.releaseLock({
    string channel, 
    uint channelType, 
    string lockName
}):Promise<ReleaseLockResponse>;
```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.releaseLock(
    {
        channel:"my_channel",
        channelType:1,
        lockName: "my_lock"
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
type releaseLockStatus ={
    "errorCode": number,
    "operation": "RTMReleaseLockOperation",
    "reason":"Error Reason"
}
  
// Example of Response 
type releaseLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    lockName: String                // 锁名
}
```
{% endcode %}

## Revoke Lock

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.revokeLockLock({
    string channel, 
    uint channelType, 
    string lockName
}):Promise<RevokeLockResponse>;
```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.revokeLockLock(
    {
        channel:"my_channel",
        channelType:1,
        lockName: "my_lock"
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
type revokeLockStatus ={
    "errorCode": number,
    "operation": "RTMRevokeLockOperation",
    "reason":"Error Reason"
}
  
// Example of Response
type revokeLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    lockName: String                // 锁名
}
```
{% endcode %}



## Get Lock

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.getLockLock({
    string channel, 
    uint channelType
}):Promise<GetLockResponse>;

```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.getLock(
    {
        channel:"my_channel",
        channelType:1
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
type getLockStatus ={
    "errorCode": number,
    "operation": "RTMGetLockOperation",
    "reason":"Error Reason"
}
  
// Example of Response
type getLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    totalLocks: Number              // 频道中锁的数量
    locks: Array[LocksList]         //  锁信息列表
}
 
type LocksList = {
    lockName: String,               // 锁的名称
    owner: String,                  // 锁的占有者
    ttl: Uint                       // 锁的 TTL
}
```
{% endcode %}



## Remove Lock

### 接口描述

### 接口方法

{% code lineNumbers="true" %}
```javascript
rtm.Lock.removeLock({
    string channel, 
    uint channelType, 
    string lockName
}):Promise<RemoveLockResponse>;
```
{% endcode %}

### 基本用法

{% code lineNumbers="true" %}
```javascript
try{
    const resulst = await rtm.Lock.removeLockLock(
    {
        channel:"my_channel",
        channelType:1,
        lockName: "my_lock"
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
type removeLockStatus ={
    "errorCode": number,
    "operation": "RTMRemoveLockOperation",
    "reason":"Error Reason"
}
  
// Example of Response
type removeLockResponse = {
    errorCode: Number,              // 本次操作错误码
    channel: String,                // 本次操作目标频道名称      
    channelType: String,            // 频道类型
    lockName: String                // 锁名
}
```
{% endcode %}

\
