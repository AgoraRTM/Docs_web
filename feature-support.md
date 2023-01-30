# 📋 Feature Support

## Setup

Setup 接口为开发者提供配置和初始化 RTM 对象的方法，实例化的 RTM 对象具备自动连接管理的能力，例如：连接、断开链接、短线重连等，同时还具备销毁自身实例的方法。

|           名称           | 描述                                                 |
| :--------------------: | -------------------------------------------------- |
|  _**Create Instance**_ | 初始化并创建一个 RTM 实例对象                                  |
|         `appId`        | 设置 `appId`, RTM 服务以 `appId` 为命名空间，同一个`appId`互通     |
|         `token`        | 设置 `Token`                                         |
|        `userId`        | 设置用户身份标示，每个用户或者设备在初始化时必须包含一个唯一的UID，否则连接不上RTM服务。    |
|       `cipherKey`      | 如果参数被设置，发送消息的负载将会被加密，订阅者在接收到消息后需要解密。               |
|       `logUpload`      | 配置日志保存路径，便于后续排查问题                                  |
|          `ssl`         | 开启TSL/SSL数据传输，永远是开启的。                              |
|    `presenceTimeout`   | 设置用户 presence TimeOut默认时间，单位为秒。范围从 10 -300 秒，默认30秒 |
|   `heartbeatInterval`  | 设置客户端SDK与 RTM 服务器心跳检测时间，单位为秒。范围从 10 -300 秒，默认30秒   |
|         `proxy`        | 设置并 Proxy功能。                                       |
|     `EventHandler`     | 初始化事件通知句柄                                          |
| _**Release Instance**_ | 销毁 RTM 实例对象，回收资源。                                  |

## Publish

此接口方法将向 `Message Channel` 中发送消息

{% hint style="info" %}
注意：`Subscribe` 方法只支持`Message Channel`。
{% endhint %}

|              名称              | 描述                                |
| :--------------------------: | --------------------------------- |
|       `storeInHistory`       | 配置发送消息是否也将此消息存储到历史消息中，默认 `False` |
| _**Publish String Message**_ | 发送字符串消息                           |
| _**Publish Binary Message**_ | 发送二进制消息                           |
| _**Publish with Extradata**_ | 发送消息附带额外数据以便于端侧过滤                 |

## Subscribe

此接口让客户端订阅 Message  Channel  并开始监听频道中的消息事件、出席通知事件、频道属性通知事件和锁通知事件等。

{% hint style="info" %}
注意：`Subscribe` 方法只支持`Message Channel` 和 `Message Channel Group`。
{% endhint %}

|                名称                | 描述                                           |
| :------------------------------: | -------------------------------------------- |
|    _**Subscribe to Channel**_    | 支持订阅 `Message Channel`                       |
| _**Subscribe to Channel Group**_ | 支持订阅 `Message Channel Group`                 |
|           `withMessage`          | 支持是否接收频道或频道组中的消息事件，默认为 `True`                |
|          `withPresence`          | 支持是否接收频道或频道组中的出席通知事件，默认为 `True`             |
|          `withMetaData`          | 支持是否接收频道或频道组中的频道属性事件，默认为 `True`             |
|            `withLock`            | 支持是否接收频道或频道组中的锁事件，默认为 `Fasle`               |
|             `beQuite`            | 支持是否静默加入频道或频道组（不向频道中的他人发送出席通知事件）默认为 `False` |
|      _**Publisher userId**_      | 支持接收的事件通知中包含 `userId`                       |
|      _**Filter Expression**_     | 支持端侧过滤接收消息                                   |

## Unsubscribe

取消订阅Message Channel 或 Message Channel Group，停止接收其中的所有事件通知。

{% hint style="info" %}
注意：Unsubscribe 方法只支持`Message Channel` 和 `Message Channel Group`。
{% endhint %}

|                  名称                  | 描述                                            |
| :----------------------------------: | --------------------------------------------- |
|    _**Unsubscribe from Channel**_   | 支持取消订阅 Message Channel ,停止接收其中的所有事件通知。       |
| _**Unsubscribe from Channel Group**_ | 支持取消订阅 Message Channel Group ,停止接收其中的所有事件通知。 |

## Stream Channel

Stream Channel是 RTM 中的高频数据通道，支持数据高频发送和接收、支持与RTC共频道、支持数据与音视频同步传输、支持Topic等

|             名称             | 描述                                       |
| :------------------------: | ---------------------------------------- |
|  **Create Stream Channel** | 支持创建 Stream Channel                      |
|      **Join Channel**      | 支持加入频道                                   |
|       `withPresence`       | 支持是否接收频道中的出席通知事件，默认为 `True`             |
|       `withMetaData`       | 支持是否接收频道中的频道属性事件，默认为 `True`             |
|          `beQuite`         | 支持是否静默加入频道（不向频道中的他人发送出席通知事件）默认为 `False` |
|         `withLock`         | 支持接收频道中的锁事件通知。默认为 `Fas`                  |
|      **Leave Channe**l     | 支持离开频道并取消订阅其中的所有事件通知                    |
| **Share Channel with RTC** | 支持当前频道与RTC数据流共频道。                        |
|     **Release Channel**    | 支持销毁频道                                   |

