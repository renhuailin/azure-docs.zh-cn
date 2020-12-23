---
title: Azure 服务总线 - 暂停消息实体
description: 本文介绍如何暂时暂停和重新激活 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543045"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暂停（禁用）和重新激活消息实体

可以暂停队列、主题和订阅。 暂停后，实体处于禁用状态，其中所有消息都保留在存储中。 不过，无法删除或添加消息，相应的协议操作也会导致错误出现。

出于紧急管理的原因，可能需要挂起实体。 例如，错误的接收方会将消息从队列中移除，处理失败，还会错误地完成消息并将其删除。 在这种情况下，你可能希望在更正和部署代码之前禁用接收队列。 

暂停或重新激活可以由用户或系统执行。 系统仅出于多种原因（例如达到订阅支出限制）暂停实体。 系统禁用的实体不能被用户重新激活，但在暂停原因消除后就会还原。

## <a name="queue-status"></a>队列状态 
可为 **队列** 设置的状态为：

-   **Active** ：队列处于活动状态。 您可以向队列发送消息和从队列接收消息。 
-   **Disabled** ：队列处于暂停状态。 它相当于同时设置 **SendDisabled** 和 **microsoft.servicebus.messaging.entitystatus.receivedisabled** 。 
-   **SendDisabled** ：不能将消息发送到队列，但可以从该队列接收消息。 如果尝试将消息发送到队列，则会出现异常。 
-   **Microsoft.servicebus.messaging.entitystatus.receivedisabled** ：可以向队列发送消息，但不能从该队列接收消息。 如果尝试将消息接收到队列，则会出现异常。


### <a name="change-the-queue-status-in-the-azure-portal"></a>在 Azure 门户中更改队列状态： 

1. 在 Azure 门户中，导航到你的服务总线命名空间。 
1. 选择要更改其状态的队列。 你将在中间窗格中看到队列。 
1. 在 " **服务总线队列** " 页上，将队列的当前状态视为超链接。 如果未在左侧菜单中选择 " **概述** "，请选择它以查看队列的状态。 选择要更改的队列的当前状态。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="选择队列状态":::
4. 选择队列的新状态，然后选择 **"确定"** 。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="设置队列状态":::
    
你还可以使用 .NET SDK 中的服务总线 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) api 来禁用发送和接收操作，或者通过 Azure CLI 或 Azure PowerShell 使用 Azure 资源管理器模板。

### <a name="change-the-queue-status-using-azure-powershell"></a>使用 Azure PowerShell 更改队列状态
下面的示例展示了用于禁用队列的 PowerShell 命令。 重新激活命令等同于以下示例，只需将 `Status` 设置为“Active”即可。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>主题状态
可以在 Azure 门户中更改主题状态。 选择主题的当前状态以查看以下页面，此页面允许您更改状态。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="更改主题状态":::

可为 **主题** 设置的状态包括：
- **活动** ：主题处于活动状态。 您可以向主题发送消息。 
- **Disabled** ：主题被挂起。 不能将消息发送到主题。 
- **SendDisabled** ：与 **已禁用** 的效果相同。 不能将消息发送到主题。 如果尝试向主题发送消息，则会出现异常。 

## <a name="subscription-status"></a>订阅状态
可以在 Azure 门户中更改订阅状态。 选择订阅的当前状态以查看以下页面，此页面允许您更改状态。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="更改订阅状态":::

可为 **订阅** 设置的状态如下：
- **活动** ：订阅处于活动状态。 可以接收 inline.frm 订阅的消息。
- **已禁用** ：订阅已挂起。 不能从订阅接收消息。 
- **Microsoft.servicebus.messaging.entitystatus.receivedisabled** ：与 **已禁用** 的效果相同。 不能从订阅接收消息。 如果尝试接收到订阅的消息，则会出现异常。

| 主题状态 | 订阅状态 | 行为 | 
| ------------ | ------------------- | -------- | 
| 可用 | 可用 | 您可以向主题发送消息，并从订阅接收消息。 | 
| 可用 | 禁用或接收已禁用 | 你可以向主题发送消息，但无法从订阅接收消息 | 
| 禁用或发送已禁用 | 可用 | 不能向主题发送消息，但可以接收已经在订阅中的消息。 | 
| 禁用或发送已禁用 | 禁用或接收已禁用 | 无法将消息发送到主题，也无法从订阅接收消息。 | 

## <a name="other-statuses"></a>其他状态
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 枚举还定义了一组只能由系统设置的过渡状态。 


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

