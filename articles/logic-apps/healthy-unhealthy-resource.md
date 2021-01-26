---
title: 设置日志记录，以便在 Azure 安全中心监视逻辑应用
description: 通过设置诊断日志记录，监视 Azure 安全中心中逻辑应用资源的运行状况。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: ed1fe2885b1be28a03251bcfcecd08bdbd35adcf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790066"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>设置日志记录，以便在 Azure 安全中心监视逻辑应用

监视逻辑应用资源 [Microsoft Azure 安全中心](../security-center/security-center-introduction.md)时，可以 [查看逻辑应用是否遵循默认策略](#view-logic-apps-health-status)。 Azure 会在启用日志记录并正确设置日志目标后显示逻辑应用资源的运行状况状态。 本文介绍如何配置诊断日志记录，并确保所有逻辑应用都是正常运行的资源。

> [!TIP]
> 若要查找逻辑应用服务的当前状态，请查看 " [Azure 状态" 页](https://status.azure.com/)，其中列出了每个可用区域中不同产品和服务的状态。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/) 。
* [已启用诊断日志记录](#enable-diagnostic-logging)的现有逻辑应用。
* 为逻辑应用启用日志记录所需的 Log Analytics 工作区。 如果没有工作区，请首先 [创建工作区](../azure-monitor/learn/quick-create-workspace.md)。

## <a name="enable-diagnostic-logging"></a>启用诊断记录

必须先 [设置诊断日志记录](monitor-logic-apps-log-analytics.md)，然后才能查看逻辑应用的资源运行状况状态。 如果已有 Log Analytics 工作区，则可以在创建逻辑应用或现有逻辑应用时启用日志记录。

> [!TIP]
> 默认建议为逻辑应用启用诊断日志。 但是，你可以控制逻辑应用的此设置。 为逻辑应用启用诊断日志时，可以使用这些信息来帮助分析安全事件。

### <a name="check-diagnostic-logging-setting"></a>检查诊断日志记录设置

如果不确定逻辑应用是否启用了诊断日志记录，可以在安全中心内查看：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，输入并选择 " **安全中心**"。
1. 在 "安全中心" 仪表板菜单的 " **常规**" 下，选择 " **建议**"。
1. 在安全建议表中，查找并选择 "在逻辑应用中 **启用审核和日志记录** &gt; **诊断日志" 应** 在安全控件表中启用。
1. 在 "建议" 页上，展开 " **修正步骤** " 部分并查看选项。 可以通过选择 "**快速修复**" 来启用逻辑应用诊断！ 按钮，或按照手动修正说明进行操作。

## <a name="view-logic-apps-health-status"></a>查看逻辑应用的运行状况状态

[启用诊断日志记录](#enable-diagnostic-logging)后，可以在安全中心看到逻辑应用的运行状况状态。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，输入并选择 " **安全中心**"。
1. 在安全中心仪表板菜单的 " **常规**" 下，选择 " **清点**"。
1. 在 "清单" 页上，筛选资产列表以仅显示逻辑应用资源。 在 "页面" 菜单中，选择 " **资源类型**" " &gt; **逻辑应用**"。

   不 **正常的资源** 计数器显示安全中心视为不正常的逻辑应用数。
1.  在逻辑应用资源列表中，查看 " **建议** " 列。 若要查看特定逻辑应用的运行状况详细信息，请选择资源名称，或选择省略号 **按钮 ("**) " &gt; **视图资源**"。
1.  若要修正任何潜在的资源运行状况问题，请按照逻辑应用中列出的步骤进行操作。

如果已启用诊断日志记录，则可能是日志的目标存在问题。 查看 [如何解决不同诊断日志记录目标的问题](#fix-diagnostic-logging-for-logic-apps)。

## <a name="fix-diagnostic-logging-for-logic-apps"></a>为逻辑应用修复诊断日志记录

如果 [逻辑应用在安全中心中被列为 "不正常](#view-logic-apps-health-status)"，请在 "代码" 视图中的 "Azure 门户" 或通过 "Azure CLI 打开逻辑应用。 然后，在 "目标配置" 中检查诊断日志： [azure Log Analytics](#log-analytics-and-event-hubs-destinations)、 [azure 事件中心](#log-analytics-and-event-hubs-destinations)或 [azure 存储帐户](#storage-account-destination)。

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics 和事件中心目标

如果使用 Log Analytics 或事件中心作为逻辑应用诊断日志的目标，请检查以下设置。 

1. 若要确认是否已启用诊断日志，请检查 "诊断设置" `logs.enabled` 字段是否设置为 `true` 。 
1. 若要确认未将存储帐户设置为目标，请检查 `storageAccountId` 字段是否设置为 `false` 。

例如：

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>存储帐户目标

如果使用存储帐户作为逻辑应用诊断日志的目标，请检查以下设置。

1. 若要确认是否已启用诊断日志，请检查 "诊断设置" `logs.enabled` 字段是否设置为 `true` 。
1. 若要确认已为诊断日志启用保留策略，请检查 `retentionPolicy.enabled` 字段是否设置为 `true` 。
1. 若要确认设置的保留时间为0-365 天，请检查 `retentionPolicy.days` 字段是否设置为0到365之间的数字。

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```