## Topic

Topic 是 Stream Channel 中消息的组织和分发方式，Stream Channel中，用户可以加入 Topic，发送 Topic Message，接收Topic Message等。

|                 名称                 | 描述                                             |
| :--------------------------------: | ---------------------------------------------- |
|           **Join Topic**           | 支持加入Topic，成为此Topic 的 Publisher之一              |
|                `Qos`               | 支持在此Topic中发送消息的 QoS 保障等级（保序或不保序）               |
|            `syncWithRTC`           | 支持此Topic中发送的数据是否与共频道的RTC音视频数据流实现数据同步，默认是false. |
|             `extraMet`a            | 支持加入Topic 的其他辅助信息                              |
|  **Publish String Topic Message**  | 支持向Topic 中发送 String 类型消息                       |
|  **Publish Binary Topic Message**  | 支持向Topic 中发送 Binary 类型消息                       |
|           **Leave Topic**          | 支持离开Topic，不再成为Publisher之一                      |
|         **Subscribe Topic**        | 支持订阅 Topic 中发布的消息                              |
|              `filter`              | 支持只订阅Topic中符合要求的 Publisher 发布的消息（按需订阅）         |
|   **Unsubscribe Users in Topic**   | 支持取消订阅Topic 中指定的 Publisher 发布的消息               |
| **Unsubscribe All Users in Topic** | 支持取消订阅Topic 中所有 Publisher 发布的消息                |

## Presence

RTM 的 Presence 能力让用户可以实时跟踪和感知其他用的在线状态和Topic的更新动态等，此能力是RTM 为用户提供的非常重要的服务发现能力。

{% hint style="success" %}
此特性同时适用于 Message  Channel 与 Stream Channel
{% endhint %}

|          名称         |                                                                    |
| :-----------------: | ------------------------------------------------------------------ |
|     **Who Now**     | 支持查询频道中在线人数和人员列表                                                   |
|    `includeState`   | 支持查询在线用户的临时状态                                                      |
|    `includeUIDs`    | 支持查询在线人员列表                                                         |
|    **where Now**    | 支持查询制定用户在哪些频道                                                      |
|    **Set State**    | 支持设置用户临时状态                                                         |
|    **Get State**    | 支持查询用户临时状态                                                         |
|   **Remove State**  | 支持删除用户临时状态                                                         |
| _**Announce Mode**_ | 在此模式下，`join`、`leave`和`timeout`事件`state-change`在触发时立即发送到客户端。        |
| _**Interval Mode**_ | 在这种模式下，`join`, `leave`,`timeout`事件将间隔Console中项目设置`interval数值定时发送`。 |

## Storage

RTM 的 Storage 能力支持用户设置、更新、查询和阐述频道属性和用户属性。

{% hint style="success" %}
此特性同时适用于 Message  Channel 与 Stream Channel
{% endhint %}

|                     名称                    | 描述            |
| :---------------------------------------: | ------------- |
|          **Set Channel Metadata**         | 支持设置频道属性      |
|          **Get Channel Metadata**         | 支持获取频道属性      |
|        **Remove Channel Metadata**        | 支持删除频道属性      |
|        **Update Channel Metadata**        | 支持更新频道属性      |
| **CAS Control with** **Channel Metadata** | 支持频道属性变更的版本控制 |
|  **Locks support with Channel Metadata**  | 支持锁的应用        |
|           **Set User Metadata**           | 支持设置用户属性      |
|           **Get User Metadata**           | 支持获取用户属性    |
|          **Remove User Metadata**         | 支持删除用户属性      |
|          **Update User Metadata**         | 支持更新用户属性      |
|  **CAS Control with** **Channel Metadat** | 支持用户属性变更的版本控制 |
|        **Subscribe User Metadata**        | 支持订阅用户属性      |
|       **Unsubscribe User Metadata**       | 支持取消订阅用户属性    |

## Locks

RTM 支持分布式锁的能力，用户可以基于此特性解决业务层竞争业务。

{% hint style="success" %}
此特性同时适用于 Message  Channel 与 Stream Channel
{% endhint %}

|        名称        | 描述            |
| :--------------: | ------------- |
|   **Set Lock**   | 支持在频道中设置锁     |
|       `ttl`      | 支持配置锁的生命周期    |
| **Acquire Lock** | 支持在频道中获取锁     |
|      `retry`     | 支持获取锁的时失败自动重试 |
| **Release Lock** | 支持频道中释放锁      |
| **Disable Lock** | 支持在频道中剥夺指定锁  |
|   **List Lock**  | 支持查询频道中所有锁    |

## History

|   |   |
| - | - |
|   |   |
|   |   |
|   |   |

## Authentication

|   |   |
| - | - |
|   |   |
|   |   |
|   |   |

## Others

RTM支持的其他特性

|      名称     | 描述        |
| :---------: | --------- |
| **Encrypt** | 支持端侧AES加密 |
| **Decrypt** | 支持端侧AES解密 |
|             |           |

