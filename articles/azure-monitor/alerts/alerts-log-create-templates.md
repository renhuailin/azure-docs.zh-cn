---
title: 使用 Azure 资源管理器模板创建日志警报 | Microsoft Docs
description: 了解如何使用资源管理器模板创建日志警报
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: d3414b0de4a173b08815fc274e06e67814f6c887
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687508"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>使用资源管理器模板创建日志警报

通过日志警报，用户可以使用 [Log Analytics](../logs/log-analytics-tutorial.md) 查询按每个设置的频率评估资源日志，并根据结果触发警报。 规则可以使用[操作组](./action-groups.md)触发运行一个或多个操作。 [详细了解日志警报的功能和术语](./alerts-unified-log.md)。

本文介绍如何在 Azure Monitor 中使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)配置[日志警报](./alerts-unified-log.md)。 使用资源管理器模板可以通过编程方式在多个环境中设置一致且可重现的警报。 日志警报是在 `Microsoft.Insights/scheduledQueryRules` 资源提供程序中创建的。 请参阅[计划查询规则 API](/rest/api/monitor/scheduledqueryrules/) 的 API 参考。

基本步骤如下所示：

1. 将以下某个模板用作描述如何创建警报的 JSON 文件。
2. 编辑相应的参数文件并将其用作 JSON 来自定义警报。
4. 使用任意部署方法部署模板。

> [!NOTE]
> 可以将 [Log Analytics 工作区](../logs/log-analytics-tutorial.md)中的日志数据发送到 Azure Monitor 指标存储。 指标警报具有[不同的行为](./alerts-metric-overview.md)，该行为可能更可取，具体取决于你要使用的数据。 要了解如何将日志路由到指标，请参阅[日志的指标警报](./alerts-metric-logs.md)。

> [!NOTE]
> 过去使用旧式 [Log Analytics 警报 API](./api-alerts.md) 以及 [Log Analytics 保存的搜索和警报](../insights/solutions.md)的旧式模板管理 Log Analytics 的日志警报。 [了解有关切换到当前 ScheduledQueryRules API 的详细信息](alerts-log-api-switch.md)。


## <a name="simple-template-up-to-api-version-2018-04-16"></a>简单模板（最高 API 版本为 2018-04-16）

[计划查询规则（创建）](/rest/api/monitor/scheduledqueryrules/createorupdate)模板，基于[结果日志警报数量](./alerts-unified-log.md#count-of-the-results-table-rows)（示例数据设置为变量）：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)保存和部署该 JSON。

## <a name="template-with-cross-resource-query-up-to-api-version-2018-04-16"></a>带有跨资源查询的模板（最高 API 版本为 2018-04-16）

[计划查询规则（创建）](/rest/api/monitor/scheduledqueryrules/createorupdate)模板，基于可[跨资源](../logs/cross-workspace-query.md)查询的[指标度量](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)（示例数据设置为变量）：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}
```

> [!IMPORTANT]
> 使用日志警报中的跨资源查询时，必须使用 [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) 且用户必须有权访问所述的资源列表

可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)保存和部署该 JSON。

## <a name="template-for-all-resource-types-from-api-version-2021-02-01-preview"></a>所有资源类型的模板（API 版本为 2021-02-01-preview）

适用于所有资源类型的[计划查询规则（创建）](/rest/api/monitor/scheduledqueryrules/createorupdate)模板（示例数据设置为变量）：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "location": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Location of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "autoMitigate": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert will automatically resolve"
            }
        },
        "checkWorkspaceAlertsStorageConfigured": {
            "type": "bool",
            "defaultValue": false,
            "metadata": {
                "description": "Specifies whether to check linked storage and fail creation if the storage was not found"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "query": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricMeasureColumn": {
            "type": "string",
            "metadata": {
                "description": "Name of the measure column used in the alert evaluation."
            }
        },
        "resourceIdColumn": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource ID column used in the alert targeting the alerts."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "muteActionsDuration": {
            "type": "string",
            "defaultValue": null,
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Mute actions for the chosen period of time (in ISO 8601 duration format) after the alert is fired."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "location": "[parameters('location')]",
            "apiVersion": "2021-02-01-preview",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "allOf": [
                        {
                            "query": "[parameters('query')]",
                            "metricMeasureColumn": "[parameters('metricMeasureColumn')]",
                            "resourceIdColumn": "[parameters('resourceIdColumn')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            }
                        }
                    ]
                },
                "muteActionsDuration": "[parameters('muteActionsDuration')]",
                "autoMitigate": "[parameters('autoMitigate')]",
                "checkWorkspaceAlertsStorageConfigured": "[parameters('checkWorkspaceAlertsStorageConfigured')]",
                "actions": {
                    "actionGroups": "[parameters('actionGroupId')]",
                    "customProperties": {
                        "key1": "value1",
                        "key2": "value2"
                    }
                }
            }
        }
    ]
}
```

可以使用 [Azure 门户中的 Azure 资源管理器](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)保存和部署该 JSON。

## <a name="next-steps"></a>后续步骤

* 了解[日志警报](./alerts-unified-log.md)
* 了解如何[管理日志警报](./alerts-log.md)
* 了解[用于日志警报的 Webhook 操作](./alerts-log-webhook.md)
* 了解有关[日志查询](../logs/log-query-overview.md)的详细信息。
