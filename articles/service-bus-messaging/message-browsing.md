---
title: Azure 服务总线 - 消息浏览
description: 通过浏览和速览服务总线消息，Azure 服务总线客户端可以枚举队列或订阅中的所有消息。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520116"
---
# <a name="message-browsing"></a>消息浏览
通过消息浏览或速览，服务总线客户端可以枚举队列或订阅中的所有消息，以用于诊断和调试。

针对队列或订阅的速览操作最多返回请求数量的消息。 下表显示了速览操作返回的消息类型。 

| 消息类型 | 是否包括？ | 
| ---------------- | ----- | 
| 活动消息 | 是 |
| 死信消息 | 否 | 
| 锁定消息 | 是 |
| 过期消息 |  可能包括（在它们成为死信消息之前） |
| 计划的消息 | 对于队列，包括。 对于订阅，不包括 |

## <a name="dead-lettered-messages"></a>死信消息
若要查看队列或订阅的死信消息，应针对与队列或订阅关联的死信队列运行速览操作。 有关详细信息，请参阅[访问死信队列](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)。

## <a name="expired-messages"></a>过期消息
过期消息可能包含在从速览操作返回的结果中。 已使用的消息和过期的消息通过异步“垃圾回收”运行进行清理。 此步骤未必会在消息过期后立即执行。 这就是为何速览操作可能会返回已过期的消息。 下一次对队列或订阅调用接收操作时，这些消息将被删除或设为死信。 请在尝试从队列中恢复延迟的消息时谨记此行为。 

过期的消息无法再通过其他任何方式定期检索，即使是 Peek 返回的消息，也不例外。 返回这些消息是设计使然，因为 Peek 是反映日志当前状态的诊断工具。

## <a name="locked-messages"></a>锁定消息
速览操作还会返回以前已锁定且当前由其他接收程序处理的消息。 但是，因为 Peek 返回的是离线快照，所以无法在速览的消息上观察到消息的锁状态。

## <a name="peek-apis"></a>Peek API
速览操作适用于队列、订阅及其死信队列。 

如果重复调用，速览操作方法会按顺序（从最低可用序号到最高可用序号）枚举队列或订阅中的所有消息。 这是消息的排队顺序，而不是消息的最终检索顺序。

还可以将 SequenceNumber 传递到速览操作。 它将用于确定从何处开始速览。 可以在不指定用于进一步枚举的参数的情况下，对速览操作进行后续调用。

## <a name="next-steps"></a>后续步骤
尝试采用所选语言的示例，探索速览或消息浏览功能：

- [适用于 Java 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/) - “速览消息”示例
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/) - “receive_peek.py”示例
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - “browseMessages.js”示例
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - “browseMessages.ts”示例
- [适用于 .NET 的 Azure.Messaging.ServiceBus 示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - 请参阅[参考文档](/dotnet/api/azure.messaging.servicebus)中针对接收器类的速览方法。

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET 的 Microsoft.Azure.ServiceBus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - “消息浏览（速览）”示例 
- [适用于 Java 的 azure-servicebus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse) - “消息浏览”示例。 
