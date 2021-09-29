---
title: Azure 服务总线 - 暂停消息实体
description: 本文介绍如何暂时暂停和重新激活 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
ms.date: 09/28/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1f4681c49ea6114f2c0d2722a357464094720d2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218292"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暂停（禁用）和重新激活消息实体

可以暂停队列、主题和订阅。 暂停后，实体处于禁用状态，其中所有消息都保留在存储中。 不过，无法删除或添加消息，相应的协议操作也会导致错误出现。

出于紧急管理原因，你可能希望暂停实体。 例如，有故障的接收程序将消息从队列中移出、无法处理消息，还会错误地完成消息并将其删除。 在这种情况下，建议禁用接收队列，直到更正和部署代码为止。 

暂停或重新激活可以由用户或系统执行。 系统只会出于重大管理原因（如达到订阅支出限制），才会暂停实体。 系统禁用的实体不能被用户重新激活，但在暂停原因消除后就会还原。

## <a name="queue-status"></a>队列状态 
可以为队列设置如下状态：

-   **Active**：队列处于活动状态。 可以向队列发送消息或从队列接收消息。 
-   **Disabled**：队列处于暂停状态。 它相当于同时设置 SendDisabled 和 ReceiveDisabled 。 
-   **SendDisabled**：不能将消息发送到队列，但可以从队列接收消息。 如果尝试将消息发送到队列，则会出现异常。 
-   **ReceiveDisabled**：可以将消息发送到队列，但不能从队列接收消息。 如果尝试接收队列的消息，则会出现异常。


### <a name="change-the-queue-status-in-the-azure-portal"></a>在 Azure 门户中更改队列状态： 

1. 在 Azure 门户中，导航到你的服务总线命名空间。 
1. 选择要更改其状态的队列。 可在底部的中间窗格中看到队列。 
1. 在“服务总线队列”页面上，将看到队列的当前状态显示为超链接。 如果未在左侧菜单中选择“概述”，请选择该选项以查看队列的状态。 选择队列的当前状态进行更改。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="选择队列状态":::
4. 为队列选择新状态，然后选择“确定”。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="设置队列状态":::
    
也可以禁用发送和接收操作，方法是在 .NET SDK 中使用服务总线 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API，或通过 Azure CLI 或 Azure PowerShell 使用 Azure 资源管理器模板。

### <a name="change-the-queue-status-using-azure-powershell"></a>使用 Azure PowerShell 更改队列状态
下面的示例展示了用于禁用队列的 PowerShell 命令。 重新激活命令等同于以下示例，只需将 `Status` 设置为“Active”即可。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>主题状态
可以在 Azure 门户中更改主题状态。 选择主题的当前状态以查看下一页，这将允许更改状态。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="更改主题状态":::

可以为主题设置如下状态：
- **活动**：主题处于活动状态。 可以将消息发送到主题。 
- **Disabled**：主题已暂停。 不能将消息发送到主题。 
- **SendDisabled**：状态与“禁用”效果相同。 不能将消息发送到主题。 如果尝试将消息发送到主题，则会出现异常。 

## <a name="subscription-status"></a>订阅状态
可以在 Azure 门户中更改订阅状态。 选择订阅的当前状态以查看下一页，这将允许更改状态。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="更改订阅状态":::

可以为订阅设置如下状态：
- **活动**：订阅处于活动状态。 可以从订阅接收消息。
- **Disabled**：订阅已暂停。 不能从订阅接收消息。 
- **ReceiveDisabled**：状态与“禁用”效果相同。 不能从订阅接收消息。 如果尝试接收订阅的消息，则会出现异常。

| 主题状态 | 订阅状态 | 行为 | 
| ------------ | ------------------- | -------- | 
| 可用 | 可用 | 可以将消息发送到主题，并从订阅接收消息。 | 
| 可用 | “禁用”或“接收已禁用” | 可以将消息发送到主题，但不能从订阅接收消息 | 
| “禁用”或“发送已禁用” | 可用 | 不能将消息发送到主题，但可以接收已在订阅中的消息。 | 
| “禁用”或“发送已禁用” | “禁用”或“接收已禁用” | 无法将消息发送到主题，也无法从订阅接收消息。 | 

## <a name="other-statuses"></a>其他状态
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 枚举还定义了一组只能由系统设置的过渡状态。 


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

