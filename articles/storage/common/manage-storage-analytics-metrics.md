---
title: 启用和管理 Azure 存储分析指标（经典）| Microsoft Docs
description: 了解如何启用、编辑和查看 Azure 存储分析指标。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-azurepowershell
ms.openlocfilehash: 5c652696d825b6dae4665de1d8afdd9bae6848a4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183525"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>启用和管理 Azure 存储分析指标（经典）

[Azure 存储分析](storage-analytics.md)提供了有关 Blob、队列和表的所有存储服务的指标。 你可以使用 [Azure 门户](https://portal.azure.com)来配置要为帐户记录哪些指标，并配置图表来提供指标数据的可视化表示形式。 本文介绍了如何启用和管理指标。 若要了解如何启用日志，请参阅[启用和管理 Azure 存储分析日志（经典）](manage-storage-analytics-logs.md)。

建议查看[用于存储的 Azure Monitor](./storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)（预览版）。 它是 Azure Monitor 的一项功能，通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供对 Azure 存储帐户的全面监视。 无需启用或配置任何设置，即可立即从预定义的交互式图表和包含的其他可视化效果中查看这些指标。

> [!NOTE]
> 在 Azure 门户中检查监视数据会产生相关的费用。 有关详细信息，请参阅[存储分析](storage-analytics.md)。
>
> 高级性能块 blob 存储帐户不支持存储分析指标。 若要查看高级性能块 blob 存储帐户的指标，可以考虑使用 [Azure Monitor 中的 Azure 存储指标](../blobs/monitor-blob-storage.md)。
>
> 有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](storage-monitoring-diagnosing-troubleshooting.md)。
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>启用指标

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中选择“存储帐户”，并单击存储帐户名称打开帐户仪表板。

