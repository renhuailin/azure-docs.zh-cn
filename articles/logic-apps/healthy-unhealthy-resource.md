---
title: 设置日志记录以在 Azure 安全中心内监视逻辑应用
description: 通过设置诊断日志记录，在 Azure 安全中心内监视逻辑应用资源的运行状况。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712379"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>设置日志记录以在 Azure 安全中心内监视逻辑应用

在 [Microsoft Azure 安全中心](../security-center/security-center-introduction.md)内监视逻辑应用资源时，你可以[查看逻辑应用是否遵循默认策略](#view-logic-apps-health-status)。 在你启用日志记录并正确设置日志目标后，Azure 会显示逻辑应用资源的运行状况。 本文介绍了如何配置诊断日志记录，并确保你的所有逻辑应用都是正常运行的资源。

> [!TIP]
> 若要查找逻辑应用服务的当前状态，请查看 [Azure 状态页](https://status.azure.com/)，其中列出了每个可用区域中不同产品和服务的状态。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [已启用诊断日志记录](#enable-diagnostic-logging)的现有逻辑应用。
* 一个 Log Analytics 工作区，它是为逻辑应用启用日志记录所必需的。 如果你没有工作区，请首先[创建工作区](../azure-monitor/logs/quick-create-workspace.md)。

## <a name="enable-diagnostic-logging"></a>启用诊断记录

你必须先[设置诊断日志记录](monitor-logic-apps-log-analytics.md)，然后才能查看逻辑应用的资源运行状况。 如果已有 Log Analytics 工作区，则可以在创建逻辑应用时启用日志记录，或在现有逻辑应用上这样做。

> [!TIP]
> 默认建议是为逻辑应用启用诊断日志。 但是，你可以控制你的逻辑应用的此设置。 为你的逻辑应用启用诊断日志后，你可以使用这些信息来帮助分析安全事件。

### <a name="check-diagnostic-logging-setting"></a>检查诊断日志记录设置

如果你不确定逻辑应用是否启用了诊断日志记录，则可以在安全中心内进行检查：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，输入并选择“安全中心”。
1. 在安全中心仪表板菜单上的“常规”下选择“建议”。 
1. 在安全建议表的安全控制表中找到并选择“启用审核和日志记录”&gt;“应启用逻辑应用中的诊断日志”。
1. 在建议页上，展开“修正步骤”部分并查看选项。 可以通过选择“快速修复!” 按钮来启用逻辑应用诊断，也可以按照手动修正说明进行启用。

## <a name="view-logic-apps-health-status"></a>查看逻辑应用的运行状况

[启用诊断日志记录](#enable-diagnostic-logging)后，可以在安全中心内查看你的逻辑应用的运行状况。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，输入并选择“安全中心”。
1. 在安全中心仪表板菜单上的“常规”下选择“清单”。 
1. 在清单页上，筛选资产列表以仅显示逻辑应用资源。 在页面菜单中，选择“资源类型”&gt;“逻辑应用”。

   “运行不正常的资源”计数器显示安全中心认为不正常的逻辑应用数。
1.  在逻辑应用资源列表中，查看“建议”列。 若要查看特定逻辑应用的运行状况详细信息，请选择资源名称，或选择省略号按钮 (...) &gt;“查看资源”。
1.  若要修正任何潜在的资源运行状况问题，请执行针对你的逻辑应用列出的步骤。

如果已启用诊断日志记录，则可能是日志的目标存在问题。 查看[如何解决不同诊断日志记录目标的问题](#fix-diagnostic-logging-for-logic-apps)。

## <a name="fix-diagnostic-logging-for-logic-apps"></a>为逻辑应用修复诊断日志记录

如果你的[逻辑应用在安全中心内被列为“运行不正常”](#view-logic-apps-health-status)，请在 Azure 门户的“代码视图”中打开你的逻辑应用，或者通过 Azure CLI 这样做。 然后，在目标配置中检查你的诊断日志：[Azure Log Analytics](#log-analytics-and-event-hubs-destinations)、[Azure 事件中心](#log-analytics-and-event-hubs-destinations)或 [Azure 存储帐户](#storage-account-destination)。

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics 和事件中心目标

如果你使用 Log Analytics 或事件中心作为逻辑应用诊断日志的目标，请检查以下设置。 

1. 若要确认是否已启用诊断日志，请检查诊断设置 `logs.enabled` 字段是否设置为 `true`。 
1. 而若要确认未将存储帐户设置为目标，请检查 `storageAccountId` 字段是否设置为 `false`。

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

如果你使用某个存储帐户作为逻辑应用诊断日志的目标，请检查以下设置。

1. 若要确认是否已启用诊断日志，请检查诊断设置 `logs.enabled` 字段是否设置为 `true`。
1. 若要确认是否已为诊断日志启用保留策略，请检查 `retentionPolicy.enabled` 字段是否设置为 `true`。
1. 若要确认是否已将保留时间设置为 0-365 天，请检查 `retentionPolicy.days` 字段是否设置为 0 到 365（含两端）之间的数字。

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