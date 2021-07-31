---
title: 使用 Azure Monitor 警报触发复杂操作
description: 了解如何创建逻辑应用操作处理 Azure Monitor 警报。
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029839"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>如何使用 Azure Monitor 警报触发复杂操作

本文介绍如何设置和触发逻辑应用，在警报触发时在 Microsoft Teams 中创建对话。

## <a name="overview"></a>概述

触发 Azure Monitor 警报时，它将调用[操作组](./action-groups.md)。 通过操作组可触发一个或多个操作，以告知其他人发生了警报，以及予以补救。

常规过程：

-   为相应的警报类型创建逻辑应用。

-   将相应警报类型的示例有效负载导入到逻辑应用中。

-   定义逻辑应用行为。

-   将逻辑应用的 HTTP 终结点复制到 Azure 操作组。

如果希望逻辑应用执行其他操作，过程与此类似。

## <a name="create-an-activity-log-alert-administrative"></a>创建活动日志警报：管理

1. [创建逻辑应用](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)。

1.  选择触发器：“当收到 HTTP 请求时”。

1. 在“收到 HTTP 请求时”对话框中，选择“使用示例有效负载生成架构”。 

    ![显示已选中“收到 HTTP 请求时”对话框和“使用示例有效负载生成架构”选项的屏幕截图。 ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  将以下示例有效负载复制并粘贴到对话框中：

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. “逻辑应用设计器”将显示一个弹出窗口，提醒发送到逻辑应用的请求必须将“Content-Type”标头设置为“application/json”。 关闭该弹出窗口。 Azure Monitor 警报将设置该标头。

    ![设置 Content-Type 标头](media/action-groups-logic-app/content-type-header.png &quot;设置 Content-Type 标头")

1. 依次选择“+ 新建步骤”、“添加操作”  。

    ![添加操作](media/action-groups-logic-app/add-action.png "添加操作")

1. 搜索并选择 Microsoft Teams 连接器。 选择“Microsoft Teams - 发布消息”操作。

    ![Microsoft Teams 操作](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams 操作")

1. 配置 Microsoft Teams 操作。 “逻辑应用设计器”将要求对你的工作或学校帐户进行身份验证。 选择要向其发送消息的“团队 ID”和“通道 ID”。

13. 通过结合使用静态文本和动态内容中对 \<fields\> 的引用来配置消息。 将以下文本复制并粘贴到“消息”字段：

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    然后，搜索 \<fields\> 并将其替换为同名的动态内容标记。

    > [!NOTE]
    > 有两个名为 **status** 的动态字段。 将这两个字段添加到消息。 使用 **activityLog** 属性包中的字段，并删除另一个字段。 将鼠标悬停在 **status** 字段上以查看完全限定的字段引用，如以下屏幕截图所示：

    ![Microsoft Teams 操作：发布消息](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams 操作：发布消息")

1. 在“逻辑应用设计器”的顶部，选择“保存”以保存逻辑应用。

1. 打开现有的操作组并添加一个操作，以引用逻辑应用。 如果还没有操作组，请参阅[在 Azure 门户中创建和管理操作组](./action-groups.md)来创建一个。 切勿忘记保存更改。

    ![更新操作组](media/action-groups-logic-app/update-action-group.png "更新操作组")

下次警报调用操作组时，将调用该逻辑应用。

## <a name="create-a-service-health-alert"></a>创建服务运行状况警报

Azure Service Health 条目包含在活动日志中。 创建警报的过程类似于[创建活动日志警报](#create-an-activity-log-alert-administrative)，但存在几处差别：

- 第 1 步到第 3 步相同。
- 在第 4 步中，对 HTTP 请求触发器使用以下示例有效负载：

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  第 5 步和第 6 步相同。
-  对于第 7 步到第 11 步，请使用以下过程：

   1. 依次选择“+ 新建步骤”和“添加条件”。 设置以下条件，以便逻辑应用仅在输入数据与下面的值匹配时执行。  在文本框中输入版本值时，请在其周围加上引号 ("0.1.1") 以确保它被评估为字符串而不是数字类型。  如果返回到该页，系统不会显示引号，但底层代码仍然保持字符串类型。   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      ![“服务运行状况有效负载条件”](media/action-groups-logic-app/service-health-payload-condition.png "服务运行状况有效负载条件")

   1. 在“if true”条件中，遵照[创建活动日志警报](#create-an-activity-log-alert-administrative)步骤 11 到 13 中的说明添加 Microsoft Teams 操作。

   1. 使用 HTML 和动态内容的组合定义消息。 将以下内容复制并粘贴到“消息”字段。 将 `[incidentType]`、`[trackingID]`、`[title]` 和 `[communication]` 字段替换为使用相同名称的动态内容标记：

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       ![“服务运行状况 true 条件下的发布操作”](media/action-groups-logic-app/service-health-true-condition-post-action.png "服务运行状况 true 条件下的发布操作")

   1. 对于 **If false** 条件，请提供有用的消息：

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![“服务运行状况 false 条件下的发布操作”](media/action-groups-logic-app/service-health-false-condition-post-action.png "服务运行状况 false 条件下的发布操作")

- 步骤 15 相同。 遵照说明保存逻辑应用并更新操作组。

## <a name="create-a-metric-alert"></a>创建指标警报

创建指标警报的过程类似于[创建活动日志警报](#create-an-activity-log-alert-administrative)，但存在几处差别：

- 第 1 步到第 3 步相同。
- 在第 4 步中，对 HTTP 请求触发器使用以下示例有效负载：

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- 第 5 步和第 6 步相同。
- 对于第 7 步到第 11 步，请使用以下过程：

  1. 依次选择“+ 新建步骤”和“添加条件”。 设置以下条件，以便逻辑应用仅在输入数据与下面的值匹配时执行。 在文本框中输入版本值时，请在其周围加上引号 ("2.0") 以确保它被评估为字符串而不是数字类型。  如果返回到该页，系统不会显示引号，但底层代码仍然保持字符串类型。 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       ![“指标警报有效负载条件”](media/action-groups-logic-app/metric-alert-payload-condition.png "指标警报有效负载条件")

  1. 在“if true”条件中，添加“For each”循环和 Microsoft Teams 操作。 使用 HTML 和动态内容的组合定义消息。

      ![“指标警报 true 条件下的发布操作”](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "指标警报 true 条件下的发布操作")

  1. 在“If false”条件中，定义 Microsoft Teams 操作，以指出指标警报不符合逻辑应用的预期。 包含 JSON 有效负载。 注意如何在 `json()` 表达式中引用 `triggerBody` 动态内容。

      ![“指标警报 false 条件下的发布操作”](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "指标警报 false 条件下的发布操作")

- 步骤 15 相同。 遵照说明保存逻辑应用并更新操作组。

## <a name="calling-other-applications-besides-microsoft-teams"></a>调用除 Microsoft Teams 外的其他应用程序
逻辑应用具有大量不同的连接器，可在广泛的应用程序和数据库中触发操作。 Slack、SQL Server、Oracle、Salesforce 只是一些示例。 有关连接器的详细信息，请参阅[逻辑应用连接器](../../connectors/apis-list.md)。  

## <a name="next-steps"></a>后续步骤
* 参阅 [Azure 活动日志警报概述](./alerts-overview.md)，了解如何接收警报。  
* 了解如何[配置发布 Azure 服务运行状况通知时的警报](../../service-health/alerts-activity-log-service-notifications-portal.md)。
* 详细了解[操作组](./action-groups.md)。
