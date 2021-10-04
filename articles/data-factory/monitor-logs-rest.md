---
title: 通过 Azure Monitor REST API 设置诊断日志
description: 了解如何使用 Azure Monitor REST API 为 Azure 数据工厂设置诊断日志。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 309b900f6c5f2ffe8cc0fd9101e7aa0408cb2dd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837725"
---
# <a name="setup-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 设置诊断日志

本文介绍如何使用 Azure Monitor REST API 为 Azure 数据工厂设置诊断日志。

## <a name="diagnostic-settings"></a>诊断设置

可以使用诊断设置来配置非计算资源的诊断日志。 用于资源控制的诊断设置具有以下功能：

* 指定要将诊断日志发送到何处。 例如，发送到 Azure 存储帐户、Azure 事件中心或 Monitor 日志。
* 指定要发送的日志类别。
* 指定要将每个日志类别保留在存储帐户中多长时间。
* 保留期为 0 天表示永久保留日志。 如果不需要永久保留，可将该值设置为 1 到 2,147,483,647 的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择了“事件中心”或“Monitor 日志”选项，此可能会发生这种情况。
* 保留策略按天应用。 一天的结束时间可能出现在协调世界时 (UTC) 的午夜。 在一天结束时，会删除当天已超过保留策略期限的日志。 例如，如果保留策略的期限为一天，则在今天开始时，会删除前天的日志。

## <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 启用诊断日志

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在 Monitor REST API 中创建或更新诊断设置

#### <a name="request"></a>请求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-vs-authorization.md)。

#### <a name="body"></a>正文

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

#### <a name="response"></a>响应

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

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>在 Monitor REST API 中获取有关诊断设置的信息

#### <a name="request"></a>请求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure AD 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-vs-authorization.md)。

#### <a name="response"></a>响应

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

## <a name="next-steps"></a>后续步骤

[使用 Azure Monitor 监视 SSIS 操作](monitor-ssis.md)
