---
title: 按资源管理对 Azure Sentinel 数据的访问 |Microsoft Docs
description: 本文介绍了如何通过用户可以访问的资源来管理对 Azure Sentinel 数据的访问。 通过使用资源管理访问权限，你可以仅提供对特定数据的访问，而无需整个 Azure Sentinel 体验。 此方法也称为资源上下文 RBAC。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054528"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>按资源管理对 Azure Sentinel 数据的访问

通常，有权访问 Azure Sentinel 工作区的用户还可以访问所有工作区数据，包括安全内容。 管理员可以使用 [azure 角色](roles.md) 配置对 azure Sentinel 中特定功能的访问权限，具体取决于其团队中的访问要求。

但是，你可能有一些用户只需要访问 Azure Sentinel 工作区中的特定数据，但不能访问整个 Azure Sentinel 环境。 例如，你可能想要 (非 SOC) 团队提供非安全操作，使其能够访问其拥有的服务器的 Windows 事件数据。

在这种情况下，我们建议你根据用户允许的资源配置基于角色的访问控制 (RBAC) ，而不是向他们提供对 Azure Sentinel 工作区或特定 Azure Sentinel 功能的访问权限。 此方法也称为设置 **资源上下文 RBAC**。

当用户通过他们可以访问的资源（而不是 Azure Sentinel 工作区）访问 Azure Sentinel 数据时，可以使用以下方法查看日志和工作簿：

- **通过资源本身**（如 Azure 虚拟机）。 使用此方法仅查看特定资源的日志和工作簿。

- **通过 Azure Monitor**。 若要创建跨多个资源和/或资源组的查询，请使用此方法。 导航到 Azure Monitor 中的日志和工作簿时，请将你的作用域定义为一个或多个特定资源组或资源。

在 Azure Monitor 中启用资源上下文 RBAC。 有关详细信息，请参阅 [管理 Azure Monitor 中的日志数据和工作区的访问权限](/azure/azure-monitor/logs/manage-access)。

