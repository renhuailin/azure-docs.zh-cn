---
title: 如何监视 Azure Purview
description: 了解如何使用 Azure Monitor 来配置 Azure Purview 指标、警报和诊断设置。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 7028bb42481c2559d7aa9d044bd25771efc41f31
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213719"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Monitor 中的 Azure Purview 指标

本文介绍了如何使用 Azure Monitor 来配置 Azure Purview 指标、警报和诊断设置。

## <a name="monitor-azure-purview"></a>监视 Azure Purview

Azure Purview 管理员可以使用 Azure Monitor 来跟踪 Purview 帐户的运行状态。 收集指标是为了提供数据点，以便你跟踪潜在问题、进行故障排除并提高 Purview 帐户的可靠性。 指标被发送到 Azure Monitor，以监视 Azure Purview 中发生的事件。

## <a name="aggregated-metrics"></a>聚合指标

可以从 Azure 门户访问 Purview 帐户指标。 对指标的访问权限由 Purview 帐户的角色分配控制。 用户需要成为 Azure Purview 中的“监视读取者”角色的一部分，才能看到这些指标。 若要详细了解角色访问级别，请查看[“监视读取者”角色权限](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)。

Purview 帐户的创建者会自动获取查看指标的权限。 如果其他人想要查看指标，请按照以下步骤操作将其添加到“监视读取者”角色中：

### <a name="add-a-user-to-the-monitoring-reader-role"></a>向“监视读取者”角色添加用户

若要将用户添加到“监视读取者”角色中，Purview 帐户所有者或订阅所有者可以按下面的步骤操作：

1. 转到 [Azure 门户](https://portal.azure.com)，然后搜索 Azure Purview 帐户名。

2. 选择“访问控制 (IAM)”。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="展示了如何访问 IAM 的屏幕截图。":::

3. 选择“添加角色分配”。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="展示了如何添加角色分配的屏幕截图。":::

4. 选择“监视读取者”角色，并将“访问权限分配对象”设置为“Azure AD 用户、组或服务主体”。 同时，分配 AAD 帐户来访问指标。  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="展示了如何添加“监视读取者”角色的屏幕截图。":::

## <a name="metrics-visualization"></a>指标可视化

“监视读取者”角色中的用户可以查看发送到 Azure Monitor 的聚合指标和诊断日志。 在 Azure 门户中列出了相应 Purview 帐户的指标。 在 Azure 门户中，选择“指标”部分即可看到所有可用指标的列表。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="展示了可用 Purview“指标”部分的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure Purview 用户也可以直接从 Azure Purview 帐户的管理中心访问“指标”页。 在 Purview 管理中心的主页中，选择“Azure Monitor”可以启动 Azure 门户。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="展示了如何从管理中心启动 Purview 指标的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>可用度量值

若要熟悉如何使用 Azure 门户的“指标”部分，请先阅读以下两篇文档。 [指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)和[指标资源管理器的高级功能](../azure-monitor/essentials/metrics-charts.md)。

下表列出了可以在 Azure 门户中浏览的指标：

| 标准名称 | 指标命名空间 | 聚合类型 | 说明 |
| ------------------- | ------------------- | ------------------- | ----------------- |
| 已取消扫描数 | 自动扫描 | Sum <br> 计数 | 聚合一段时间内取消的数据源扫描数 |
| 已完成扫描数 | 自动扫描 | Sum <br> 计数 | 聚合一段时间内完成的数据源扫描数 |
| 已失败扫描数 | 自动扫描 | Sum <br> 计数 | 聚合一段时间内失败的数据源扫描数 |
| 扫描所用时间 | 自动扫描 | Min <br> Max <br> Sum <br> 平均值 | 聚合一段时间内扫描的总耗时 |

## <a name="diagnostic-logs-to-azure-storage-account"></a>记录到 Azure 存储帐户的诊断日志

原始遥测事件被发送到 Azure Monitor。 可以将事件记录到所选的客户存储帐户，以便进行进一步分析。 Purview 帐户的日志导出是在 Azure 门户上通过 诊断设置完成的。

若要为 Azure Purview 帐户创建诊断设置，请按以下步骤操作。

1. 按照下面这篇文章中的说明操作，新建诊断设置来收集平台日志和指标：[创建诊断设置以将平台日志和指标发送到不同的目标](../azure-monitor/essentials/diagnostic-settings.md)。 只选择 Azure 存储帐户作为目标。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="展示了如何创建诊断日志的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. 将事件记录到存储帐户。 建议使用专用存储帐户来存档诊断日志。 按照下面这篇文章中的说明操作来创建存储帐户：[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="展示了如何为诊断日志分配存储帐户的屏幕截图。" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

允许最长 15 分钟来开始在新创建的存储帐户中接收日志。 [查看 Azure 存储帐户中资源日志的数据保留和架构](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。 在配置诊断日志后，事件会流向存储帐户。

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

此事件跟踪扫描生命周期。 扫描操作遵循一系列状态进行，包括“已排队”、“正在运行”，以及最后的最终状态“成功”|“失败”|“已取消”。 每个状态转换都会记录一个事件，事件架构将具有以下属性。

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

下面的部分展示了事件实例的示例日志。

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