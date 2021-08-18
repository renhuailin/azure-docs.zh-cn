---
title: 使用 Azure Monitor 日志监视 Azure Functions
description: 了解如何将 Azure Monitor 日志和 Azure Functions 结合使用来监视函数执行。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 194154f192cfcc91138b13bd37b37d80d38097d2
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126781"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视 Azure Functions

Azure Functions 提供与 [Azure Monitor 日志](../azure-monitor/logs/data-platform-logs.md)的集成来监视函数。 本文介绍如何配置 Azure Functions 以将系统生成的日志和用户生成的日志发送到 Azure Monitor 日志。

利用 Azure Monitor 日志，能够合并来自同一工作区中不同资源的日志，在这种情况下，可以使用[查询](../azure-monitor/logs/log-query-overview.md)对其进行分析，以快速检索、合并和分析收集的数据。  可以使用 Azure 门户中的 [Log Analytics](../azure-monitor/logs/log-query-overview.md) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](../azure-monitor/visualizations.md)或[警报规则](../azure-monitor/alerts/alerts-overview.md)配合使用。

Azure Monitor 使用 Azure 数据资源管理器使用的 [Kusto 查询语言](/azure/kusto/query/)的某个版本，该查询语言适用于简单的日志查询，但也包括高级功能，例如聚合、联接和智能分析。 可以通过[多个课程](../azure-monitor/logs/get-started-queries.md)快速了解此查询语言。

> [!NOTE]
> 与 Azure Monitor 日志的集成目前以公共预览版提供。 不支持在[版本 1.x](functions-versions.md) 上运行的函数应用。

## <a name="setting-up"></a>设置

1. 在 [Azure 门户](https://portal.azure.com)的函数应用的“监视”部分中，选择“诊断设置”，然后选择“添加诊断设置”。

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="选择“诊断设置”":::

1. 在“诊断设置”页的“类别详细信息”和“日志”下，选择“FunctionAppLogs”。

   FunctionAppLogs 表包含所需的日志。

1. 在“目标详细信息”下，选择“发送到 Log Analytics”，然后选择“Log Analytics 工作区”。 

1. 输入“诊断设置名称”，然后选择“保存”。

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="添加诊断设置":::

## <a name="user-generated-logs"></a>用户生成的日志

若要生成自定义日志，请使用特定于你的语言的日志记录语句。 下面提供了示例代码片段：


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>查询日志

若要查询生成的日志，请执行以下操作：
 
1. 在函数应用中，选择“诊断设置”。 

1. 从“诊断设置”列表中，选择配置用于将函数日志发送到的 Log Analytics 工作区。 

1. 从“Log Analytics 工作区”页面，选择“日志”。

   Azure Functions 将所有日志写入 LogManagement 下的 FunctionAppLogs 表中。 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Log Analytics 工作区中的查询窗口":::

下面提供了一些查询示例：

### <a name="all-logs"></a>所有日志

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>特定函数日志

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>例外

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Functions 概述](functions-overview.md)。
- 详细了解 [Azure Monitor 日志](../azure-monitor/logs/data-platform-logs.md)。
- 详细了解[查询语言](../azure-monitor/logs/get-started-queries.md)。
