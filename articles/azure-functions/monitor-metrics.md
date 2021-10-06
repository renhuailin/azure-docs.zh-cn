---
title: 将 Monitor 指标与 Azure Functions 配合使用
description: 了解如何使用 Azure Monitor 指标来查看和查询由 Azure Application Insights 收集并存储在其中的 Azure Functions 遥测数据。
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: 8f9e80a50e3041ea37d765938613563b31f16af0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215750"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>将 Azure Monitor 指标与 Azure Functions 配合使用

Azure Functions 与 Azure Monitor 指标集成，可让你分析函数应用在执行期间生成的指标。 有关详细信息，请参阅 [Azure Monitor 指标概述](../azure-monitor/essentials/data-platform-metrics.md)。 这些指标指示函数应用在应用服务平台上的运行状况。 可以查看用于估算消耗计划成本的资源消耗数据。 若要调查函数执行后提供的详细遥测数据（包括日志数据），还应使用 Azure Monitor 中的 [Application Insights](functions-monitoring.md)。 

## <a name="available-metrics"></a>可用度量值

Azure Monitor 从一组已进入时序数据库的受监视资源收集数字数据。 Azure Monitor 收集特定于 Functions 以及底层应用服务资源的指标。   

### <a name="functions-specific-metrics"></a>特定于 Functions 的指标

有两个特定于 Functions 的相关指标：

| 指标 | 说明 |
| ---- | ---- |
| **FunctionExecutionCount** | 函数执行计数表示函数应用已执行的次数。 此值与某个函数在应用中运行的次数相关联。 |
| **FunctionExecutionUnits** | 函数执行单位由执行次数和内存使用组成。  目前无法通过 Azure Monitor 获取内存数据这一指标。 但是，如果要优化应用的内存用量，可以使用 Application Insights 收集的性能计数器数据。 此指标目前不支持在 Linux 上运行的高级和专用（App 服务）计划。|

[估算消耗计划成本](functions-consumption-costs.md)时，会专门使用这些指标。 

### <a name="general-app-service-metrics"></a>常规应用服务指标

除了特定于函数的指标以外，应用服务平台还会实现其他可用于监视函数应用的指标。 有关完整列表，请参阅[可用于应用服务应用的指标](../app-service/web-sites-monitor.md#understand-metrics)。

## <a name="accessing-metrics"></a>访问指标

可以使用 [Azure 门户](https://portal.azure.com)中的 [Azure Monitor 指标资源管理器](../azure-monitor/essentials/metrics-getting-started.md)或使用 REST API 来获取 Monitor 指标数据。 

以下示例使用 Monitor 指标来帮助估算在消耗计划中运行函数应用所产生的成本。 若要详细了解如何估算消耗计划成本，请参阅[估算消耗计划成本](functions-consumption-costs.md)。

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

若要详细了解如何使用监视器资源管理器查看指标，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，以详细了解如何监视 Azure Functions：

+ [监视 Azure Functions](functions-monitoring.md)
+ [在 Application Insights 中分析 Azure Functions 遥测数据](analyze-telemetry-data.md)
