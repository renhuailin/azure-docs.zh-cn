---
title: 使用逻辑应用将数据从 Log Analytics 工作区存档到 Azure 存储
description: 介绍一种使用 Azure 逻辑应用从 Log Analytics 工作区查询数据并将数据发送到 Azure 存储的方法。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: fd66aa1f10a32d94d515a1f0aa25c25331796a8d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035681"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>使用逻辑应用将数据从 Log Analytics 工作区存档到 Azure 存储
本文介绍一种使用 [Azure 逻辑应用](../../logic-apps/index.yml)从 Azure Monitor 中的 Log Analytics 工作区查询数据并将其发送到 Azure 存储的方法。 如果需要导出 Azure Monitor 日志数据用于审核和符合性场景或允许其他服务检索此数据，请使用此过程。  

## <a name="other-export-methods"></a>其他导出方法
本文中的方法介绍了一种使用逻辑应用从日志查询进行的计划导出。 针对特定场景导出数据的其他选项包括：

- 若要将所有数据从 Log Analytics 工作区导出到 Azure 存储帐户或事件中心，请使用 Azure Monitor 日志的 Log Analytics 工作区数据导出功能。 请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）](logs-data-export.md)
- 使用逻辑应用一次性导出。 请参阅[适用于逻辑应用和 Power Automate 的 Azure Monitor 日志连接器](logicapp-flow-connector.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [Invoke-AzOperationalInsightsQueryExport]](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) 。

## <a name="overview"></a>概述
此过程使用 [Azure Monitor 日志连接器](/connectors/azuremonitorlogs/)，通过该连接器，可以从逻辑应用运行日志查询，并将其输出用于工作流中的其他操作。 此过程中使用 [Azure Blob 存储连接器](/connectors/azureblob/)，将查询输出发送到 Azure 存储。 其他操作会在以下各部分中介绍。

![逻辑应用概述](media/logs-export-logic-app/logic-app-overview.png)

从 Log Analytics 工作区导出数据时，应筛选和聚合日志数据并优化查询，以将由逻辑应用工作流处理的数据量限制为所需数据。 例如，如果需要对登录事件进行存档，可以使用以下查询筛选所需事件并仅投影所需字段： 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

