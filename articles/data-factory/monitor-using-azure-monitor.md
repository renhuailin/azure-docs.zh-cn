---
title: 使用 Azure Monitor 监视数据工厂
description: 了解如何在 Azure Monitor 中，使用数据工厂中的信息通过启用诊断日志来监视 Azure 数据工厂管道。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 3029f7756d50e509d7bd539dc5fde8de8a075424
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122272206"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>使用 Azure Monitor 监视数据工厂和发警报

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

云应用程序比较复杂，包含很多移动部件。 监视功能可以提供数据来确保应用程序始终处于正常运行状态。 监视功能还有助于避免潜在问题，或者排查以往的问题。 可以使用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性。 此外，还有助于实现本来需要手动干预的操作的自动化。

Azure Monitor 针对大多数 Azure 服务提供基本级别的基础结构指标和日志。 Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 Azure 数据工厂 (ADF) 可以在 Azure Monitor 中写入诊断日志。 有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

有关详细信息，请参阅 [Azure Monitor 概述](../azure-monitor/overview.md)。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>保留 Azure 数据工厂指标和管道运行数据

数据工厂仅将管道运行数据存储 45 天。 若要将这些数据保留更长时间，请使用 Azure Monitor。 使用 Monitor，可以将诊断日志路由到多个不同目标进行分析。

* **存储帐户**：将诊断日志保存到存储帐户进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* **事件中心**：将日志流式传输到 Azure 事件中心。 日志可用作合作伙伴服务/自定义分析解决方案（例如 Power BI）的输入。
* **Log Analytics**：使用 Log Analytics 分析日志。 在以下情况下，将数据工厂与 Azure Monitor 集成非常有用：
  * 需要针对由数据工厂发布到 Monitor 的丰富指标集编写复杂查询。 可以通过 Monitor 创建针对这些查询的自定义警报。
  * 你希望跨数据工厂进行监视。 可将来自多个数据工厂的数据路由到单个 Monitor 工作区。

还可使用与发出日志的资源不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 访问权限。

## <a name="configure-diagnostic-settings-and-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在门户中，转到“监视”。 选择“设置” > “诊断设置”。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂不存在任何设置，则系统会提示你创建设置。 选择“启用诊断”。

   ![如果不存在任何设置，请创建一个诊断设置](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果数据工厂中存在现有设置，你将看到数据工厂中已配置的设置列表。 选择“添加诊断设置”。

   ![如果存在设置，则添加诊断设置](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 为设置指定名称，选择“发送到 Log Analytics”，然后从 **Log Analytics 工作区** 中选择一个工作区。

    * 在“Azure 诊断”模式下，诊断日志流入 AzureDiagnostics 表中。

    * 在“特定于资源”模式下，来自 Azure 数据工厂的诊断日志流入下表中：
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      你可以选择与工作负荷相关的各种日志，以发送到 Log Analytics 表。 例如，如果你根本不使用 SQL Server Integration Services (SSIS)，就不需要选择任何 SSIS 日志。 如果要记录 SSIS Integration Runtime (IR) 的启动/停止/维护操作，则可以选择 SSIS IR 日志。 如果在 SQL Server Management Studio (SSMS)、SQL Server 代理或其他指定工具上通过 T-SQL 调用 SSIS 包执行，则可以选择 SSIS 包日志。 如果通过 ADF 管道中的“执行 SSIS 包”活动调用 SSIS 包执行，则可以选择所有日志。

    * 如果选择 AllMetrics，则可使用多种 ADF 指标来进行监视或发出警报，包括针对 ADF 活动、管道和触发器运行的指标以及针对 SSIS IR 操作和 SSIS 包执行的指标。

   ![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > 由于 Azure 日志表的列数不能超过 500，因此强烈建议选择“特定于资源”模式。 有关详细信息，请参阅 [Azure 诊断日志参考](/azure/azure-monitor/reference/tables/azurediagnostics)。

1. 选择“保存”。

几分钟后，新设置将出现在此数据工厂的设置列表中。 生成新的事件数据后，诊断日志将立即流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>从 Azure 市场安装 Azure 数据工厂分析解决方案

此解决方案提供数据工厂总体运行状况的摘要，并提供用于钻取详细信息以及排查意外行为模式的选项。 使用丰富的现成视图可以洞察关键的处理活动，包括：

* 数据工厂管道、活动和触发器运行的一览式摘要
* 可以按类型钻取到数据工厂活动运行
* 数据工厂中最常出现的管道和活动错误的摘要

1. 转到“Azure 市场”，选择“分析”筛选器，然后搜索“Azure 数据工厂分析(预览版)”  

   ![转到“Azure 市场”，输入“分析筛选器”，然后选择“Azure 数据工厂分析(预览版)”](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. 有关“Azure 数据工厂分析(预览版)”的详细信息

   ![有关“Azure 数据工厂分析(预览版)”的详细信息](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. 选择“创建”，然后创建或选择“Log Analytics 工作区”。 

   ![创建新解决方案](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>监视数据工厂指标

安装此解决方案会在所选 Log Analytics 工作区的工作簿部分中创建一组默认视图。 因此，将启用以下指标：

* ADF 运行 - 1) 数据工厂中的管道运行
* ADF 运行 - 2) 数据工厂中的活动运行
* ADF 运行 - 3) 数据工厂中的触发器运行
* ADF 错误 - 1) 数据工厂中最常出现的 10 个管道错误
* ADF 错误 - 2) 数据工厂中最常出现的 10 个活动运行
* ADF 错误 - 3) 数据工厂中最常出现的 10 个触发器错误
* ADF 统计信息 - 1) 按类型列出的活动运行
* ADF 统计信息 - 2) 按类型列出的触发器运行
* ADF 统计信息 - 3) 最长管道运行持续时间

