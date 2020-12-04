---
title: 使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
description: 使用 Azure Monitor 在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572144"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
Azure Monitor 支持 Azure 数据资源管理器之间的跨服务查询、 [Application Insights (AI) ](/azure/azure-monitor/app/app-insights-overview)和 [Log Analytics (LA) ](/azure/azure-monitor/platform/data-platform-logs)。 然后，你可以使用 Log Analytics/Application Insights 工具查询 Azure 数据资源管理器群集，并在跨服务查询中引用它。 本文介绍如何进行交叉服务查询。

Azure Monitor 跨服务流： :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="azure Monitor 和 azure 数据资源管理器跨服务流。":::

>[!NOTE]
>* Azure Monitor 交叉服务查询是 AllowListing 所必需的。
>* 如有任何疑问，请联系 [服务团队](mailto:ADXProxy@microsoft.com) 。
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>跨查询 Log Analytics 或 Application Insights 资源和 Azure 数据资源管理器

你可以使用支持 Kusto 查询的客户端工具（例如 Log Analytics web UI、工作簿、PowerShell、REST API 等）来运行跨资源查询。

* 在 "adx" 模式的查询中输入 Azure 数据资源管理器群集的标识符，后跟数据库名称和表。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="跨服务查询示例。":::

> [!NOTE]
>* 数据库名称区分大小写。
>* 不支持将跨资源查询作为警报。
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>结合使用 union 和 join) 与 LA 工作区结合使用 Azure 数据资源管理器群集表格 (。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="联合的跨服务查询示例。":::

>[!Tip]
>* 允许使用速记格式-ClusterName/InitialCatalog。 例如，adx ( "help/Samples" ) 转换为 adx ( "kusto/Samples" ) 
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>将一个租户的 Azure 数据资源管理器群集中的数据与另一个租户的 Azure Monitor 资源联接

不支持服务之间的跨租户查询。 你已登录到单个租户，以跨两个资源运行查询。

如果 Azure 数据资源管理器资源位于租户 "A"，并且 Log Analytics 工作区位于租户 "B" 中，请使用以下两种方法之一：

*  通过 Azure 数据资源管理器，可以为不同租户中的主体添加角色。 在 Azure 数据资源管理器群集上将用户 ID 作为授权用户添加到租户“B”中。 验证 Azure 数据资源管理器群集上的[“TrustedExternalTenant”](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)属性是否包含租户“B”。 在租户“B”中完全运行交叉查询。
*  使用 [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 将 Azure Monitor 资源投影到租户 "A"。
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>从不同租户连接到 Azure 数据资源管理器群集

Kusto Explorer 会自动将你登录到用户帐户最初所属的租户。 若要使用同一用户帐户访问其他租户中的资源，必须在以下连接字符串中显式指定 `tenantId`：`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`TenantId

## <a name="next-steps"></a>后续步骤
* [写入查询](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [使用 Azure 数据资源管理器查询 Azure Monitor 中的数据](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [在 Azure Monitor 中执行跨资源日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
