---
title: 监视 Azure Cosmos DB |Microsoft Docs
description: 了解如何监视 Azure Cosmos DB 的性能和可用性。
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 026724b6c9636a0c4b89ca8390a997d9640be6ec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582232"
---
# <a name="monitor-azure-cosmos-db"></a>监视 Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍 Azure Cosmos 数据库生成的监视数据，以及如何使用 Azure Monitor 的功能在此数据的基础上进行分析和发出警报。

可以通过客户端和服务器端指标监视数据。 使用服务器端指标时，可以使用以下选项监视存储在 Azure Cosmos DB 中的数据：

* **通过 Azure Cosmos DB 门户监视：** 可以使用 Azure Cosmos 帐户的“指标”选项卡中提供的指标进行监视。 此选项卡上的指标包括吞吐量、存储、可用性、延迟、一致性和系统级指标。 默认情况下，这些指标的保留期为 7 天。 若要了解详细信息，请参阅本文的[从 Azure Cosmos DB 中收集的监视数据](#monitoring-from-azure-cosmos-db)部分。

* **使用 Azure Monitor 中的指标进行监视：** 可以监视 Azure Cosmos 帐户的指标，并从 Azure Monitor 中创建仪表板。 默认情况下，Azure Monitor 收集 Azure Cosmos DB 指标，你无需显式配置任何内容。 这些指标以一分钟的粒度进行收集，粒度可能因所选指标而异。 默认情况下，这些指标的保留期为 30 天。 从前面选项中获得的大多数指标也可在这些指标中使用。 指标的维度值（例如容器名称）不区分大小写。 因此，在对这些维度值进行字符串比较时，需要使用不区分大小写的比较。 若要了解详细信息，请参阅本文的[分析指标数据](#analyze-metric-data)部分。

* **使用 Azure Monitor 中的诊断日志进行监视：** 可以监视 Azure Cosmos 帐户的日志，并从 Azure Monitor 中创建仪表板。 以秒粒度发生的遥测（例如，事件和跟踪）将作为日志进行存储。 例如，如果容器的吞吐量发生变化，则 Cosmos 帐户的属性也会发生变化，这些事件将在日志中捕获。 可以通过对收集的数据运行查询来分析这些日志。 若要了解详细信息，请参阅本文的[分析日志数据](#analyze-log-data)部分。

* **使用 SDK 以编程方式监视：** 可以使用 .NET、Java、Python、Node.js SDK 和 REST API 中的标头以编程方式监视 Azure Cosmos 帐户。 若要了解详细信息，请参阅本文的[以编程方式监视 Azure Cosmos DB](#monitor-cosmosdb-programmatically) 部分。

下图显示了可用于通过 Azure 门户监视 Azure Cosmos DB 帐户的不同选项：

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Azure 门户中提供的监视选项" border="false":::

使用 Azure Cosmos DB 时，可以在客户端上收集有关请求费用、活动 ID、异常/堆栈跟踪信息、HTTP 状态/子状态代码、诊断字符串的详细信息，以调试可能发生的任何问题。 如果需要与 Azure Cosmos DB 支持团队联系，也需要这些信息。 

## <a name="monitor-overview"></a>Monitor 概述

Azure 门户中每个 Azure Cosmos DB 帐户的“概述”页面包含资源使用情况的简要视图，例如请求总数、导致特定 HTTP 状态代码的请求，以及每小时计费。 这些信息非常有用，但此页面只提供少量的监视数据。 创建资源后，其中的某些数据会自动收集，并可供分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure Cosmos DB 使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据，后者是 Azure 中的一项完整堆栈监视服务，它提供了一组完整的功能来监视 Azure 资源以及其他云中和本地的资源。

如果你尚不熟悉如何监视 Azure 服务，请从[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)一文着手，其中介绍了以下概念：

* 说明是 Azure Monitor？
* 与监视相关的成本
* 监视 Azure 中收集的数据
* 配置数据收集
* Azure 中用于分析监视数据并就其发出警报的标准工具

本文的以下各部分介绍如何从 Azure Cosmos DB 收集特定数据，并提供了有关使用 Azure 工具配置数据收集和分析此数据的示例。

## <a name="azure-monitor-for-azure-cosmos-db"></a>适用于 Azure Cosmos DB 的 Azure Monitor

适用于 Azure Cosmos DB 的 Azure Monitor 基于 [Azure Monitor 的工作簿功能](../azure-monitor/visualize/workbooks-overview.md)，并使用以下部分中所述的为 Azure Cosmos DB 收集的相同监视数据。 使用 Azure Monitor 以统一的交互式体验查看所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况，并利用 Azure Monitor 的其他功能进行详细分析和发出警报。 若要了解详细信息，请参阅文章[探究适用于 Azure Cosmos DB 的 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md)。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> 监视数据 

Azure Cosmos DB 会收集与[来自 Azure 资源的监视数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同类型的监视数据。 有关 Azure Cosmos DB 创建的日志和指标的详细参考，请参阅 [Azure Cosmos DB 监视数据参考](monitor-cosmos-db-reference.md)。

Azure 门户中每个 Azure Cosmos 数据库的“概述”页都提供数据库使用情况的简要视图，其中包括该数据库的请求和每小时计费。 这些信息非常有用，但只提供少量监视数据。 创建数据库后，系统会立即自动收集上述某些数据并使其可用于分析；同时，你可以使用某项配置启用其他数据收集。

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="概述页":::

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

若要了解使用 Azure 门户创建诊断设置的详细过程以及一些诊断查询示例，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](cosmosdb-monitor-resource-logs.md)。 创建诊断设置时，请指定要收集的日志类别。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> 分析指标

Azure Cosmos DB 提供了一个自定义体验来用于处理指标。 可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析 Azure Cosmos DB 的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 还可以查看如何监视 Azure Cosmos DB 资源的 [服务器端延迟](monitor-server-side-latency.md)、 [请求单位使用情况](monitor-request-unit-usage.md)和 [规范化请求单位使用情况](monitor-normalized-request-units.md) 。

有关为 Azure Cosmos DB 收集的平台指标的列表，请参阅[监视 Azure Cosmos DB 数据参考指标](monitor-cosmos-db-reference.md#metrics)一文。

Azure Cosmos DB 的所有指标都位于命名空间“Cosmos DB 标准指标”中。 在将筛选器添加到图表时，可对这些指标使用以下维度：

* CollectionName
* DatabaseName
* OperationType
* 区域
* StatusCode

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>查看 Azure Cosmos DB 的操作级别指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中选择“监视”，然后选择“指标”。 

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Azure Monitor 中的“指标”窗格":::

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。    对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。  

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="选择 Cosmos DB 帐户以查看指标":::

1. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 和其他方面的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别划分的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，让我们选择“请求单位”和“平均”作为聚合值。

   除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。  可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="从 Azure 门户中选择指标":::

### <a name="add-filters-to-metrics"></a>向指标添加筛选器

还可以按 **CollectionName**、**DatabaseName**、**OperationType**、**Region** 和 **StatusCode** 筛选指标及显示的图表。 若要筛选指标，请选择“添加筛选器”，选择所需的属性（例如 **OperationType**），然后选择一个值（例如 **Query**）。 然后，图表中会显示查询操作在所选时间段内消耗的请求单位数。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="添加筛选器以选择指标粒度":::

可以使用“应用拆分”选项将指标分组。 例如，可按操作类型将请求单位分组，并一次性查看所有操作的图表，如下图所示：

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="添加“应用拆分”筛选器":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> 分析日志

Azure Monitor 日志中的数据以表形式存储，每个表包含自己独有的属性集。

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 有关为 Azure Cosmos DB 收集的资源日志类型的列表，请参阅[监视 Azure Cosmos DB 数据参考](monitor-cosmos-db-reference.md#resource-logs)。

[活动日志](../azure-monitor/platform/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

Azure Cosmos DB 将数据存储在以下表中。

| 表 | 说明 |
|:---|:---|
| AzureDiagnostics | 由多个服务用来存储资源日志的公用表。 可以使用 `MICROSOFT.DOCUMENTDB` 识别来自 Azure Cosmos DB 的资源日志。   |
| AzureActivity    | 用于存储活动日志中所有记录的公用表。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 从 Azure Cosmos DB 菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 Azure Cosmos DB 帐户。 这意味着日志查询只包含来自该资源的数据。 如果你希望运行的查询包含来自其他帐户或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/logs/scope.md)。

可在“日志搜索”搜索栏中输入下面这些查询，以便监视 Azure Cosmos 资源。 这些查询使用[新语言](../azure-monitor/logs/log-query-overview.md)。

* 若要查询指定时间段内来自 Azure Cosmos DB 的所有诊断日志，请执行以下操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* 查询已按资源分组的所有操作：

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 查询已按资源分组的所有用户活动：

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点

例如，下表列出了资源的几个警报规则。 可以从 Azure 门户中找到警报规则的详细列表。 若要了解详细信息，请参阅[如何配置警报](create-alerts.md)一文。  

| 警报类型 | 条件 | 描述  |
|:---|:---|:---|
|请求单位的速率限制（指标警报） |维度名称：StatusCode，运算符：等于，维度值：429  | 如果容器或数据库已超出预配的吞吐量限制，则会发出警报。 |
|已故障转移区域 |运算符：大于，聚合类型：计数，阈值：1 | 单个区域发生故障转移时。 如果未启用自动故障转移，则此警报非常有用。 |
| 轮换密钥（活动日志警报）| 事件级别：信息，状态：已启动| 当轮换帐户密钥时发出警报。 你可以用新密钥更新你的应用程序。 |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> 以编程方式监视 Azure Cosmos DB

门户中可用的帐户级别指标（如帐户存储使用情况和请求总数）不可通过 SQL API 使用。 但是，可以使用 SQL API 在集合级别检索使用情况数据。 若要检索集合级别的数据，请执行以下操作：

* 若要使用 REST API，请[对集合执行 GET](/rest/api/cosmos-db/get-a-collection)。 集合的配额和使用情况信息返回到响应中的 x-ms-resource-quota 和 x-ms-resource-usage 标头中。

* 要使用 .NET SDK，请使用 [DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) 方法，它返回 [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1)，其中包含大量使用情况属性，例如 **CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** 等。

若要访问其他指标，请使用 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)。 可以通过调用以下命令检索可用的指标定义：

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

若要检索单个指标，请使用以下格式：

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

若要了解详细信息，请参阅 [Azure 监视 REST API](../azure-monitor/essentials/rest-api-walkthrough.md) 一文。

## <a name="next-steps"></a>后续步骤

* 有关 Azure Cosmos DB 创建的日志和指标的参考，请参阅 [Azure Cosmos DB 监视数据参考](monitor-cosmos-db-reference.md)。
* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。