---
title: 使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
description: 通过 Azure Monitor，可以在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 498fc101f257b05d24826cead8906b513ec34ccd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324532"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
Azure Monitor 支持在 Azure 数据资源管理器、[Application Insights](../app/app-insights-overview.md) 和 [Log Analytics](../logs/data-platform-logs.md) 之间跨服务查询。 然后，可使用 Log Analytics/Application Insights 工具来查询 Azure 数据资源管理器群集，并在跨服务查询中引用它。 本文介绍如何进行跨服务查询。

以下示意图显示了 Azure Monitor 跨服务流：

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="显示用户、Azure Monitor、代理和 Azure 数据资源管理器之间的查询流的示意图。":::

>[!NOTE]
> Azure Monitor 跨服务查询目前处于公共预览阶段。 如有任何疑问，请联系[服务团队](mailto:ADXProxy@microsoft.com)。

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>交叉查询 Log Analytics/Application Insights 资源和 Azure 数据资源管理器

可使用支持 Kusto 查询的客户端工具来运行跨资源查询。 这些工具的示例包括 Log Analytics Web UI、工作簿、PowerShell 和 REST API。

在 `adx` 模式的查询中输入 Azure 数据资源管理器群集的标识符，后跟数据库名称和表。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="显示跨服务查询示例的屏幕截图。":::

> [!NOTE]
>* 数据库名称区分大小写。
>* 不支持使用跨资源查询作为警报。
>* 不支持标识群集中的“时间戳”列，Log Analytics 查询 API 将不通过时间筛选器

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>将 Azure 数据资源管理器群集表与 Log Analytics 工作区合并

使用 `union` 命令将群集表与 Log Analytics 工作区合并。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="显示使用 union 命令跨服务查询示例的屏幕截图。":::

> [!Tip]
> 允许使用速记格式：ClusterName/InitialCatalog 。 例如 `adx('help/Samples')` 转换为 `adx('help.kusto.windows.net/Samples')`。

>[!Note]
> 
>* 使用[`join` 运算符](/azure/data-explorer/kusto/query/joinoperator)（而不是联合）时，需要使用 [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) 将 Azure 数据资源管理器群集中的数据与 Log Analytics 工作区合并。
>* 使用 Hint.remote={Log Analytics 工作区的方向} - 例如：
>```kusto
>AzureDiagnostics
>| join hint.remote=left adx("cluster=ClusterURI").AzureDiagnostics on (ColumnName)
>```

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>将一个租户的 Azure 数据资源管理器群集中的数据与另一个租户的 Azure Monitor 资源联接

不支持在服务之间跨租户查询。 你已登录到单个租户，可运行跨两个资源的查询。

如果 Azure 数据资源管理器资源位于租户 A 中，Log Analytics 工作区位于租户 B 中，那么请使用下述某种方法：

*  通过 Azure 数据资源管理器，可以为不同租户中的主体添加角色。 在 Azure 数据资源管理器群集上将用户 ID 作为授权用户添加到租户 B 中。 验证 Azure 数据资源管理器群集上的 [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) 属性是否包含租户 B。在租户 B 中完全运行交叉查询。
*  使用 [Lighthouse](../../lighthouse/index.yml) 将 Azure Monitor 资源投射到租户 A。

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>从不同租户连接到 Azure 数据资源管理器群集

Kusto 资源管理器会自动将你登录到用户帐户最初所属的租户。 若要使用同一用户帐户访问其他租户中的资源，必须在以下连接字符串中显式指定 `TenantId`：

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>后续步骤
* [编写查询](/azure/data-explorer/write-queries)
* [使用 Azure 数据资源管理器查询 Azure Monitor 中的数据](/azure/data-explorer/query-monitor-data)
* [在 Azure Monitor 中执行跨资源日志查询](../logs/cross-workspace-query.md)