> [!NOTE]
> 如果你的数据不是 Azure 资源（如 Syslog、CEF 或 AAD 数据）或自定义收集器收集的数据，则需要手动配置用于标识数据和启用访问的资源 ID。
>
> 有关详细信息，请参阅 [显式配置资源上下文 RBAC](#explicitly-configure-resource-context-rbac)。
>
## <a name="scenarios-for-resource-context-rbac"></a>用于资源上下文 RBAC 的方案

下表突出显示了资源上下文 RBAC 最有用的方案。 请注意 SOC 团队与非 SOC 团队之间的访问要求差异。

| 要求类型 |SOC 团队  |非 SOC 团队  |
|---------|---------|---------|
|**权限**     | 整个工作区        |   仅特定资源      |
|**数据访问**     |  工作区中的所有数据       | 仅限团队有权访问的资源数据        |
|**体验**     |  完整的 Azure Sentinel 体验，可能受分配给用户的 [功能权限](roles.md) 限制       |  仅记录查询和工作簿       |
|     |         |         |

如果你的团队对上表中所述的非 SOC 团队具有类似的访问要求，则资源上下文 RBAC 可能是适用于你的组织的最佳解决方案。

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>用于实现资源上下文 RBAC 的替代方法

根据组织中所需的权限，使用资源上下文 RBAC 可能无法提供完整的解决方案。

下面的列表介绍了其他数据访问解决方案可能满足你的要求的方案：

|方案  |解决方案  |
|---------|---------|
|**子公司有一个 SOC 团队需要完整的 Azure Sentinel 体验**。     |  在这种情况下，请使用多工作区体系结构来分隔你的数据权限。 <br><br>有关详细信息，请参阅： <br>- [跨工作区和租户扩展 Azure Sentinel](extend-sentinel-across-workspaces-tenants.md)<br>    - [同时处理多个工作区中的事件](multiple-workspace-view.md)          |
|**您希望提供对特定事件类型的访问权限**。     |  例如，为 Windows 管理员提供对所有系统中的 Windows 安全事件的访问权限。 <br><br>在这种情况下，可以使用 [表级 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) 来定义每个表的权限。       |
| **将访问权限限制为更精细的级别，不基于资源，或仅限于事件中字段的子集**   |   例如，你可能想要基于用户的子公司限制对 Office 365 日志的访问权限。 <br><br>在这种情况下，可以使用与 [Power BI 仪表板和报表](/azure/azure-monitor/platform/powerbi)的内置集成来提供对数据的访问。      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>显式配置资源上下文 RBAC

如果要配置资源上下文 RBAC，但你的数据不是 Azure 资源，请使用以下步骤。

例如，不是 Azure 资源的 Azure Sentinel 工作区中的数据包括 Syslog、CEF 或 AAD 数据，或自定义收集器收集的数据。

**显式配置资源上下文 RBAC**：

1. 请确保已在 Azure Monitor 中 [启用资源上下文 RBAC](/azure/azure-monitor/platform/manage-access) 。 

1. 为需要访问资源的每个用户团队[创建一个资源组](/azure/azure-resource-manager/management/manage-resource-groups-portal)，而无需使用整个 Azure Sentinel 环境。

    为每个团队成员分配 [日志读取器权限](/azure/azure-monitor/platform/manage-access#resource-permissions) 。

1. 将资源分配给创建的资源团队组，并标记带有相关资源 Id 的事件。

    Azure 资源将数据发送到 Azure Sentinel 时，会自动用数据源的资源 ID 标记日志记录。

    > [!TIP]
    > 建议你在为此目的而创建的特定资源组下对要授予访问权限的资源进行分组。
    >
    > 如果你不能这样做，请确保你的团队对你要访问的资源直接具有日志读取器权限。
    >

    有关资源 Id 的详细信息，请参阅：

    - [具有日志转发的资源 Id](#resource-ids-with-log-forwarding)
    - [具有 Logstash 集合的资源 Id](#resource-ids-with-logstash-collection)
    - [Log Analytics API 集合的资源 Id](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>具有日志转发的资源 Id

使用 [常见事件格式收集事件时 (CEF) ](connect-common-event-format.md) 或 [Syslog](connect-syslog.md)，使用日志转发从多个源系统收集事件。

例如，当 CEF 或 Syslog 转发 VM 侦听发送 Syslog 事件的源，并将其转发到 Azure Sentinel 时，会将日志转发 VM 资源 ID 分配给它们转发的所有事件。

如果有多个团队，请确保有单独的日志转发 Vm 处理每个单独团队的事件。

例如，分隔 Vm 可确保使用收集器 VM A 收集属于团队 A 的 Syslog 事件。

> [!TIP]
> - 使用本地 VM 或其他云 VM （如 AWS）作为日志转发器时，请通过实现 [Azure Arc](/azure/azure-arc/servers/overview)来确保其具有资源 ID。
> - 若要缩放日志转发 VM 环境，请考虑创建 [VM 规模集](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) 来收集 CEF 和 Sylog 日志。


### <a name="resource-ids-with-logstash-collection"></a>具有 Logstash 集合的资源 Id

如果要使用 Azure Sentinel [Logstash output 插件](connect-logstash.md)收集数据，请使用 " **azure_resource_id** " 字段配置自定义收集器，以在输出中包含资源 id。

如果使用的是资源上下文 RBAC，并且希望 API 收集的事件可用于特定用户，请使用 [为用户创建](#explicitly-configure-resource-context-rbac)的资源组的资源 ID。

例如，以下代码显示了一个示例 Logstash 配置文件：

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> 您可能想要添加多个 `output` 节来区分应用于不同事件的标记。
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Log Analytics API 集合的资源 Id

使用 [Log Analytics 数据收集器 API](/azure/azure-monitor/platform/data-collector-api)进行收集时，可以使用 HTTP [*x AzureResourceId*](/azure/azure-monitor/platform/data-collector-api#request-headers) 请求标头向事件分配资源 ID。

如果使用的是资源上下文 RBAC，并且希望 API 收集的事件可用于特定用户，请使用 [为用户创建](#explicitly-configure-resource-context-rbac)的资源组的资源 ID。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。
