---
title: 使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
description: 使用 Azure Monitor 在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8bc47eb2a6a22224f4df186136da2c184979ea24
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608408"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>使用 Azure Monitor 跨资源查询 Azure 数据资源管理器
Azure Monitor 支持 Azure 数据资源管理器、 [Application Insights](../app/app-insights-overview.md)和 [Log Analytics](../logs/data-platform-logs.md)之间的跨服务查询。 然后，你可以使用 Log Analytics/Application Insights 工具查询 Azure 数据资源管理器群集，并在跨服务查询中引用它。 本文介绍如何进行跨服务查询。

下图显示了 Azure Monitor 跨服务流：

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="显示用户、Azure Monitor、代理和 Azure 数据资源管理器之间的查询流的关系图。":::

>[!NOTE]
> Azure Monitor 的跨服务查询是公共预览。 如有任何疑问，请联系 [服务团队](mailto:ADXProxy@microsoft.com) 。

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>跨查询 Log Analytics 或 Application Insights 资源和 Azure 数据资源管理器

你可以使用支持 Kusto 查询的客户端工具运行跨资源查询。 这些工具的示例包括 Log Analytics web UI、工作簿、PowerShell 和 REST API。

在模式中的查询中输入 Azure 数据资源管理器群集的标识符 `adx` ，后跟数据库名称和表。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="显示跨服务查询示例的屏幕截图。":::

> [!NOTE]
>* 数据库名称区分大小写。
>* 不支持使用跨资源查询作为警报。

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>将 Azure 数据资源管理器群集表与 Log Analytics 工作区结合使用

使用 `union` 命令将分类表与 Log Analytics 工作区组合在一起。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="使用 union 命令显示跨服务查询示例的屏幕截图。":::

> [!Tip]
> 允许使用速记格式： *ClusterName* / *InitialCatalog*。 例如， `adx('help/Samples')` 转换为 `adx('help.kusto.windows.net/Samples')` 。

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>将一个租户的 Azure 数据资源管理器群集中的数据与另一个租户的 Azure Monitor 资源联接

不支持在服务之间跨租户查询。 你已登录到单个租户，用于运行跨两个资源的查询。

如果 Azure 数据资源管理器资源在租户 A 中，而 Log Analytics 工作区在租户 B 中，则使用以下方法之一：

*  通过 Azure 数据资源管理器，可以为不同租户中的主体添加角色。 将租户 B 中的用户 ID 添加为 Azure 数据资源管理器群集上的授权用户。 验证 Azure 数据资源管理器群集上的 [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) 属性包含租户 b. 完全在租户 b 中运行交叉查询。
*  使用 [Lighthouse](../../lighthouse/index.yml) 将 Azure Monitor 资源投影到租户 A。

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>从不同租户连接到 Azure 数据资源管理器群集

Kusto 资源管理器会自动登录到用户帐户最初所属的租户。 若要访问具有相同用户帐户的其他租户中的资源，必须 `TenantId` 在连接字符串中显式指定：

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>后续步骤
* [编写查询](/azure/data-explorer/write-queries)
* [使用 Azure 数据资源管理器查询 Azure Monitor 中的数据](/azure/data-explorer/query-monitor-data)
* [在 Azure Monitor 中执行跨资源日志查询](../logs/cross-workspace-query.md)