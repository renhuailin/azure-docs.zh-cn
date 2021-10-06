---
title: 使用 Azure 数据资源管理器查询从 Azure Monitor 中导出的数据
description: 使用 Azure 数据资源管理器可查询从 Log Analytics 工作区导出到 Azure 存储帐户的数据。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b50badbf1353ffbb4c5f6fb347cb3c8b1168c97c
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407069"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer"></a>使用 Azure 数据资源管理器查询从 Azure Monitor 中导出的数据
将数据从 Azure Monitor 导出到 Azure 存储帐户可以实现低成本保留，并能够将日志重新分配到不同的区域。 使用 Azure 数据资源管理器可查询从 Log Analytics 工作区导出的数据。 配置后，从你的工作区发送到 Azure 存储帐户的受支持的表将可用作 Azure 数据资源管理器的数据源。

处理流如下： 

1.  将数据从 Log Analytics 工作区导出到 Azure 存储帐户。
2.  在 Azure 数据资源管理器群集中创建外部表和数据类型映射。
3.  从 Azure 数据资源管理器查询数据。

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure 数据资源管理器导出的数据查询流。":::



## <a name="send-data-to-azure-storage"></a>将数据发送到 Azure 存储
可以使用以下任一选项将 Azure Monitor 日志导出到 Azure 存储帐户。

- 若要将所有数据从 Log Analytics 工作区导出到 Azure 存储帐户或事件中心，请使用 Azure Monitor 日志的 Log Analytics 工作区数据导出功能。 请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出功能](./logs-data-export.md)
- 使用逻辑应用从日志查询进行计划性导出。 这类似于数据导出功能，但你可向 Azure 存储发送经过筛选或聚合的数据。 不过，此方法受限于[日志查询限制](../service-limits.md#log-analytics-workspaces)。请参阅[使用逻辑应用将 Log Analytics 工作区中的数据存档到 Azure 存储](./logs-export-logic-app.md)。
- 使用逻辑应用一次性导出。 请参阅[适用于逻辑应用和 Power Automate 的 Azure Monitor 日志连接器](./logicapp-flow-connector.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。

> [!TIP]
> 可以使用现有的 Azure 数据资源管理器群集，或使用所需的配置创建新的专用群集。

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>创建位于 Azure Blob 存储中的外部表
使用[外部表](/azure/data-explorer/kusto/query/schema-entities/externaltables)将 Azure 数据资源管理器链接到 Azure 存储帐户。 外部表是引用存储在 Kusto 数据库外部的数据的 Kusto 架构实体。 与表一样，外部表具有定义明确的架构。 与表不同的是，数据是在 Kusto 群集外部进行存储和管理的。 从之前部分导出的数据保存在 JSON 行中。

若要创建引用，你需要导出表的架构。 使用 Log Analytics 中的 [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 运算符来检索此信息，其中包括表的列及其数据类型。

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics 表架构。":::

现在可以使用输出创建 Kusto 查询以构建外部表。
按照[在 Azure 存储或 Azure Data Lake 中创建和更改外部表](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)中的指导，以 JSON 格式创建外部表，然后从 Azure 数据资源管理器数据库运行该查询。

>[!NOTE]
>外部表的创建由两个进程构建。 第一个是创建外部表，第二个是创建映射。

下面的 PowerShell 脚本将为表和映射创建 [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) 命令。

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } elseif ($record.DataType -eq 'System.Int32') {
        $dataType = 'int32'
    } elseif ($record.DataType -eq 'System.Double') {
        $dataType = 'double'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName.ToLower(), $resourcegroupname.ToLower(),$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

下图显示了输出示例。

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable 创建命令输出。":::

[![示例输出](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* 复制、粘贴，然后在 Azure 数据资源管理器客户端工具中运行脚本的输出，以创建表和映射。
>* 若要使用容器内的所有数据，请更改脚本并将 URL 更改为“https://your.blob.core.windows.net/containername;SecKey”

## <a name="query-the-exported-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器查询导出的数据 

配置映射后，可以通过 Azure 数据资源管理器查询导出的数据。 查询需要 [external_table](/azure/data-explorer/kusto/query/externaltablefunction) 函数，如以下示例中所示。

```kusto
external_table("HBTest","map") | take 10000
```

[![查询 Log Analytics 导出的数据](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure 数据资源管理器中编写查询](/azure/data-explorer/write-queries)
