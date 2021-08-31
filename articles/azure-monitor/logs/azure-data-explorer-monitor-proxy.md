---
title: 使用 Azure 数据资源管理器查询 Azure Monitor 中的数据
description: 通过 Azure 数据资源管理器，可以在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9faa9ff9c0635c84ebc5c56a343db0426873f945
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735838"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer"></a>使用 Azure 数据资源管理器查询 Azure Monitor 中的数据

Azure 数据资源管理器支持 Azure 数据资源管理器、[Application Insights (AI)](../app/app-insights-overview.md) 和 [Log Analytics (LA)](./data-platform-logs.md) 之间的跨服务查询。 然后，你可使用 Azure 数据资源管理器工具来查询 Log Analytics/Application Insights 工作区，并在跨服务查询中引用它。 本文介绍如何创建跨服务查询，以及如何将 Log Analytics/Application Insights 工作区添加到 Azure 数据资源管理器 Web UI。

Azure 数据资源管理器跨服务查询流：:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure 数据资源管理器代理流。":::

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>将 Log Analytics/Application Insights 工作区添加到 Azure 数据资源管理器客户端工具

1. 在连接到 Log Analytics 或 Application Insights 群集之前，请先确认 Azure 数据资源管理器本机群集（例如 *help* 群集）是否显示在左侧菜单中。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 数据资源管理器原生群集。":::

 在 Azure 数据资源管理器 UI (https://dataexplorer.azure.com/clusters) 中，选择“添加群集”。 

2. 在“添加群集”窗口中，添加 LA 或 AI 群集的 URL。

    * 对于 LA：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * 对于 AI：`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 选择“添加”   。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="添加群集。":::
 
>[!NOTE]
>如果将连接添加到多个 Log Analytics/Application Insights 工作区，请为每个工作区指定不同的名称。 否则它们全部以相同的名称显示在左窗格。

 建立连接后，Log Analytics 或 Application Insights 工作区将随同 Azure 数据资源管理器本机群集显示在左侧窗格。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics 和 Azure 数据资源管理器群集。":::
 
> [!NOTE]
> 可映射的 Azure Monitor 工作区数限制为 100 个。

## <a name="create-queries-using-azure-monitor-data"></a>使用 Azure Monitor 数据创建查询

可使用支持 Kusto 查询的客户端工具来运行查询，这些工具包括：Kusto 资源管理器、Azure 数据资源管理器 Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Lens、REST API。

> [!NOTE]
> 跨服务查询功能仅用于数据检索。 有关详细信息，请参阅[函数可支持性](#function-supportability)。

> [!TIP]
> * 数据库名称应与跨服务查询中指定的资源的名称相同。 名称区分大小写。
> * 在跨群集查询中，请确保 Application Insights 应用和 Log Analytics 工作区的命名正确。
> * 如果名称包含特殊字符，这些字符会在跨服务查询中替换为 URL 编码。
> * 如果名称包含的字符不符合 [KQL 标识符命名规则](/azure/data-explorer/kusto/query/schema-entities/entity-names)，这些字符将由短划线 **-** 字符替换。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>通过 Azure 数据资源管理器客户端工具直接查询 Log Analytics 或 Application Insights 工作区

在 Log Analytics 或 Application Insights 工作区上运行查询。 验证是否已在左侧窗格中选择你的工作区。
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="查询 Log Analytics 工作区。":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>交叉查询 Log Analytics/Application Insights 和 Azure 数据资源管理器本机群集

运行跨群集服务查询时，请验证是否已在左侧窗格中选择 Azure 数据资源管理器本机群集。 下面的示例演示如何[使用 union](/azure/data-explorer/kusto/query/unionoperator)将 Azure 数据资源管理器群集表与 Log Analytics 工作区结合。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="从 Azure 数据资源管理器进行跨服务查询。":::

>[!TIP]
>* 如果使用 [`join` 运算符](/azure/data-explorer/kusto/query/joinoperator)而不是 union，那么可能需要 [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) 才能在 Azure 数据资源管理器本机群集上运行它。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>将一个租户的 Azure 数据资源管理器群集中的数据与另一个租户的 Azure Monitor 资源联接

不支持在服务之间跨租户查询。 你已登录到单个租户，以跨两个资源运行查询。

如果 Azure 数据资源管理器资源位于租户“A”中，Log Analytics 工作区位于租户“B”中，那么请使用下述两种方法之一：

1. 通过 Azure 数据资源管理器，可以为不同租户中的主体添加角色。 在 Azure 数据资源管理器群集上将用户 ID 作为授权用户添加到租户“B”中。 验证 Azure 数据资源管理器群集上的[“TrustedExternalTenant”](/powershell/module/az.kusto/update-azkustocluster)属性是否包含租户“B”。 在租户“B”中完全运行交叉查询。

2. 使用 [Lighthouse](../../lighthouse/index.yml) 将 Azure Monitor 资源投射到租户“A”。
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>从不同租户连接到 Azure 数据资源管理器群集

Kusto Explorer 会自动将你登录到用户帐户最初所属的租户。 若要使用同一用户帐户访问其他租户中的资源，必须在以下连接字符串中显式指定 `tenantId`：`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`TenantId

## <a name="function-supportability"></a>函数可支持性

Azure 数据资源管理器跨服务查询支持同时适用于 Application insights 和 Log Analytics 的函数。
此功能允许跨群集查询直接引用 Azure Monitor 表格函数。
跨服务查询支持下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下图描述了一个从 Azure 数据资源管理器 Web UI 查询表格函数的示例。
若要使用此函数，请在“查询”窗口中运行名称。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="从 Azure 数据资源管理器 Web UI 查询表格函数。":::

## <a name="additional-syntax-examples"></a>其他语法示例

调用 Log Analytics 或 Application Insights 群集时，可以使用以下语法选项：

|语法说明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 仅包含此订阅中所定义资源的群集中的数据库（**建议用于跨群集查询**） |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此订阅中所有应用/工作区的群集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|包含订阅中所有应用/工作区且属于此资源组的群集    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|仅包含此订阅中定义的资源的群集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>后续步骤

- 请参阅有关 [Log Analytics 工作区和 Application Insights 的数据结构](data-platform-logs.md)的详细信息。
- 了解如何[在 Azure 数据资源管理器中编写查询](/azure/data-explorer/write-queries)。