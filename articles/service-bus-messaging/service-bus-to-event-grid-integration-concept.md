---
title: Azure 服务总线到事件网格集成概述 | Microsoft Docs
description: 本文介绍 Azure 服务总线消息传送如何与 Azure 事件网格集成。
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 999bfb9278f3c355e2b431c0fe3ca13648aa42c9
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412986"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure 服务总线到事件网格的集成概述
现在，当队列或订阅中存在消息时，服务总线可以在没有接收器的情况下，将事件发出到事件网格。 可以针对服务总线命名空间创建事件网格订阅、侦听这些事件，然后通过启动接收器对这些事件做出反应。 借助此功能，可以在反应式编程模型中使用服务总线。 此功能实现的重要方案是，消息量不大的服务总线队列或订阅不需要一直使用接收器来轮询消息。 

若要启用此功能，需要准备好以下各项：

* 至少包含一个服务总线队列的服务总线高级命名空间，或者至少包含一个订阅的服务总线主题。
* 对服务总线命名空间拥有“参与者”访问权限。 导航到 Azure 门户中的“服务总线”命名空间，选择“访问控制(IAM)”，并选择“角色分配”选项卡。验证你是否拥有该命名空间的参与者访问权限 。 
* 此外，需要服务总线命名空间的事件网格订阅。 存在可提取的消息时，此订阅将从事件网格接收通知。 典型的订阅方可能是 Azure 应用服务的逻辑应用功能、Azure Functions，或者与 Web 应用联系的 Webhook。 然后，订阅方会处理消息。 

![19][]

[!INCLUDE [event-grid-service-bus.md](./includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>为服务总线命名空间创建事件网格订阅
可以使用三种不同的方法，为服务总线命名空间创建事件网格订阅：

- Azure 门户。 请参阅以下教程，了解如何使用 Azure 门户为服务总线事件创建事件网格订阅，并以 Azure 逻辑应用和 Azure Functions 作为处理程序。 
    - [Azure 逻辑应用](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI。 以下 CLI 示例演示如何为服务总线命名空间创建的 [系统主题](../event-grid/system-topics.md) 创建 Azure Functions 订阅。

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell。 下面是一个示例：
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>发出事件的数量和频率如何？

如果命名空间中有多个队列和主题或订阅，则对于每个队列和每个订阅，你会至少收到一个事件。 如果服务总线实体中没有任何消息，在新消息抵达时，会立即发出事件。 或者，除非服务总线检测到活动的接收方，否则每隔两分钟发送事件。 对消息进行浏览不会导致事件中断。

默认情况下，服务总线针对命名空间中的所有实体发出事件。 如果只需获取特定实体的事件，请参阅下一部分。

### <a name="use-filters-to-limit-where-you-get-events-from"></a>使用筛选器限制事件的来源

例如，如果你只想从命名空间中的某个队列或订阅接收事件，可以使用事件网格提供的“开头为”或“结尾为”筛选器。  在某些界面中，这两个筛选器称为“前缀”和“后缀”筛选器。  如果需要接收多个（但并非所有）队列和订阅的事件，可以创建多个事件网格订阅，并为每个订阅提供一个筛选器。

## <a name="next-steps"></a>后续步骤
参阅以下教程： 
- [用于处理通过事件网格接收的服务总线消息的 Azure 逻辑应用](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [用于处理通过事件网格接收的服务总线消息的 Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