按计划导出数据时，请在查询中使用 ingestion_time() 函数，确保不会错过晚到达的数据。 如果数据由于网络或平台问题而延迟，使用引入时间可确保将其包含在下一次逻辑应用执行中。 有关示例，请参阅[添加 Azure Monitor 日志操作](#add-azure-monitor-logs-action)。

## <a name="prerequisites"></a>先决条件
以下是完成此过程之前必须完成的先决条件。

- Log Analytics 工作区。 创建逻辑应用的用户必须至少具有工作区的读取权限。 
- Azure 存储帐户。 存储帐户不必与 Log Analytics 工作区在同一订阅中。 创建逻辑应用的用户必须具有存储帐户的写入权限。 


## <a name="connector-limits"></a>连接器限制
Azure Monitor 中的 Log Analytics 工作区和日志查询是多租户服务，其中包括可以保护和隔离客户并保持服务质量的限制。 查询大量数据时，应考虑以下限制，这些限制可能会影响配置逻辑应用定期和日志查询的方式：

- 日志查询不能返回超过 500,000 行。
- 日志查询不能返回超过 64,000,000 字节。
- 默认情况下，日志查询的运行时间不能超过 10 分钟。 
- Log Analytics 连接器限制为每分钟 100 个调用。


## <a name="create-container-in-the-storage-account"></a>在存储帐户中创建容器
使用[创建容器](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)中的过程将容器添加到存储帐户中，以保存导出的数据。 本文中使用的容器名称是 loganalytics-data，但是你可以使用任何名称。


## <a name="create-logic-app"></a>创建逻辑应用

在 Azure 门户中转到“逻辑应用”，然后单击“添加”。  选择用于存储新逻辑应用的“订阅”、“资源组”和“区域”，并为逻辑应用指定唯一的名称。   可以打开 **Log Analytics** 设置来收集有关运行时数据和事件的信息，如 [设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据](../../logic-apps/monitor-logic-apps-log-analytics.md)中所述。 使用 Azure Monitor 日志连接器不需要此设置。

![创建逻辑应用](media/logs-export-logic-app/create-logic-app.png)


单击“查看 + 创建”，然后选择“创建” 。 部署完成后，单击“转到资源”打开“逻辑应用设计器”。 

## <a name="create-a-trigger-for-the-logic-app"></a>为逻辑应用创建触发器
在“从通用触发器开始”下，选择“重复”。  这会创建一个按固定间隔自动运行的逻辑应用。 在操作的“频率”框中选择“天”，然后在“间隔”框中输入 **1** 以每日运行工作流一次。  

![重复操作](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>添加 Azure Monitor 日志操作
单击“+ 新建步骤”，添加在完成重复操作后运行的操作。 在“选择操作”中键入 **azure monitor**，然后选择“Azure Monitor 日志”。 

![Azure Monitor 日志操作](media/logs-export-logic-app/select-azure-monitor-connector.png)

单击 " **Azure Log Analytics –运行查询并列出结果**。

![在逻辑应用设计器的一个步骤中添加新操作的屏幕截图。 Azure Monitor 日志在“选择操作”下突出显示。](media/logs-export-logic-app/select-query-action-list.png)

系统将提示你选择一个租户并向工作流将用于运行查询的帐户授予对 Log Analytics 工作区的访问权限。


## <a name="add-azure-monitor-logs-action"></a>添加 Azure Monitor 日志操作
通过 Azure Monitor 日志操作，可以指定要运行的查询。 此示例中使用的日志查询针对每小时定期进行优化，并收集特定执行时间引入的数据。 例如，如果工作流在 4:35 运行，则时间范围将是 4:00 至 5:00。 如果将逻辑应用更改为以其他频率运行，则也需要更改查询。 例如，如果将定期设置为每天运行，则可以将查询中的 startTime 设置为 (make_datetime(year,month,day,0,0))。 

选择 Log Analytics 工作区的“订阅”和“资源组”。  为“资源类型”选择“Log Analytics 工作区”，然后在“资源名称”下选择工作区的名称。 

将以下日志查询添加到“查询”窗口中。  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

时间范围根据 TimeGenerated 列来指定将包含在查询中的记录 。 应将其设置为等于或大于查询中选择的时间范围的值。 由于此查询未使用 TimeGenerated 列，因此“在查询中设置”选项不可用 。 有关时间范围的更多详细信息，请参阅[查询范围](./scope.md)。 

对于“时间范围”，选择“过去 4 小时” 。 这样可以确保引入时间大于 TimeGenerated 的所有记录都将包含在结果中。
   
![名为“运行查询并将结果可视化”的新 Azure Monitor 日志操作的设置的屏幕截图。](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>添加“分析 JSON”活动（可选）
“运行查询并列出结果”操作的输出采用 JSON 格式。 可以分析此数据并对其进行操作，以准备进行“撰写”操作。 

可以提供一个 JSON 架构用于描述预期要接收的有效负载。 设计器使用此架构分析 JSON 内容，并生成用户友好的标记来表示 JSON 内容中的属性。 然后，你可以在整个逻辑应用工作流中轻松引用和使用这些属性。 


依次单击“+ 新建步骤”、“+ 添加操作” 。 在“选择操作”下，键入“json”，然后选择“分析 JSON”  。

![选择“分析 JSON”活动](media/logs-export-logic-app/select-parse-json.png)

单击“内容”框可显示以前活动的值列表。 从“运行查询并列出结果”操作中选择“正文” 。 这是日志查询的输出。

[![选择“正文”](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  单击“使用示例有效负载生成架构”。 运行日志查询并复制输出以用于示例有效负载。  对于此处的示例查询，可以使用以下输出：


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![分析 JSON 有效负载](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>添加“撰写”操作
“撰写”操作获取已分析的 JSON 输出，并创建需要存储在 blob 中的对象。

依次单击“+ 新建步骤”、“+ 添加操作” 。 在“选择操作”下，键入“撰写”，然后选择“撰写”操作  。

![选择“撰写”操作](media/logs-export-logic-app/select-compose.png)


单击“输入”框，显示以前活动的值列表。 从“分析 JSON”操作中选择“正文” 。 这是日志查询中的已分析输出。

[![选择用于“撰写”操作的正文](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>添加“创建 Blob”操作
“创建 Blob”操作会将撰写的 JSON 写入存储。

依次单击“+ 新建步骤”、“+ 添加操作” 。 在“选择操作”下，键入“blob”，然后选择“创建 Blob”操作  。

![选择“创建 Blob”](media/logs-export-logic-app/select-create-blob.png)

在“连接名称”中键入存储帐户的连接名称，然后单击“文件夹路径”框中的文件夹图标，以在存储帐户中选择容器 。 单击“Blob 名称”，查看以前活动的值列表。 单击“表达式”，然后输入与你的时间间隔匹配的表达式。 对于每小时运行一次的该查询，以下表达式将设置每个前一小时的 blob 名称： 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Blob 表达式](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

单击“Blob 内容”框，显示先前活动的值列表，然后在“撰写”部分中选择“输出”  。


![创建 blob 表达式](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>测试逻辑应用
通过单击“运行”来测试工作流。 如果工作流存在错误，则会在有问题的步骤中指示。 可以查看执行并钻取到每个步骤，通过查看输入和输出来进行故障调查。 如有必要，请参阅[在 Azure 逻辑应用中排查和诊断工作流故障](../../logic-apps/logic-apps-diagnosing-failures.md)。

[![运行历史记录](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>查看存储中的日志
转到 Azure 门户中的“存储帐户”菜单，然后选择你的存储帐户。 单击“Blob”磁贴，然后选择在“创建 blob”操作中指定的容器。 选择一个 blob，然后选择“编辑 blob”。

[![Blob 数据](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的日志查询](./log-query-overview.md)。
- 详细了解[逻辑应用](../../logic-apps/index.yml)
- 详细了解 [Power Automate](https://flow.microsoft.com)。