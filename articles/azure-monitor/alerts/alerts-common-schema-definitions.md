---
title: Azure Monitor 中的警报架构定义
description: 了解 Azure Monitor 的常见警报架构定义
author: ofirmanor
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 784106dab7b95f63957abe1de44dfbf5734dd5f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744485"
---
# <a name="common-alert-schema-definitions"></a>常见警报架构定义

本文介绍了 Azure Monitor 的[常见警报架构定义](./alerts-common-schema.md)，包括 Webhook、Azure 逻辑应用、Azure Functions 和 Azure 自动化 Runbook 的警报架构定义。 

任何警报实例都描述了受影响的资源和警报原因。 在下面各部分介绍的常见架构中对这些实例进行了描述：
* 基本要素：所有警报类型中都有的一组标准化字段，描述了警报所针对的资源，以及其他常见警报元数据（例如，严重性或说明）。 在[警报概述](alerts-overview.md#overview)中可以找到严重级别的定义。 
* 警报上下文：一组描述了警报原因的字段，以及因警报类型而异的字段。 例如，指标警报包含警报上下文中的指标名称和指标值等字段，而活动日志警报则包含关于生成了警报的事件的信息。 

**示例警报有效负载**
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "configurationItems": [
        "wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials"></a>Essentials

| 字段 | 说明|
|:---|:---|
| alertId | 标识警报实例的唯一资源 ID。 |
| alertRule | 生成了警报实例的警报规则的名称。 |
| 严重性 | 警报严重性。 可能的值：Sev0、Sev1、Sev2、Sev3 或 Sev4。 |
| signalType | 标识其上定义了警报规则的信号。 可能的值：Metric、Log 或 Activity Log。 |
| monitorCondition | 当警报触发时，警报的监视条件设置为“已触发”。 当导致警报触发的基础条件清除时，监视条件设置为“已解决”。   |
| monitoringService | 生成了警报的监视服务或解决方案。 警报上下文的字段取决于监视服务。 |
| alertTargetIds | 作为警报的受影响目标的 Azure 资源管理器 ID 的列表。 对于在 Log Analytics 工作区或 Application Insights 实例上定义的日志警报，则为各自的工作区或应用程序。 |
| configurationItems | 警报的受影响资源的列表。 在某些情况下，配置项可能与警报目标不同，例如，在 Log Analytics 工作区上定义的日志指标或日志警报中，配置项是发送遥测的实际资源，而不是工作区。 ITSM 系统使用此字段将警报关联到 CMDB 中的资源。 |
| originAlertId | 警报实例的 ID，由生成警报实例的监视服务生成。 |
| firedDateTime | 警报实例的触发日期和时间，采用协调世界时 (UTC)。 |
| resolvedDateTime | 当警报实例的监视条件设置为“已解决”时的日期和时间 (UTC)。 目前只适用于指标警报。|
| description | 警报规则中定义的说明。 |
|essentialsVersion| “基本要素”部分的版本号。|
|alertContextVersion | “`alertContext`”部分的版本号。 |

**示例值**
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context"></a>警报上下文

### <a name="metric-alerts---static-threshold"></a>指标警报 - 静态阈值

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**示例值**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="metric-alerts---dynamic-threshold"></a>指标警报 - 动态阈值

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**示例值**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "DynamicThresholdCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "alertSensitivity": "High",
            "failingPeriods": {
              "numberOfEvaluationPeriods": 1,
              "minFailingPeriodsToAlert": 1
            },
            "ignoreDataBefore": null,
            "metricName": "Egress",
            "metricNamespace": "microsoft.storage/storageaccounts",
            "operator": "GreaterThan",
            "threshold": "47658",
            "timeAggregation": "Total",
            "dimensions": [],
            "metricValue": 50101
          }
        ],
        "windowStartTime": "2021-07-20T05:07:26.363Z",
        "windowEndTime": "2021-07-20T05:12:26.363Z"
      }
    }
}
```

### <a name="metric-alerts---availability-tests"></a>指标警报 - 可用性测试

#### <a name="monitoringservice--platform"></a>`monitoringService` = `Platform`

**示例值**
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "WebtestLocationAvailabilityCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Failed Location",
            "metricNamespace": null,
            "operator": "GreaterThan",
            "threshold": "2",
            "timeAggregation": "Sum",
            "dimensions": [],
            "metricValue": 5,
            "webTestName": "myAvailabilityTest-myApplication"
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>日志警报

> [!NOTE]
> 对于定义了自定义电子邮件主题和/或 JSON 有效负载的日志警报，启用常见架构会将电子邮件主题和/或有效负载架构恢复为如下所述的架构。 这意味着，如果要定义自定义 JSON 有效负载，则 Webhook 不能使用常见的警报架构。 对于启用了常见架构的警报，大小上限为 256KB/警报。 如果搜索结果导致警报大小超出此阈值，就不会被嵌入日志警报有效负载。 可以通过检查标志 `IncludedSearchResults` 来确定这一点。 在不包括搜索结果时，应使用 `LinkToFilteredSearchResultsAPI` 或 `LinkToSearchResultsAPI` 通过 [Log Analytics API](/rest/api/loganalytics/dataaccess/query/get) 访问查询结果。

#### <a name="monitoringservice--log-analytics"></a>`monitoringService` = `Log Analytics`

**示例值**
```json
{
  "alertContext": {
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
    "ResultCount": 2,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "SeverityDescription": "Warning",
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "SearchIntervalDurationMin": "15",
    "AffectedConfigurationItems": [
      "INC-Gen2Alert"
    ],
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000,
    "Operator": "Less Than",
    "Dimensions": [
      {
        "name": "Computer",
        "value": "INC-Gen2Alert"
      }
    ],
    "SearchResults": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Computer",
              "type": "string"
            },
            {
              "name": "TimeGenerated",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ]
    },
    "dataSources": [
      {
        "resourceId": "/subscriptions/a5ea55e2-7482-49ba-90b3-60e7496dd873/resourcegroups/test/providers/microsoft.operationalinsights/workspaces/test",
        "tables": [
          "Heartbeat"
        ]
      }
    ],
  "IncludedSearchResults": "True",
  "AlertType": "Metric measurement"
  }
}
```

#### <a name="monitoringservice--application-insights"></a>`monitoringService` = `Application Insights`

**示例值**
```json
{
  "alertContext": {
    "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
    "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
    "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
    "ResultCount": 2,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
    "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
    "SearchIntervalDurationMin": "15",
    "SearchIntervalInMinutes": "15",
    "Threshold": 10000.0,
    "Operator": "Less Than",
    "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6",
    "Dimensions": [
      {
        "name": "IP",
        "value": "1.1.1.1"
      }
    ],
    "SearchResults": {
      "tables": [
        {
          "name": "PrimaryResult",
          "columns": [
            {
              "name": "$table",
              "type": "string"
            },
            {
              "name": "Id",
              "type": "string"
            },
            {
              "name": "Timestamp",
              "type": "datetime"
            }
          ],
          "rows": [
            [
              "Fabrikam",
              "33446677a",
              "2018-02-02T15:03:12.18Z"
            ],
            [
              "Contoso",
              "33445566b",
              "2018-02-02T15:16:53.932Z"
            ]
          ]
        }
      ],
      "dataSources": [
        {
          "resourceId": "/subscriptions/a5ea27e2-7482-49ba-90b3-52e7496dd873/resourcegroups/test/providers/microsoft.operationalinsights/workspaces/test",
          "tables": [
            "Heartbeat"
          ]
        }
      ]
    },
    "IncludedSearchResults": "True",
    "AlertType": "Metric measurement"
  }
}
```

#### <a name="monitoringservice--log-alerts-v2"></a>`monitoringService` = `Log Alerts V2`

> [!NOTE]
> API 版本 2020-05-01 中的日志警报规则使用此有效负载类型，该类型仅支持通用架构。 使用此版本时，搜索结果不会嵌入到日志警报有效负载中。 应使用[维度](./alerts-unified-log.md#split-by-alert-dimensions)来提供已触发警报的上下文。 还可以使用 `LinkToFilteredSearchResultsAPI` 或 `LinkToSearchResultsAPI` 通过 [Log Analytics API](/rest/api/loganalytics/dataaccess/query/get) 访问查询结果。 如果必须嵌入结果，请使用包含所提供链接的逻辑应用，以生成自定义有效负载。

**示例值**
```json
{
  "alertContext": {
    "properties": null,
    "conditionType": "LogQueryCriteria",
    "condition": {
      "windowSize": "PT10M",
      "allOf": [
        {
          "searchQuery": "Heartbeat",
          "metricMeasure": null,
          "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
          "operator": "LowerThan",
          "threshold": "1",
          "timeAggregation": "Count",
          "dimensions": [
            {
              "name": "Computer",
              "value": "TestComputer"
            }
          ],
          "metricValue": 0.0,
          "failingPeriods": {
            "numberOfEvaluationPeriods": 1,
            "minFailingPeriodsToAlert": 1
          },
          "linkToSearchResultsUI": "https://portal.azure.com#@12345a-1234b-123c-123d-12345678e/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%212345a-1234b-123c-123d-12345678e%2FresourceGroups%2FContoso%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2FContoso%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12345a-1234b-123c-123d-12345678e/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%212345a-1234b-123c-123d-12345678e%2FresourceGroups%2FContoso%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2FContoso%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/Contoso/providers/Microsoft.Compute/virtualMachines/Contoso/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
          "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/Contoso/providers/Microsoft.Compute/virtualMachines/Contoso/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
        }
      ],
      "windowStartTime": "2020-07-07T13:54:34Z",
      "windowEndTime": "2020-07-09T13:54:34Z"
    }
  }
}
```

### <a name="activity-log-alerts"></a>活动日志警报

#### <a name="monitoringservice--activity-log---administrative"></a>`monitoringService` = `Activity Log - Administrative`

**示例值**
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/12345a-1234b-123c-123d-12345678e/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/12345a-1234b-123c-123d-12345678e/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"12345a-1234b-123c-123d-12345678e\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

#### <a name="monitoringservice--activity-log---policy"></a>`monitoringService` = `Activity Log - Policy`

**示例值**
```json
{
  "alertContext": {
    "authorization": {
      "action": "Microsoft.Resources/checkPolicyCompliance/read",
      "scope": "/subscriptions/<GUID>"
    },
    "channels": "Operation",
    "claims": "{\"aud\":\"https://management.azure.com/\",\"iss\":\"https://sts.windows.net/<GUID>/\",\"iat\":\"1566711059\",\"nbf\":\"1566711059\",\"exp\":\"1566740159\",\"aio\":\"42FgYOhynHNw0scy3T/bL71+xLyqEwA=\",\"appid\":\"<GUID>\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/<GUID>/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"<GUID>\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"<GUID>\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"<GUID>\",\"uti\":\"Miy1GzoAG0Scu_l3m1aIAA\",\"ver\":\"1.0\"}",
    "caller": "<GUID>",
    "correlationId": "<GUID>",
    "eventSource": "Policy",
    "eventTimestamp": "2019-08-25T11:11:34.2269098+00:00",
    "eventDataId": "<GUID>",
    "level": "Warning",
    "operationName": "Microsoft.Authorization/policies/audit/action",
    "operationId": "<GUID>",
    "properties": {
      "isComplianceCheck": "True",
      "resourceLocation": "eastus2",
      "ancestors": "<GUID>",
      "policies": "[{\"policyDefinitionId\":\"/providers/Microsoft.Authorization/policyDefinitions/<GUID>/\",\"policySetDefinitionId\":\"/providers/Microsoft.Authorization/policySetDefinitions/<GUID>/\",\"policyDefinitionReferenceId\":\"vulnerabilityAssessmentMonitoring\",\"policySetDefinitionName\":\"<GUID>\",\"policyDefinitionName\":\"<GUID>\",\"policyDefinitionEffect\":\"AuditIfNotExists\",\"policyAssignmentId\":\"/subscriptions/<GUID>/providers/Microsoft.Authorization/policyAssignments/SecurityCenterBuiltIn/\",\"policyAssignmentName\":\"SecurityCenterBuiltIn\",\"policyAssignmentScope\":\"/subscriptions/<GUID>\",\"policyAssignmentSku\":{\"name\":\"A1\",\"tier\":\"Standard\"},\"policyAssignmentParameters\":{}}]"
    },
    "status": "Succeeded",
    "subStatus": "",
    "submissionTimestamp": "2019-08-25T11:12:46.1557298+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---autoscale"></a>`monitoringService` = `Activity Log - Autoscale`

**示例值**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "claims": "{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn\":\"Microsoft.Insights/autoscaleSettings\"}",
    "caller": "Microsoft.Insights/autoscaleSettings",
    "correlationId": "<GUID>",
    "eventSource": "Autoscale",
    "eventTimestamp": "2019-08-21T16:17:47.1551167+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Insights/AutoscaleSettings/Scaleup/Action",
    "operationId": "<GUID>",
    "properties": {
      "description": "The autoscale engine attempting to scale resource '/subscriptions/d<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS' from 9 instances count to 10 instances count.",
      "resourceName": "/subscriptions/<GUID>/resourceGroups/voiceassistancedemo/providers/Microsoft.Compute/virtualMachineScaleSets/alexademo",
      "oldInstancesCount": "9",
      "newInstancesCount": "10",
      "activeAutoscaleProfile": "{\r\n  \"Name\": \"Auto created scale condition\",\r\n  \"Capacity\": {\r\n    \"Minimum\": \"1\",\r\n    \"Maximum\": \"10\",\r\n    \"Default\": \"1\"\r\n  },\r\n  \"Rules\": [\r\n    {\r\n      \"MetricTrigger\": {\r\n        \"Name\": \"Percentage CPU\",\r\n        \"Namespace\": \"microsoft.compute/virtualmachinescalesets\",\r\n        \"Resource\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"ResourceLocation\": \"eastus\",\r\n        \"TimeGrain\": \"PT1M\",\r\n        \"Statistic\": \"Average\",\r\n        \"TimeWindow\": \"PT5M\",\r\n        \"TimeAggregation\": \"Average\",\r\n        \"Operator\": \"GreaterThan\",\r\n        \"Threshold\": 0.0,\r\n        \"Source\": \"/subscriptions/<GUID>/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachineScaleSets/testVMSS\",\r\n        \"MetricType\": \"MDM\",\r\n        \"Dimensions\": [],\r\n        \"DividePerInstance\": false\r\n      },\r\n      \"ScaleAction\": {\r\n        \"Direction\": \"Increase\",\r\n        \"Type\": \"ChangeCount\",\r\n        \"Value\": \"1\",\r\n        \"Cooldown\": \"PT1M\"\r\n      }\r\n    }\r\n  ]\r\n}",
      "lastScaleActionTime": "Wed, 21 Aug 2019 16:17:47 GMT"
    },
    "status": "Succeeded",
    "submissionTimestamp": "2019-08-21T16:17:47.2410185+00:00"
  }
}
```

#### <a name="monitoringservice--activity-log---security"></a>`monitoringService` = `Activity Log - Security`

**示例值**
```json
{
  "alertContext": {
    "channels": "Operation",
    "correlationId": "<GUID>",
    "eventSource": "Security",
    "eventTimestamp": "2019-08-26T08:34:14+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Security/locations/alerts/activate/action",
    "operationId": "<GUID>",
    "properties": {
      "threatStatus": "Quarantined",
      "category": "Virus",
      "threatID": "2147519003",
      "filePath": "C:\\AlertGeneration\\test.eicar",
      "protectionType": "Windows Defender",
      "actionTaken": "Blocked",
      "resourceType": "Virtual Machine",
      "severity": "Low",
      "compromisedEntity": "testVM",
      "remediationSteps": "[\"No user action is necessary\"]",
      "attackedResourceType": "Virtual Machine"
    },
    "status": "Active",
    "submissionTimestamp": "2019-08-26T09:28:58.3019107+00:00"
  }
}
```

#### <a name="monitoringservice--servicehealth"></a>`monitoringService` = `ServiceHealth`

**示例值**
```json
{
  "alertContext": {
    "authorization": null,
    "channels": 1,
    "claims": null,
    "caller": null,
    "correlationId": "f3cf2430-1ee3-4158-8e35-7a1d615acfc7",
    "eventSource": 2,
    "eventTimestamp": "2019-06-24T11:31:19.0312699+00:00",
    "httpRequest": null,
    "eventDataId": "<GUID>",
    "level": 3,
    "operationName": "Microsoft.ServiceHealth/maintenance/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "Azure Synapse Analytics Scheduled Maintenance Pending",
      "service": "Azure Synapse Analytics",
      "region": "East US",
      "communication": "<MESSAGE>",
      "incidentType": "Maintenance",
      "trackingId": "<GUID>",
      "impactStartTime": "2019-06-26T04:00:00Z",
      "impactMitigationTime": "2019-06-26T12:00:00Z",
      "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"East US\"}],\"ServiceName\":\"Azure Synapse Analytics\"}]",
      "impactedServicesTableRows": "<tr>\r\n<td align='center' style='padding: 5px 10px; border-right:1px solid black; border-bottom:1px solid black'>Azure Synapse Analytics</td>\r\n<td align='center' style='padding: 5px 10px; border-bottom:1px solid black'>East US<br></td>\r\n</tr>\r\n",
      "defaultLanguageTitle": "Azure Synapse Analytics Scheduled Maintenance Pending",
      "defaultLanguageContent": "<MESSAGE>",
      "stage": "Planned",
      "communicationId": "<GUID>",
      "maintenanceId": "<GUID>",
      "isHIR": "false",
      "version": "0.1.1"
    },
    "status": "Active",
    "subStatus": null,
    "submissionTimestamp": "2019-06-24T11:31:31.7147357+00:00",
    "ResourceType": null
  }
}
```
#### <a name="monitoringservice--resource-health"></a>`monitoringService` = `Resource Health`

**示例值**
```json
{
  "alertContext": {
    "channels": "Admin, Operation",
    "correlationId": "<GUID>",
    "eventSource": "ResourceHealth",
    "eventTimestamp": "2019-06-24T15:42:54.074+00:00",
    "eventDataId": "<GUID>",
    "level": "Informational",
    "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
    "operationId": "<GUID>",
    "properties": {
      "title": "This virtual machine is stopping and deallocating as requested by an authorized user or process",
      "details": null,
      "currentHealthStatus": "Unavailable",
      "previousHealthStatus": "Available",
      "type": "Downtime",
      "cause": "UserInitiated"
    },
    "status": "Active",
    "submissionTimestamp": "2019-06-24T15:45:20.4488186+00:00"
  }
}
```


## <a name="next-steps"></a>后续步骤

- 详细了解[常见警报架构](./alerts-common-schema.md)。
- 了解[如何创建使用常见警报架构处理所有警报的逻辑应用](./alerts-common-schema-integrations.md)。