![突出显示了“工作簿(预览版)”和“AzureDataFactoryAnalytics”的窗口](media/data-factory-monitor-oms/monitor-oms-image6.png)

可将上述指标可视化，查看这些指标背后的查询，编辑查询，创建警报，以及执行其他操作。

![数据工厂中的管道运行的图形表示形式](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 数据工厂分析（预览版）将诊断日志发送到“资源特定”目标表。 可以针对以下表编写查询：_ADFPipelineRun_、_ADFTriggerRun_ 和 _ADFActivityRun_。

## <a name="data-factory-metrics"></a>数据工厂指标

使用 Monitor 可以洞察 Azure 工作负荷的性能与运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

下面是 Azure 数据工厂版本 2 发出的一些指标：

| **指标**                           | **指标显示名称**                  | **单位** | **聚合类型** | **说明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 已取消的活动运行指标数           | 计数    | 总计                | 在一分钟时段内取消的活动运行总数。 |
| ActivityFailedRuns                   | 失败的活动运行数指标             | 计数    | 总计                | 在一分钟时段内失败的活动运行总数。 |
| ActivitySucceededRuns                | 成功的活动运行数指标          | 计数    | 总计                | 在一分钟时段内成功的活动运行总数。 |
| PipelineCancelledRuns                 | 已取消的管道运行指标数           | 计数    | 总计                | 在一分钟时段内取消的管道运行总数。 |
| PipelineFailedRuns                   | 失败的管道运行数指标             | 计数    | 总计                | 在一分钟时段内失败的管道运行总数。 |
| PipelineSucceededRuns                | 成功的管道运行数指标          | 计数    | 总计                | 在一分钟时段内成功的管道运行总数。 |
| TriggerCancelledRuns                  | 已取消的触发器运行指标数            | 计数    | 总计                | 在一分钟时段内取消的触发器运行总数。 |
| TriggerFailedRuns                    | 失败的触发器运行数指标              | 计数    | 总计                | 在一分钟时段内失败的触发器运行总数。 |
| TriggerSucceededRuns                 | 成功的触发器运行数指标           | 计数    | 总计                | 在一分钟时段内成功的触发器运行总数。 |
| SSISIntegrationRuntimeStartCancelled  | 已取消的 SSIS 集成运行时启动指标           | 计数    | 总计                | 在一分钟时段内取消的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStartFailed    | 失败的 SSIS 集成运行时启动指标             | 计数    | 总计                | 在一分钟时段内失败的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStartSucceeded | 成功的 SSIS 集成运行时启动指标          | 计数    | 总计                | 在一分钟时段内成功的 SSIS 集成运行时启动总数。 |
| SSISIntegrationRuntimeStopStuck      | 停滞的 SSIS 集成运行时停止指标               | 计数    | 总计                | 在一分钟时段内停滞的 SSIS 集成运行时停止总数。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功的 SSIS 集成运行时停止指标           | 计数    | 总计                | 在一分钟时段内成功的 SSIS 集成运行时停止总数。 |
| SSISPackageExecutionCancelled         | 已取消的 SSIS 包执行指标  | 计数    | 总计                | 在一分钟时段内取消的 SSIS 包执行总数。 |
| SSISPackageExecutionFailed           | 失败的 SSIS 包执行指标    | 计数    | 总计                | 在一分钟时段内失败的 SSIS 包执行总数。 |
| SSISPackageExecutionSucceeded        | 已成功的 SSIS 包执行指标 | 计数    | 总计                | 在一分钟时段内成功的 SSIS 包执行总数。 |

若要访问指标，请参阅 [Azure Monitor 数据平台](../azure-monitor/data-platform.md)中的说明。

> [!NOTE]
> 仅发出已完成和已触发的活动以及管道运行事件。 不会发出正在进行的运行和调试运行。 另一方面，将发出所有 SSIS 包执行事件，包括已完成和正在进行的事件，而无论使用何种调用方法。 例如，可以在支持 Azure 的 SQL Server Data Tools (SSDT) 中、在 SSMS、SQL Server 代理或其他指定工具中通过 T-SQL，或者作为 ADF 管道中“执行 SSIS 包”活动的已触发运行或调试运行来调用包执行。

## <a name="data-factory-alerts"></a>数据工厂警报

登录到 Azure 门户，选择“Monitor” > “警报”以创建警报。

![门户菜单中的警报](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报。

    ![新建警报规则](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有”。

    ![“定义警报条件”>“选择目标”，此时会打开“选择资源”窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![“定义警报条件”>“添加条件”，此时会打开“配置信号逻辑”窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![“配置信号类型”窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定义警报详细信息。

    ![警报详细信息](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定义操作组。

    ![创建规则的屏幕截图，其中突出显示了“新建操作组”](media/monitor-using-azure-monitor/alerts_image9.png)

    ![创建新的操作组](media/monitor-using-azure-monitor/alerts_image10.png)

    ![配置电子邮件、短信、推送和语音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![删除操作组](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 设置诊断日志

### <a name="diagnostic-settings"></a>诊断设置

可以使用诊断设置来配置非计算资源的诊断日志。 用于资源控制的诊断设置具有以下功能：

* 指定要将诊断日志发送到何处。 例如，发送到 Azure 存储帐户、Azure 事件中心或 Monitor 日志。
* 指定要发送的日志类别。
* 指定要将每个日志类别保留在存储帐户中多长时间。
* 保留期为 0 天表示永久保留日志。 如果不需要永久保留，可将该值设置为 1 到 2,147,483,647 的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择了“事件中心”或“Monitor 日志”选项，此可能会发生这种情况。
* 保留策略按天应用。 一天的结束时间可能出现在协调世界时 (UTC) 的午夜。 在一天结束时，会删除当天已超过保留策略期限的日志。 例如，如果保留策略的期限为一天，则在今天开始时，会删除前天的日志。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 启用诊断日志

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在 Monitor REST API 中创建或更新诊断设置

##### <a name="request"></a>请求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-vs-authorization.md)。

##### <a name="body"></a>正文

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| **storageAccountId** |String | 要将诊断日志发送到的存储帐户的资源 ID。 |
| **serviceBusRuleId** |String | 服务总线命名空间的服务总线规则 ID。你要在该服务总线命名空间中创建事件中心，以便流式传输诊断日志。 规则 ID 的格式为 `{service bus resource ID}/authorizationrules/{key name}`。|
| **workspaceId** | 字符串 | 将在其中保存日志的工作区的工作区 ID。 |
|**指标**| 要传递到被调用管道的管道运行的参数值| 将参数名映射为自变量值的 JSON 对象。 |
| **logs**| 复杂类型| 某个资源类型的诊断日志类别的名称。 若要获取资源的诊断日志类别列表，请先执行 GET 诊断设置操作。 |
| **category**| String| 日志类别及其保留策略的数组。 |
| **timeGrain** | String | 以 ISO 8601 持续时间格式捕获的指标的粒度。 该属性值必须为 `PT1M`（1 分钟）。 |
| **enabled**| 布尔 | 指定是否为此资源启用了该指标或日志类别的收集。 |
| **retentionPolicy**| 复杂类型| 描述指标或日志类别的保留策略。 此属性仅用于存储帐户。 |
|**days**| int| 指标或日志的保留天数。 如果该属性值为 0，则永久保留日志。 此属性仅用于存储帐户。 |

##### <a name="response"></a>响应

200 正常。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>在 Monitor REST API 中获取有关诊断设置的信息

##### <a name="request"></a>请求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure AD 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-vs-authorization.md)。

##### <a name="response"></a>响应

200 正常。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
有关详细信息，请参阅[诊断设置](/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>日志和事件的架构

### <a name="monitor-schema"></a>监视架构

#### <a name="activity-run-log-attributes"></a>活动运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 活动运行的 ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `ActivityRuns`。 | `ActivityRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 活动的名称及其状态。 如果活动是启动检测信号，则属性值为 `MyActivity -`。 如果活动是结束检测信号，则属性值为 `MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| String | 管道的名称。 | `MyPipeline` |
|**activityName**| String | 活动的名称。 | `MyActivity` |
|**start**| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管道运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 管道的名称及其状态。 管道运行完成后，该属性值为 `Pipeline - Succeeded`。 | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 管道的名称。 | `MyPipeline` |
|**start**| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 管道运行的最终状态。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>触发器运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |String | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **correlationId** |String | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 触发器运行的 ID。 | `08587023010602533858661257311` |
|**resourceId**| String | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**level**| String | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**operationName**| String | 触发器的名称及其最终状态（指示是否已成功激发该触发器）。 如果检测信号成功，则该属性值为 `MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**triggerName**| String | 触发器的名称。 | `MyTrigger` |
|**triggerType**| String | 触发器的类型。 可能的属性值为 `Manual Trigger` 和 `Schedule Trigger`。 | `ScheduleTrigger` |
|**triggerEvent**| String | 触发器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | 激发触发器的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 最终状态（指示是否已成功激发该触发器）。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS Integration Runtime 日志特性

下面是 SSIS IR 启动/停止/维护操作的日志特性。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 属性                   | 类型   | 说明                                                   | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 操作的名称                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | 诊断日志的类别                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | ADF 的名称                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                      | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                  | `Informational` |
| **resultType**             | String | SSIS IR 操作的结果                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | SSIS IR 操作的输出消息                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | ADF 资源的唯一 ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS 事件消息上下文日志特性

下面是与 SSIS IR 上的 SSIS 包执行生成的事件消息相关的条件的日志特性。 它们传递的信息与显示许多 SSIS 包属性的运行时值的 [SSIS 目录 (SSISDB) 事件消息上下文表或视图](/sql/integration-services/system-views/catalog-event-message-context)类似。 这些日志在你选择“`Basic/Verbose`”日志记录级别时生成，可用于调试/合规性检查。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 属性                   | 类型   | 说明                                                          | 示例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此项设置为 `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 诊断日志的类别                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF 的名称                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                             | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                         | `Informational` |
| **operationId**            | String | 用于跟踪 SSISDB 中的特定操作的唯一 ID          | `1`（1 表示与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的操作） |
| **contextDepth**           | String | 事件消息上下文的深度                              | `0`（0 表示在包执行开始之前的上下文，1 表示发生错误时的上下文，该数字将随着上下文离错误的距离的增加而增大） |
| **packagePath**            | String | 作为事件消息上下文源的包对象的路径      | `\Package` |
| **contextType**            | String | 作为事件消息上下文源的包对象的类型      | `60`（请参阅[更多上下文类型](/sql/integration-services/system-views/catalog-event-message-context#remarks)） |
| **contextSourceName**      | String | 作为事件消息上下文源的包对象的名称      | `MyPackage` |
| **contextSourceId**        | String | 作为事件消息上下文源的包对象的唯一 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | 事件消息上下文源的包属性的名称   | `DelayValidation` |
| **propertyValue**          | String | 事件消息上下文源的包属性的值  | `False` |
| **resourceId**             | String | ADF 资源的唯一 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS 事件消息日志特性

下面是通过 SSIS IR 上的 SSIS 包执行操作生成的事件消息的日志特性。 它们传递的信息与 [SSISDB 事件消息表或视图](/sql/integration-services/system-views/catalog-event-messages)类似，后者显示事件消息的详细文本/元数据。 它们可以在除 `None` 之外的任何日志记录级别生成。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| properties                   | 类型   | 说明                                                        | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此项设置为 `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 诊断日志的类别                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF 的名称                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                       | `Informational` |
| **operationId**            | String | 用于跟踪 SSISDB 中的特定操作的唯一 ID        | `1`（1 表示与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的操作） |
| **messageTime**            | 字符串 | 创建事件消息的时间（采用 UTC 格式）          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | 字符串 | 事件消息的类型                                     | `70`（请参阅[更多消息类型](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)） |
| **messageSourceType**      | 字符串 | 事件消息源的类型                              | `20`（请参阅[更多消息源类型](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)） |
| **message**                | String | 事件消息的文本                                     | `MyPackage:Validation has started.` |
| **packageName**            | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **eventName**              | 字符串 | 相关运行时事件的名称                                 | `OnPreValidate` |
| **messageSourceName**      | 字符串 | 作为事件消息源的包组件的名称         | `Data Flow Task` |
| **messageSourceId**        | 字符串 | 作为事件消息源的包组件的唯一 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | 字符串 | 作为事件消息源的数据流组件的名称       | `SSIS.Pipeline` |
| **packagePath**            | String | 作为事件消息源的包对象的路径            | `\Package\Data Flow Task` |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径            | `\Transformation\Data Flow Task`（此路径还捕获组件迭代） |
| **threadId**               | 字符串 | 记录事件消息时执行的线程的唯一 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 可执行文件统计信息日志特性

下面是通过 SSIS IR 上的 SSIS 包执行操作生成的可执行文件统计信息的日志特性，其中的可执行文件是包的控制流中的容器或任务。 它们传递的信息与 [SSISDB 可执行文件统计信息表或视图](/sql/integration-services/system-views/catalog-executable-statistics)类似，后者针对每个运行的可执行文件（包括其迭代）显示一行。 它们可以在除 `None` 之外的任何日志记录级别生成，用于识别任务级瓶颈/故障。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 说明                                                      | 示例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此项设置为 `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 诊断日志的类别                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF 的名称                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                         | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                     | `Informational` |
| **executionId**            | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID      | `1`（1 表示与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的执行） |
| **executionPath**          | 字符串 | 从父包到已执行组件的完整路径          | `\Transformation\Data Flow Task`（此路径还捕获组件迭代） |
| **startTime**              | String | 可执行文件进入执行前阶段的时间（采用 UTC 格式）  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 可执行文件进入执行后阶段的时间（采用 UTC 格式） | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | 字符串 | 可执行文件的运行时间（毫秒）                   | `1,125` |
| **executionResult**        | 字符串 | 运行可执行文件的结果                                 | `0`（0 表示成功，1 表示失败，2 表示完成，3 表示取消） |
| **executionValue**         | 字符串 | 通过运行可执行文件返回的用户定义的值            | `1` |
| **resourceId**             | String | ADF 资源的唯一 ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 执行组件阶段日志特性

下面是通过 SSIS IR 上的 SSIS 包执行操作生成的数据流组件的运行时统计信息的日志特性。 它们传递的信息与 [SSISDB 执行组件阶段表或视图](/sql/integration-services/system-views/catalog-execution-component-phases)类似，后者显示数据流组件在其所有执行阶段中花费的时间。 当你选择 `Performance/Verbose` 日志记录级别时会生成它们，用于捕获数据流执行统计信息。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                   | 类型   | 说明                                                         | 示例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 此项设置为 `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 诊断日志的类别                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 用于跟踪特定操作的唯一 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF 的名称                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR 的名称                                            | `MySSISIR` |
| **level**                  | String | 诊断日志的级别                                        | `Informational` |
| **executionId**            | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID         | `1`（1 表示与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的执行） |
| **packageName**            | 字符串 | 已执行的包文件的名称                              | `MyPackage.dtsx` |
| **taskName**               | 字符串 | 执行的数据流任务的名称                                 | `Data Flow Task` |
| **subcomponentName**       | 字符串 | 数据流组件的名称                                     | `Derived Column` |
| **phase**                  | 字符串 | 执行阶段的名称                                         | `AcquireConnections` |
| **startTime**              | String | 执行阶段的开始时间（采用 UTC 格式）                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 执行阶段的结束时间（采用 UTC 格式）                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | 字符串 | 数据流任务的执行路径                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | ADF 资源的唯一 ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 执行数据统计信息日志特性

下面是通过数据流管道的每个分支进行的从上游组件到下游组件的数据移动的日志特性，这些特性通过 SSIS IR 上的 SSIS 包执行操作生成。 它们传递的信息与 [SSISDB 执行数据统计信息表或视图](/sql/integration-services/system-views/catalog-execution-data-statistics)类似，后者显示通过数据流任务移动的数据的行数。 当你选择 `Verbose` 日志记录级别时会生成它们，用于计算数据流吞吐量。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| properties                     | 类型   | 说明                                                        | 示例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | 事件的时间，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | 此项设置为 `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 诊断日志的类别                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 用于跟踪特定操作的唯一 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | ADF 的名称                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR 的名称                                           | `MySSISIR` |
| **level**                    | String | 诊断日志的级别                                       | `Informational` |
| **executionId**              | 字符串 | 用于跟踪 SSISDB 中的特定执行的唯一 ID        | `1`（1 表示与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的执行） |
| **packageName**              | 字符串 | 已执行的包文件的名称                             | `MyPackage.dtsx` |
| **taskName**                 | 字符串 | 执行的数据流任务的名称                                | `Data Flow Task` |
| **dataflowPathIdString**     | 字符串 | 用于跟踪数据流路径的唯一 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | 字符串 | 数据流路径的名称                                         | `ADO NET Source Output` |
| **sourceComponentName**      | 字符串 | 发送数据的数据流组件的名称                    | `SQLDB Table3` |
| **destinationComponentName** | 字符串 | 接收数据的数据流组件的名称                 | `Derived Column` |
| **rowsSent**                 | 字符串 | 由源组件发送的行数                        | `500` |
| **createdTime**              | 字符串 | 获取行值的时间（采用 UTC 格式）                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | 字符串 | 数据流任务的执行路径                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | ADF 资源的唯一 ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics 架构

Log Analytics 从 Monitor 继承架构，但存在以下例外情况：

* 每个列名中的第一个字母会大写。 例如，Monitor 中的列名“correlationId”在 Log Analytics 中为“CorrelationId”。
* 没有“Level”列。
* 动态“properties”列保留为以下动态 JSON Blob 类型。

    | Azure Monitor 列 | Log Analytics 列 | 类型 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 动态 |
    | $.properties.Annotations | 批注 | 动态 |
    | $.properties.Input | 输入 | 动态 |
    | $.properties.Output | 输出 | 动态 |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | 错误 | 动态 |
    | $.properties.Predecessors | Predecessors | 动态 |
    | $.properties.Parameters | parameters | 动态 |
    | $.properties.SystemParameters | SystemParameters | 动态 |
    | $.properties.Tags | Tags | 动态 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>使用 Azure Monitor 监视 SSIS 操作

要直接迁移 SSIS 工作负载，可以[在 ADF 中预配 SSIS IR](./tutorial-deploy-ssis-packages-azure.md)，以支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配后，可以[通过 Azure PowerShell 或 ADF 门户的“监视器”中心检查 SSIS IR 操作状态](./monitor-integration-runtime.md#azure-ssis-integration-runtime)。 使用项目部署模型时，SSIS 包执行日志存储在 SSISDB 内部表或视图中，因此可以使用 SSMS 之类的指定工具对其进行查询、分析和直观显示。 使用包部署模型时，可以将 SSIS 包执行日志作为 CSV 文件存储在文件系统或 Azure 文件存储中，仍需要使用其他指定工具对这些文件进行分析和处理，然后才能对其进行查询、分析和直观显示。

现在，通过 [Azure Monitor](../azure-monitor/data-platform.md) 集成，可在 Azure 门户上查询、分析和直观显示从 SSIS IR 操作和 SSIS 包执行生成的所有指标和日志。 此外，还可发出相关警报。

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>为 SSIS 操作配置诊断设置和工作区

要将从 SSIS IR 操作和 SSIS 包执行生成的所有指标和日志发送到 Azure Monitor，需要[为 ADF 配置诊断设置和工作区](#configure-diagnostic-settings-and-workspace)。

### <a name="ssis-operational-metrics"></a>SSIS 操作指标

SSIS 操作[指标](../azure-monitor/essentials/data-platform-metrics.md)是性能计数器或数字值，用于描述特定时间点的 SSIS IR 启动和停止操作以及 SSIS 包执行的状态。 它们是 [Azure Monitor 中 ADF 指标](#data-factory-metrics)的一部分。

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，选中“AllMetrics”复选框将使 SSIS 操作指标可用于[使用 Azure 指标资源管理器进行的交互分析](../azure-monitor/essentials/metrics-getting-started.md)、[在 Azure 仪表板上呈现](../azure-monitor/app/tutorial-app-dashboards.md)以及[近实时警报](../azure-monitor/alerts/alerts-metric.md)。

![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 操作警报

若要从 ADF 门户基于 SSIS 操作指标引发警报，请[选择 ADF“监视器”中心的“警报和指标”页面，并按照提供的分步说明进行操作](./monitor-visually.md#alerts)。

![从 ADF 门户引发 SSIS 操作警报](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

若要从 Azure 门户基于 SSIS 操作指标引发警报，请[选择 Azure“监视器”中心的“警报”页面，并按照提供的分步说明进行操作](#data-factory-alerts)。

![从 Azure 门户引发 SSIS 操作警报](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 操作日志

SSIS 操作[日志](../azure-monitor/logs/data-platform-logs.md)是由 SSIS IR 操作和 SSIS 包执行生成的事件，这些事件提供了有关已识别问题的充足上下文，对根本原因分析很有用。 

在 Azure Monitor 上为 ADF 配置诊断设置和工作区时，可以选择相关 SSIS 操作日志，并将其发送到基于 Azure 数据资源管理器的 Log Analytics。 在这里，它们可用于[使用丰富的查询语言进行的分析](../azure-monitor/logs/log-query-overview.md)、[在 Azure 仪表板上呈现](../azure-monitor/app/tutorial-app-dashboards.md)以及[近实时警报](../azure-monitor/alerts/alerts-log.md)。

![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Monitor 和 Log Analytics 中的 SSIS 包执行日志的架构和内容类似于 SSISDB 内部表或视图中的架构。

| Azure Monitor 日志类别          | Log Analytics 表                     | SSISDB 内部表/视图              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

有关 SSIS 操作日志特性/属性的详细信息，请参阅 [Azure Monitor 和 Log Analytics 为 ADF 使用的架构](#schema-of-logs-and-events)。

无论使用哪种调用方法，所选 SSIS 包执行日志始终会发送到 Log Analytics。 例如，可以在支持 Azure 的 SSDT 中、在 SSMS、SQL Server 代理或其他指定工具中通过 T-SQL，或者作为 ADF 管道中“执行 SSIS 包”活动的已触发运行或调试运行来调用包执行。

在 Logs Analytics 上查询 SSIS IR 操作日志时，可以使用 OperationName 和 ResultType 属性，这两个属性分别设置为 `Start/Stop/Maintenance/Heartbeat` 和 `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` 。

![在 Log Analytics 上查询 SSIS IR 操作日志](media/data-factory-monitor-oms/log-analytics-query.png)

若要查询 SSIS IR 节点状态，可以将 OperationName 属性设置为 `Heartbeat`。 通常，每个节点每分钟都会向日志分析发送一个 `Heartbeat` 记录，通过 ResultType 属性反映其状态，当节点可用于包执行时其属性为 `Healthy`，反之则为 `Unhealthy`。 例如，如果 SSIS IR 有两个可用节点，将在任意一个一分钟时间段内始终可以看到两个 ResultType 属性设置为 `Healthy` 的 `Heartbeat` 记录。

![在日志分析上查询 SSIS IR 检测信号](media/data-factory-monitor-oms/log-analytics-query-3.png)

你可以查询以下模式以检测 SSIS IR 节点的不可用性：

* SSIS IR 仍在运行时，在许多一分钟时间段内都会缺失 `Heartbeat` 记录。
* SSIS IR 仍在运行时，在许多一分钟时间段内会有 ResultType 属性设置为 `Unhealthy` 的 `Heartbeat` 记录。

你可以将以上查询转换成[警报](../azure-monitor/alerts/alerts-unified-log.md)，然后转到 [SSIS IR 监视页面](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)以确认收到那些警报。

在 Logs Analytics 上查询 SSIS 包执行日志时，可以使用 OperationId/ExecutionId/CorrelationId 属性联接它们  。 对于与未存储在 SSISDB 中的包/通过 T-SQL 调用的包相关的所有操作/执行，OperationId/ExecutionId 始终设置为 `1`  。

![在 Log Analytics 上查询 SSIS 包执行日志](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>后续步骤
[以编程方式监视和管理管道](monitor-programmatically.md)
