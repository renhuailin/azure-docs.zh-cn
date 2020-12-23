---
title: 'Azure Monitor (预览版中的数据收集规则) '
description: 概述数据收集规则 (Dcr) 在 Azure Monitor 包括其内容和结构，以及如何创建和使用它们。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 048068a74151bb986392b5cb27787385fc0f5363
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95315526"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Azure Monitor (预览版中的数据收集规则) 
数据收集规则 (DCR) 定义传入 Azure Monitor 的数据，并指定应发送或存储数据的位置。 本文概述了数据收集规则，其中包括内容和结构，以及如何创建和使用它们。

## <a name="input-sources"></a>输入源
数据收集规则当前支持以下输入源：

- 具有 Azure Monitor 代理的 Azure 虚拟机。 请参阅 [配置 Azure Monitor 代理的数据收集 (预览版) ](data-collection-rule-azure-monitor-agent.md)。



## <a name="components-of-a-data-collection-rule"></a>数据收集规则的组件
数据收集规则包括以下组件。

| 组件 | 说明 |
|:---|:---|
| 数据源 | 监视数据的唯一源，其自身的格式和公开其数据的方法。 数据源的示例包括 Windows 事件日志、性能计数器和 syslog。 每个数据源都与特定数据源类型匹配，如下所述。 |
| 流 | 唯一的句柄，描述将被转换并架构化为一种类型的一组数据源。 每个数据源都需要一个或多个流，多个数据源可以使用一个流。 流中的所有数据源共享公用架构。 例如，当您想要将特定数据源发送到同一 Log Analytics 工作区中的多个表时，请使用多个流。 |
| 目标 | 应将数据发送到的目标集。 示例包括 Log Analytics 工作区、Azure Monitor 指标和 Azure 事件中心。 | 
| 数据流 | 应将哪些流发送到哪些目标的定义。 | 

下图显示了数据收集规则的组件及其关系

[![DCR 图](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>数据源类型
每个数据源都有一个数据源类型。 每种类型都定义一组唯一的属性，这些属性必须为每个数据源指定。 下表显示了当前可用的数据源类型。

| 数据源类型 | 说明 | 
|:---|:---|
| 扩展 | 基于 VM 扩展的数据源 |
| performanceCounters | 适用于 Windows 和 Linux 的性能计数器 |
| syslog | Linux 上的 Syslog 事件 |
| windowsEventLogs | Windows 事件日志 |


## <a name="limits"></a>限制
有关适用于每个数据收集规则的限制，请参阅 [Azure Monitor 服务限制](../service-limits.md#data-collection-rules)。


## <a name="create-a-dcr"></a>创建 DCR
目前有两种方法可用于创建 DCR：

- [使用 Azure 门户](data-collection-rule-azure-monitor-agent.md) 创建数据收集规则并将其与一个或多个虚拟机相关联。
- 直接在 JSON 中编辑数据收集规则并 [使用 REST API 进行提交](/rest/api/monitor/datacollectionrules)。

## <a name="sample-data-collection-rule"></a>示例数据收集规则
下面的示例数据收集规则适用于具有 Azure 管理代理的虚拟机，并提供以下详细信息：

- 性能数据
  - 每15秒收集一次特定的处理器、内存、逻辑磁盘和物理磁盘计数器，每分钟上载一次。
  - 每隔30秒收集一次特定的进程计数器，每5分钟上载一次。
- Windows 事件
  - 收集每分钟的 Windows 安全事件和上载。
  - 收集 Windows 应用程序和系统事件，并每5分钟上载一次。
- Syslog
  - 从 cron 设施收集调试、关键和紧急事件。
  - 收集 syslog 设备发出的警报、严重事件和紧急事件。
- 目标
  - 将所有数据发送到名为 centralWorkspace 的 Log Analytics 工作区。

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
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
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
            "Microsoft-WindowsEvent"
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

- 使用 Azure Monitor 代理从虚拟机[创建数据收集规则](data-collection-rule-azure-monitor-agent.md)及其关联。