2. 在菜单边栏选项卡的“监视(经典)”部分中选择“诊断设置(经典)”。
   
   ![屏幕截图，突出显示了“监视(经典)”部分下的“诊断设置(经典)”选项。](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. 选择要监视的每个服务的指标数据类型，以及数据的保留策略。 还可以通过将“状态”设置为“关闭”来禁用监视。

   > [!div class="mx-imgBorder"]
   > ![在 Azure 门户中配置日志记录。](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   若要设置数据保留策略，请移动“保留期(天)”滑块，或输入数据的保留天数（1 到 365 天）。 新存储帐户的默认保留期为 7 天。 如果不需要设置保留策略，请输入零。 如果没有保留策略，则由用户自行决定是否删除监视数据。

   > [!WARNING]
   > 指标作为数据存储在你的帐户中。 指标数据会随着时间的推移在你的帐户中累积，这可能会增加存储成本。 如果只需要一小段时间的指标数据，则可以通过修改数据保留策略来降低成本。 陈旧的指标数据（超出保留策略的数据）将被系统删除。 建议根据要将帐户的指标数据保留多长时间来设置保留策略。 有关详细信息，请参阅[按存储指标计费](storage-analytics-metrics.md#billing-on-storage-metrics)。
   >

4. 完成监视配置后，选择“保存”。

“概览”边栏选项卡以及“指标(经典)”边栏选项卡上的图表中会显示默认的一组指标。 启用服务的指标后，最长可能需要一小时，数据才会显示在其图表中。 可以在任何指标图表中选择“编辑”，配置要在图表中显示的指标。

将“状态”设置为“关闭”可以禁用指标收集和日志记录。

> [!NOTE]
> Azure 存储使用[表存储](storage-introduction.md#table-storage)来存储存储帐户的指标，将指标存储在帐户中的表内。 有关详细信息，请参阅： [指标的存储方式](storage-analytics-metrics.md#how-metrics-are-stored)。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 打开 Windows PowerShell 命令窗口。

2. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

3. 如果你的标识关联到多个订阅，请设置你的活动订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

5. 获取定义了要使用的存储帐户的存储帐户上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 将 `<resource-group-name>` 占位符值替换为资源组的名称。

   * 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。 

6. 可以使用本地计算机上的 PowerShell 在存储帐户中配置存储指标。 使用 Azure PowerShell cmdlet“Set-AzStorageServiceMetricsProperty”更改当前设置。 

   以下命令在存储帐户中开启 blob 服务的分钟指标，并将保留期设置为五天。

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   此 cmdlet 使用以下参数：  

   - ServiceType：可能值为 Blob、Queue、Table 和 File。
   - MetricsType：可能值为 Hour 和 Minute。  
   - MetricsLevel：可能的值包括：
      - **无**：禁用监视。
      - **服务**：收集经过汇总的有关 Blob、队列、表和文件服务的入口和出口、可用性、延迟及成功百分比等指标。
      - ServiceAndApi：除服务指标外，在 Azure 存储服务 API 中为每项存储操作收集一组相同的指标。

   以下命令在默认存储帐户中为 Blob 服务检索当前的小时度量值级别和保留天数：  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[安装和配置 Azure PowerShell](/powershell/azure/)。  

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

有关使用 .NET 语言配置存储指标的详细信息，请参阅[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)。  

有关使用 REST API 配置存储指标的一般信息，请参阅[启用和配置存储分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。 

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

有关使用 .NET 语言配置存储指标的详细信息，请参阅[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)。  

有关使用 REST API 配置存储指标的一般信息，请参阅[启用和配置存储分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>在图表中查看指标

在将存储分析指标配置为监视存储帐户后，存储分析将使用存储帐户在一组已知表中记录指标。 可以在 [Azure 门户](https://portal.azure.com)中配置图表以查看每小时指标。

使用以下过程选择要在指标图表中查看哪些存储指标。

1. 首先在 Azure 门户中显示存储指标图表。 可以在“存储帐户”边栏选项卡和“指标（经典）”边栏选项卡上找到图表。

   本示例使用“存储帐户边栏选项卡”上显示的以下图表：

   ![在 Azure 门户中选择图表](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. 单击图表中的任意位置以进行编辑。

3. 接下来，选择要在图表中显示的指标“时间范围”，以及要显示其指标的“服务”（Blob、队列、表或文件） 。 此处已选择要显示 Blob 服务在过去一周的指标：

   ![在“编辑图表”边栏选项卡中选择时间范围和服务](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. 选择要在图表中显示的各个指标，然后单击“确定”。

   ![在“编辑图表”边栏选项卡选择单个指标](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

图表设置不会影响存储帐户中监视数据的收集、聚合或存储。

#### <a name="metrics-availability-in-charts"></a>图表中可用的指标

可用指标列表根据在下拉列表中选择的服务，以及要编辑的图表单位类型而异。 例如，仅当所要编辑的图表显示百分比单位时，才能选择 PercentNetworkError 和 PercentThrottlingError 等百分比指标：

![Azure 门户中的请求错误百分比图表](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>指标解析

在“诊断”中选择的指标决定了可用于帐户的指标的解析：

* “聚合”监视提供入口/出口、可用性、延迟和成功百分比等指标。 系统将从 Blob、表、文件和队列服务聚合这些指标。
* “按 API”除了提供服务级别的聚合外，还提供更精细的解析，包括可用于单个存储操作的指标。

## <a name="download-metrics-to-archive-or-analyze-locally"></a>下载指标以在本地进行存档或分析

如果要为长期存储下载指标或在本地分析这些指标，则需要使用工具或编写一些代码来读取表。 如果在存储帐户中列出所有表，则这些表不会显示，但可以按名称直接访问。 很多存储浏览工具可识别这些表，并可用于直接查看这些表。 有关可用工具的列表，请参阅 [Azure 存储客户端工具](./storage-explorers.md)。

|指标|表名|备注| 
|-|-|-|  
|小时指标|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在 2013 年 8 月 15 日之前的版本中，这些表称为：<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 从 2015 年 4 月 5 日的版本开始，可以使用文件服务指标。|  
|分钟度量值|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只能通过 PowerShell 或以编程方式启用。<br /><br /> 从 2015 年 4 月 5 日的版本开始，可以使用文件服务指标。|  
|容量|$MetricsCapacityBlob|仅限 Blob 服务。|  

有关这些表的完整架构详细信息，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。 以下示例行仅显示一部分可用列，但也说明了存储指标在采用相应方式保存这些指标时展现的一些重要功能：  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|可用性|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.765025Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.764025Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.765025Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.765025Z|1|1|771|217|100|9|6|100|  

在这个分钟指标数据示例中，分区键按分钟使用时间。 行键用于标识行中所存储信息的类型。 行键由访问类型和请求类型组成：  

-   访问类型是 user 或 system，其中 user 是指用户对存储服务发出的所有请求，而 system 是指存储分析发出的请求。  
-   请求类型是 all（在这种情况下是摘要行）或可识别特定 API，如 QueryEntity 或 UpdateEntity。  

此示例数据显示一分钟的所有记录（从上午 11:00 开始），因此，QueryEntities 请求数加 QueryEntity 请求数再加 UpdateEntity 请求数的和为 7。 这是显示在 user:All 行上的总数。 同样，通过计算 ((143.8 * 5) + 3 + 9)/7，可以在 user:All 行得到平均端到端延迟为 104.4286。  

## <a name="view-metrics-data-programmatically"></a>以编程方式查看指标数据

以下列表显示示例 C# 代码，该代码用于访问分钟范围的分钟指标，并在控制台窗口中显示结果。 代码示例使用 Azure 存储客户端库版本 4.x 或更高版本，其中包括 CloudAnalyticsClient 类，用于简化访问存储中的指标表的过程。 

> [!NOTE]
> 适用于 .NET 的 Azure Blob 存储客户端库 v12 中不包含 CloudAnalyticsClient 类。 存储分析指标（也称为经典指标）将于 2023 年 8 月 31 日停用。 有关详细信息，请查看[官方公告](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)。 如果使用经典指标，建议你在该日期之前转换为 Azure Monitor 中的指标。 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>将指标图表添加到门户仪表板

可将任何存储帐户的 Azure 存储指标图表添加到门户仪表板。

1. 在 [Azure 门户](https://portal.azure.com)中查看仪表板时，单击“编辑仪表板”。
1. 在“磁贴库”中，选择“查找磁贴依据” > “类型”。
1. 选择“类型” > “存储帐户”。
1. 在“资源”中，选择要将其指标添加到仪表板的存储帐户。
1. 选择“类别” > “监视”。
1. 将图表磁贴拖放到要显示的指标所在的仪表板中。 针对要在仪表板上显示的所有指标重复上述步骤。 在下图中，为了方便演示，已突出显示“Blob - 请求总数”图表，但可将所有图表放置在仪表板上。

   ![在 Azure 门户中的磁贴库](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. 添加完图表后，请选择仪表板顶部附近的“完成自定义”。

将图表添加到仪表板后，可以根据“自定义指标图表”所述进一步自定义这些图表。

## <a name="next-steps"></a>后续步骤

* 若要详细了解存储分析，请参阅[存储分析](storage-analytics.md)。
* [配置存储分析日志](manage-storage-analytics-logs.md)。
* 详细了解指标架构。 请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。