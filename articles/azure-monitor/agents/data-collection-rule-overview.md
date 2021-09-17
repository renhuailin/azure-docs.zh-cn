---
title: Azure Monitor 中的数据收集规则（预览版）
description: 概述 Azure Monitor 中的数据收集规则 (DCR)，包括内容和结构，以及如何创建和使用它们。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.custom: references_region
ms.openlocfilehash: 7dca96e05860bb399435ac95ed81107c268ebc5c
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662225"
---
# <a name="data-collection-rules-in-azure-monitor"></a>Azure Monitor 中的数据收集规则
数据收集规则 (DCR) 定义传入 Azure Monitor 的数据，并指定应发送或存储数据的位置。 本文概述了数据收集规则，包括内容和结构，以及如何创建和使用它们。

## <a name="input-sources"></a>输入源
数据收集规则当前支持以下输入源：

- 在虚拟机、虚拟机规模集和 Azure Arc for servers 上运行的 Azure Monitor 代理。 请参阅[为 Azure Monitor 代理配置数据收集（预览版）](../agents/data-collection-rule-azure-monitor-agent.md)。



## <a name="components-of-a-data-collection-rule"></a>数据收集规则的组件
数据收集规则包括以下组件。

| 组件 | 说明 |
|:---|:---|
| 数据源 | 具有自己的格式和数据公开方法的监视数据的唯一源。 数据源的示例包括 Windows 事件日志、性能计数器和 syslog。 每个数据源都与特定数据源类型匹配，如下所述。 |
| 流 | 唯一句柄，描述将被转换并架构化为一种类型的一组数据源。 每个数据源都需要一个或多个流，多个数据源可以使用一个流。 流中的所有数据源共享公用架构。 例如，当你想要将特定数据源发送到同一 Log Analytics 工作区中的多个表时，请使用多个流。 |
| Destinations | 应将数据发送到的目标集。 示例包括 Log Analytics 工作区和 Azure Monitor 指标。 | 
| 数据流 | 应将哪些流发送到哪些目标的定义。 | 

数据收集规则按区域存储，并可在支持 Log Analytics 的所有公共区域中使用。 目前不支持政府区域和云。

下图显示了数据收集规则的组件及其关系

[![DCR 关系图](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>数据源类型
每个数据源都有一个数据源类型。 每种类型定义必须为每个数据源指定的唯一属性集。 下表显示了当前可用的数据源类型。

| 数据源类型 | 说明 | 
|:---|:---|
| 扩展 | 基于 VM 扩展的数据源 |
| performanceCounters | 适用于 Windows 和 Linux 的性能计数器 |
| syslog | Linux 上的 syslog 事件 |
| windowsEventLogs | Windows 事件日志 |


## <a name="limits"></a>限制
有关适用于每个数据收集规则的限制，请参阅 [Azure Monitor 服务限制](../service-limits.md#data-collection-rules)。

## <a name="data-resiliency-and-high-availability"></a>数据复原能力和高可用性
数据收集规则作为一项服务在区域内部署。 规则在你指定的区域中创建和存储，并备份到同一地区内的[配对区域](../../best-practices-availability-paired-regions.md#azure-regional-pairs)。  
此外，该服务部署到该区域内的所有 3 个[可用性区域](../../availability-zones/az-overview.md#availability-zones)，使其成为“区域冗余服务”，进一步增加了高可用性。


单一区域数据驻留：允许在单一区域存储客户数据的预览功能目前仅在亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部（圣保罗州）区域提供。 “单一区域驻留”在这些区域默认启用。

## <a name="create-a-dcr"></a>创建 DCR
当前可以使用以下任一方法创建 DCR：

- [使用 Azure 门户](../agents/data-collection-rule-azure-monitor-agent.md)创建数据收集规则，并将其与一个或多个虚拟机关联。
- 直接在 JSON 中编辑数据收集规则并[使用 REST API 进行提交](/rest/api/monitor/datacollectionrules)。
- 创建 DCR 以及与 [Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md) 的关联。
- 创建 DCR 以及与 Azure PowerShell 的关联。
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>示例数据收集规则
以下示例数据收集规则适用于具有 Azure Monitor 代理的虚拟机，并提供以下详细信息：

- 性能数据
  - 每 15 秒收集一次特定处理器、内存、逻辑磁盘和物理磁盘计数器，且每分钟上传一次。
  - 每 30 秒收集一次特定进程计数器，且每 5 分钟上传一次。
- Windows 事件
  - 收集 Windows 安全事件，且每分钟上传一次。
  - 收集 Windows 应用程序和系统事件，且每 5 分钟上传一次。
- Syslog
  - 从 cron 设备收集调试、严重和紧急事件。
  - 从 syslog 设备收集警报、严重和紧急事件。
- Destinations
  - 将所有数据发送到名为 centralWorkspace 的 Log Analytics 工作区。

> [!NOTE]
> 有关用于在数据收集规则中指定事件收集的 XPath 的说明，请参阅[使用自定义 XPath 查询限制数据收集](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>后续步骤

- 使用 Azure Monitor 代理[创建数据收集规则](data-collection-rule-azure-monitor-agent.md)以及与虚拟机的关联。
