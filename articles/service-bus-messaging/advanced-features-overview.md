---
title: Azure 服务总线消息传送 - 高级功能
description: 本文简要概述了 Azure 服务总线中的高级功能。
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 80fab956c182b6f934a767f2dffc09ca2d0c0214
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112034801"
---
# <a name="azure-service-bus---advanced-features"></a>Azure 服务总线 - 高级功能
服务总线包括用于解决更复杂消息传送问题的高级功能。 本文介绍其中几个功能。

## <a name="message-sessions"></a>消息会话
若要在服务总线中创建先进先出 (FIFO) 保证，请使用会话。 使用消息会话，可以排他、有序的方式处理一系列无限多的相关消息。 为了允许在高度缩放的高可用性系统中处理会话，会话功能还允许存储会话状态，以便在处理程序间安全地移动会话。 有关详细信息，请参阅[消息会话：先进先出 (FIFO)](message-sessions.md)。

## <a name="autoforwarding"></a>自动转发
自动转发功能将序列或订阅链接到相同命名空间中的主题。 使用此功能时，服务总线会自动将消息从队列或订阅移动到目标队列或主题。 这样的移动都通过事务完成。 有关详细信息，请参阅[使用自动转发链接服务总线实体](service-bus-auto-forwarding.md)。

## <a name="dead-letter-queue"></a>死信队列
所有服务总线队列和主题订阅都有关联的死信队列 (DLQ)。 DLQ 包含满足以下条件的消息： 

- 无法成功传递到任何接收方。
- 已超时。
- 由接收方应用程序显式排除。 

死信队列中的消息将带有关于其进入队列原因的批注。 死信队列具有特殊的终结点，其他方面与任何常规队列类似。 应用程序或工具可以浏览 DLQ 或取消排队。 你也可以自动转发出死信队列。 有关详细信息，请参阅[服务总线死信队列概述](service-bus-dead-letter-queues.md)。

## <a name="scheduled-delivery"></a>计划的传递
可以将消息提交到队列或主题以进行延迟处理，并设置消息变得可用的时间。 还可以取消计划的消息。 有关详细信息，请参阅[计划的消息](message-sequencing.md#scheduled-messages)。

## <a name="message-deferral"></a>消息延迟
队列或订阅客户端可以延迟一段时间检索收到的消息。 消息可能是按预期顺序发布的，而客户端希望等到收到另一条消息。 延迟的消息保留在队列或订阅中，必须使用其服务分配的序列号显式重新激活。 有关详细信息，请参阅[消息延迟](message-deferral.md)。

## <a name="batching"></a>批处理
通过客户端批处理，队列或主题客户端可以累积一组消息并一起传输它们。 这样做通常是为了节约带宽或增加吞吐量。 有关详细信息，请参阅[客户端批处理](service-bus-performance-improvements.md#client-side-batching)。

## <a name="transactions"></a>事务
一个事务将两个或更多操作组合成执行作用域。 服务总线允许对针对单个事务作用域内的多个消息传递实体的操作进行分组。 消息实体可以是队列、主题或订阅。 有关详细信息，请参阅[服务总线事务处理概述](service-bus-transactions.md)。

## <a name="autodelete-on-idle"></a>出现空闲队列时自动删除
可以使用“出现空闲队列时自动删除”功能指定一个空闲时间间隔，该时间间隔过后系统会自动删除队列或主题订阅。 最短持续时间为 5 分钟。 

## <a name="duplicate-detection"></a>重复检测
重复检测功能使发送方能够再次发送相同的消息，并让代理能够删除可能的重复项。 有关详细信息，请参阅[重复检测](duplicate-detection.md)。

## <a name="geo-disaster-recovery"></a>异地灾难恢复
在 Azure 区域遭遇停机时，可以使用灾难恢复功能在其他区域或数据中心进行消息处理，以实现持续运行。 此功能在次要区域中保留可用命名空间的结构化镜像，并允许命名空间标识切换到辅助命名空间。 可用性事件平息后，已发布的消息仍保留在以前的主命名空间中以便恢复。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](service-bus-geo-dr.md)。

## <a name="security"></a>安全性
服务总线支持标准 [AMQP 1.0](service-bus-amqp-overview.md) 和 [HTTP 或 REST](/rest/api/servicebus/) 协议及其各自的安全功能，包括传输级安全性 (TLS)。 可以使用[共享访问签名](service-bus-sas.md)或 [Azure Active Directory](service-bus-authentication-and-authorization.md) 基于角色的安全性，向客户端授予访问权限。 

为针对意外的流量进行保护，服务总线提供了多种[安全功能](network-security.md)，例如 IP 防火墙和与虚拟网络的集成。 

## <a name="next-steps"></a>后续步骤
请参阅[服务总线消息传送示例](service-bus-samples.md)，其中演示了如何使用这些服务总线功能。
