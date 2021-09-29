---
title: Azure 消息传送服务 - 从服务管理器迁移到资源管理器
description: 本文提供了从弃用的 Azure 服务管理器 REST API 和 PowerShell cmdlet 到资源管理器 REST API 和 PowerShell cmdlet 的映射。
ms.topic: article
ms.date: 08/31/2021
ms.openlocfilehash: 7d5e4ff0748c2b31c0e9a5c7cdaeb8f2d196d915
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791833"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>弃用了 Azure 服务管理器对 Azure 服务总线、中继和事件中心的支持

资源管理器是我们的下一代云基础结构堆栈，正在完全取代“经典”Azure 服务管理模型（经典部署模型）。 因此，我们将于 2021 年 11 月 1 日停用经典部署模型 REST API 以及对服务总线、中继和事件中心的支持。 此弃用曾首次在 [Microsoft Tech Community 公告](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)上公布。 这些 API 的 URI 中包含 `management.core.windows.net`，这是为了方便识别。 请参阅下表，了解一系列已弃用的 API 及其 Azure 资源管理器 API 版本，后者是你现在应该使用的。

若继续使用服务总线、中继和事件中心，请在 2021 年 10 月 31 日之前迁移到资源管理器。 我们鼓励所有仍在使用旧 API 的客户尽快转换版本，以便利用资源管理器带来的其他好处，这包括资源分组、标记、简化的部署和管理过程，以及使用 Azure 基于角色的访问控制 (Azure RBAC) 进行精细的访问控制。

若要详细了解 Azure 资源管理器与 Azure 服务管理器的对比情况，请查看 [TechNet 博客](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)。

若要详细了解适用于 Azure 服务总线、中继和事件中心的服务管理器 API 和资源管理器 API，请参阅我们的 REST API 文档：

- [Azure 服务总线](/rest/api/servicebus/)
- [Azure 事件中心](/rest/api/eventhub/)
- [Azure 中继](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>服务管理器 REST API - 资源管理器 REST API

| 服务管理器 API（已弃用） | 资源管理器 - 服务总线 API | 资源管理器 - 事件中心 API | 资源管理器 - 中继 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[服务总线获取命名空间](/rest/api/servicebus/get-namespace)<br/>[事件中心获取命名空间](/rest/api/eventhub/get-event-hub)<br/>[中继获取命名空间](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>服务总线/事件中心/中继 GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) | [listkeys](/rest/api/eventhub/stable/authorization-rules-event-hubs/list-keys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Topics-GetTopicsAsync**<br/>服务总线<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/stable/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Queues-GetQueueAsync** <br/>服务总线<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/stable/queues/get) | &nbsp; | &nbsp; | 
| **Relays-GetRelaysAsync**<br/>[获取中继](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>服务总线/事件中心/中继 GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/get-authorization-rule) | [getauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/get-authorization-rule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNamespaceAsync**<br/>[服务总线删除命名空间](/rest/api/servicebus/delete-namespace)<br/>[事件中心删除命名空间](/rest/api/eventhub/delete-event-hub)<br/>[中继删除命名空间](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/stable/namespaces/delete) | [delete](/rest/api/eventhub/stable/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>服务总线/事件中心/中继获取命名空间<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>服务总线/事件中心/中继获取命名空间<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>服务总线/事件中心/中继获取命名空间<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
服务总线/事件中心/中继<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/stable/authorization-rules-event-hubs/create-or-update-authorization-rule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[服务总线获取命名空间](/rest/api/servicebus/get-namespace)<br/>[事件中心获取命名空间](/rest/api/eventhub/get-event-hub)<br/>[中继获取命名空间](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/stable/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>服务总线/事件中心/中继获取命名空间<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/stable/regions/listbysku) | [listbysku](/rest/api/eventhub/stable/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>服务总线/事件中心/中继<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[列出事件中心](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/stable/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[获取事件中心](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>服务总线/事件中心/中继<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/stable/namespaces-authorization-rules/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/eventhub/stable/authorization-rules-namespaces/delete-authorization-rule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>服务总线/事件中心/中继<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/stable/namespaces-authorization-rules/list-authorization-rules) | [listauthorizationrules](/rest/api/eventhub/stable/authorization-rules-namespaces/list-authorization-rules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[服务总线命名空间可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/stable/namespaces-check-name-availability/check-name-availability) | [checknameavailability](/rest/api/eventhub/stable/check-name-availability-namespaces/check-name-availability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>服务总线/事件中心/中继<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/stable/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Topics-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/stable/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>服务管理器 PowerShell - 资源管理器 PowerShell
| 服务管理器 PowerShell 命令（已弃用） | 新资源管理器命令 | 更新的资源管理器命令 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure.service/get-azuresblocation) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/get-azuresbnamespace) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/new-azuresbauthorizationrule) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/new-azuresbnamespace) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace) |
| [Remove-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/remove-azuresbauthorizationrule) | [Remove-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusauthorizationrule) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/remove-azuresbnamespace) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/set-azuresbauthorizationrule) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) |

## <a name="next-steps"></a>后续步骤
请参阅以下文档： 

- 最新 REST API 文档
    - [Azure 服务总线](/rest/api/servicebus/)
    - [Azure 事件中心](/rest/api/eventhub/)
    - [Azure 中继](/rest/api/relay/)
- 最新 PowerShell 文档
    - [Azure 服务总线](/powershell/module/azurerm.servicebus/#service_bus)
    - [Azure 事件中心](/powershell/module/azurerm.eventhub/#event_hub)
    - [Azure 事件网格](/powershell/module/azurerm.eventgrid/#event_grid)
