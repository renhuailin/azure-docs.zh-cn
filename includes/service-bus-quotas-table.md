---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 226377f2a8df895c078d05b58d5ed49c4bba9117
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803380"
---
下表列出了特定于 Azure 服务总线消息的配额信息。 若要了解服务总线的定价和其他配额，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配额名称 | 作用域 | 值 | 说明 | 
| --- | --- | --- | --- |
| 每个 Azure 订阅的最大命名空间数 |命名空间 |  1000（默认值和最大值） |对更多命名空间的后续请求被拒绝。 |
| 队列或主题大小 |实体 | 1、2、3、4 GB 或 5 GB。<p>在高级 SKU 以及启用了[分区](../articles/service-bus-messaging/service-bus-partitioning.md)的标准 SKU 中，队列或主题的最大大小是 80 GB。</p><p>对于高级命名空间，每个[消息传送单元](../articles/service-bus-messaging/service-bus-premium-messaging.md)的总大小限制为 1 TB。 命名空间中所有实体的总大小不能超过此限制。</p> | 创建/更新队列或主题时定义。 <br/><br/> 后续的传入消息会被拒绝，且调用代码会收到异常。 |
| 命名空间上的并发连接数 |命名空间 |Net Messaging：1,000。<br /><br />AMQP：5,000。 | 系统会拒绝后续的附加连接请求，且调用代码会收到异常。 REST 操作不计入并发 TCP 连接数。 |
| 队列、主题或订阅实体上的并发接收请求数 |实体 | 5,000 |后续的接收请求会被拒绝，且调用代码会收到异常。 此配额适用于一个主题上所有订阅的并发接收操作总数。 |
| 每个命名空间的主题或队列数 |命名空间 | 基本层或标准层为 10,000。 命名空间中主题和队列的数目之和必须小于或等于 10,000。 <br/><br/>对于高级层，每个消息传送单元 (MU) 为 1,000。 | 系统将拒绝后续的在命名空间中创建新主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |
| 每个命名空间的[分区主题或队列](../articles/service-bus-messaging/service-bus-partitioning.md)数 |命名空间 | 基本层和标准层：100。<br/><br/>[高级](../articles/service-bus-messaging/service-bus-premium-messaging.md)层中不支持分区实体。<br/><br />每个分区的队列或主题都会计入每个命名空间 1,000 个实体的配额。 | 系统将拒绝后续的在命名空间中创建新分区主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是通过管理 API 调用的，调用代码会收到 QuotaExceededException 异常。 <p>如果要在基本或标准层命名空间中具有更多分区实体，请创建更多命名空间。 </p>|
| 任一消息实体路径的最大大小：队列或主题 |实体 |- |260 个字符。 |
| 任一消息实体名称的最大大小：命名空间、订阅或订阅规则 |实体 |- |50 个字符。 |
| 消息 ID 的最大大小 | 实体 |- | 128 |
| 消息会话 ID 的最大大小 | 实体 |- | 128 |
| 队列、主题或订阅实体的消息大小 |实体 |超过这些配额的传入消息会被拒绝，且调用代码会收到异常。 |最大消息大小：[标准层](../articles/service-bus-messaging/service-bus-premium-messaging.md)为 256 KB，[高级层](../articles/service-bus-messaging/service-bus-premium-messaging.md)为 1 MB。 <br /><br />由于系统开销，此限制小于这些值。<br /><br />最大标头大小：64 KB。<br /><br />属性包中标头属性的最大数：byte/int.MaxValue。<br /><br />属性包中属性的最大大小：属性名称和值都限制为 32KB。 |
| 队列、主题或订阅实体的消息属性大小 |实体 | 生成了 `SerializationException` 异常。 |每个属性的最大消息属性大小为 32 KB。 所有属性的累计大小不得超过 64 KB。 此限制适用于中转消息的整个标头，其中既有用户属性也有系统属性，如序列号、标签和消息 ID。 |
| 每个主题的订阅数 |实体 |系统将拒绝后续的为主题创建更多订阅的请求。 因此，如果是通过门户配置的，会显示错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |标准层和高级层每个主题 2,000 个。 |
| 每个主题的 SQL 筛选器数 |实体 |在主题上创建更多筛选器的后续请求会被拒绝，且调用代码会收到异常。 |2,000 |
| 每个主题的相关性筛选器数 |实体 |在主题上创建更多筛选器的后续请求会被拒绝，且调用代码会收到异常。 |100,000 |
| SQL 筛选器或操作的大小 |命名空间 |创建更多筛选器的后续请求将被拒绝，且调用代码会收到异常。 |筛选器条件字符串的最大长度：1,024 (1 K)。<br /><br />规则操作字符串的最大长度：1,024 (1 K)。<br /><br />每个规则操作的最大表达式数：32。 |
| 每个命名空间、队列或主题的共享访问授权规则数 |实体、命名空间 |创建更多规则的后续请求将被拒绝，且调用代码会收到异常。 |每个实体类型的最大规则数：12. <br /><br /> 在服务总线命名空间上配置的规则适用于所有类型：队列、主题。 |
| 每个事务的消息数 | 事务 | 更多传入消息会被拒绝，并且调用代码会收到异常，其中指明“不能在单个事务中发送 100 条以上的消息”。 | 100 <br /><br /> 适用于 Send() 和 SendAsync() 操作。 |
| 虚拟网络和 IP 筛选器规则的数量 | 命名空间 | &nbsp; | 128 |

[Azure portal]: https://portal.azure.com
