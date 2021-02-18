---
title: 如何监视 Azure 监控范围
description: 了解如何使用 Azure Monitor 配置 Azure 监控范围指标、警报和诊断设置。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: b13d03f05b7225a1c88f6b10b454749bd7b42477
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588337"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Monitor 中的 Azure 监控范围指标

本文介绍如何使用 Azure Monitor 配置 Azure 监控范围的指标、警报和诊断设置。

## <a name="monitor-azure-purview"></a>监视 Azure 监控范围

Azure 监控范围管理员可以使用 Azure Monitor 来跟踪监控范围帐户的操作状态。 收集度量值是为了为您提供数据点，以便您跟踪潜在问题、排查问题并提高监控范围帐户的可靠性。 对于 Azure 监控范围中发生的事件，会将指标发送到 Azure monitor。

## <a name="aggregated-metrics"></a>聚合指标

可以从监控范围帐户的 Azure 门户访问度量值。 对指标的访问由监控范围帐户的角色分配控制。 用户需要成为 Azure 监控范围中 "监视读取者" 角色的一部分，以查看指标。 查看 [监视读者角色权限](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) ，了解有关角色访问级别的详细信息。

监控范围帐户的创建者会自动获取查看指标的权限。 如果其他人想要查看指标，请按照以下步骤将其添加到 " **监视读取** 者" 角色：

### <a name="add-a-user-to-the-monitoring-reader-role"></a>将用户添加到 "监视读取者" 角色

若要将用户添加到 " **监视读取** 者" 角色，监控范围帐户或订阅所有者的所有者可以执行以下步骤：

1. 中转到 [Azure 门户](https://portal.azure.com) 并搜索 Azure 监控范围帐户名称。

2. 选择“访问控制(IAM)”。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="显示如何访问 IAM 的屏幕截图。":::

3. 选择 " **添加角色分配**"。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="显示如何添加角色分配的屏幕截图。":::

4. 选择角色 **监视读取器** ，并将 "分配访问权限" 设置为 **Azure AD 用户、组或服务主体**。 并分配 AAD 帐户以访问指标。  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="显示如何添加监视读者角色的屏幕截图。":::

## <a name="metrics-visualization"></a>指标可视化

" **监视读取** 者" 角色中的用户可以查看发送到 Azure Monitor 的聚合指标和诊断日志。 相应监控范围帐户的 "Azure 门户中列出了这些指标。 在 Azure 门户中，选择 "指标" 部分以查看所有可用指标的列表。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="显示可用的监控范围指标部分的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure 监控范围用户还可以直接从 Azure 监控范围帐户的管理中心访问 "指标" 页。 在监控范围管理中心的主页中选择 "Azure Monitor"，以启动 Azure 门户。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="用于从管理中心启动监控范围指标的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>可用度量值

若要获取有关如何使用 "指标" Azure 门户部分的熟悉，请阅读以下两篇文档。 指标资源管理[器](../azure-monitor/essentials/metrics-getting-started.md)和[指标资源管理器高级功能](../azure-monitor/essentials/metrics-charts.md)入门。

下表包含可用于在 Azure 门户中浏览的指标列表：

| 标准名称 | 指标命名空间 | 聚合类型 | 说明 |
| ------------------- | ------------------- | ------------------- | ----------------- |
| 扫描已取消 | 自动扫描 | Sum <br> 计数 | 聚合经过一段时间后取消的数据源扫描 |
| 已完成扫描数 | 自动扫描 | Sum <br> 计数 | 聚合经过一段时间后完成的数据源扫描 |
| 已失败扫描数 | 自动扫描 | Sum <br> 计数 | 在一段时间内聚合失败的数据源扫描 |
| 扫描所用时间 | 自动扫描 | Min <br> Max <br> Sum <br> 平均值 | 聚合经过一段时间后扫描所用的总时间 |

## <a name="diagnostic-logs-to-azure-storage-account"></a>诊断日志到 Azure 存储帐户

原始遥测事件将发出到 Azure Monitor。 可以将事件记录到所选的客户存储帐户以进行进一步分析。 日志的导出是通过 Azure 门户上的监控范围帐户的诊断设置来完成的。

按照以下步骤创建 Azure 监控范围帐户的诊断设置。

1. 按照本文所述，创建新的诊断设置来收集平台日志和指标： [创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/essentials/diagnostic-settings.md)。 仅选择 "Azure 存储帐户" 作为目标。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="显示创建诊断日志的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. 将事件记录到存储帐户。 建议使用专用存储帐户来存档诊断日志。 本文后面 [的创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="显示为诊断日志分配存储帐户的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

允许最多15分钟开始接收新创建的存储帐户中的日志。 [请参阅数据保留和 Azure 存储帐户中的资源日志架构](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)。 配置诊断日志后，事件会流向存储帐户。

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

此事件跟踪扫描生命周期。 扫描操作将按照一系列状态进行，从已排队、正在运行，最后一个 "成功" 结束状态 |失败 |放弃. 为每个状态转换记录事件，事件的架构将具有以下属性。

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

以下部分显示了事件实例的示例日志。

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>后续步骤

[查看资产见解](asset-insights.md)
