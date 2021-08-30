---
title: 在 Azure Functions 中流式传输执行日志
description: 了解如何近乎实时地流式传输函数的日志。
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurepowershell
ms.openlocfilehash: 6abf93be2d63434996fa2de7f400c742f9962575
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668452"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>在 Azure Functions 中启用流式传输执行日志功能

开发应用程序时，通常需要了解在 Azure 中运行时近实时地写入日志的内容。

可以通过两种方式查看由函数执行生成的日志文件流。

* **内置日志流式处理**：借助应用服务平台即可查看应用程序日志文件流。 这等效于在[本地开发](functions-develop-local.md)期间调试函数时以及在门户中使用“测试”选项卡时所显示的输出。 此时将显示所有基于日志的信息。 有关详细信息，请参阅[流式处理日志](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)。 这种流式处理方法仅支持单个实例，不能用于在消耗计划中的 Linux 上运行的应用。

* **实时指标流**：当函数应用 [连接到 Application Insights](configure-monitoring.md#enable-application-insights-integration) 时，可以使用 [实时指标流](../azure-monitor/app/live-stream.md)在 Azure 门户中近实时地查看日志数据和其他指标。 当监视在多个实例或消耗计划中的 Linux 上运行的函数时，请使用此方法。 此方法使用[抽样数据](configure-monitoring.md#configure-sampling)。

可以在门户和大多数本地开发环境中查看日志流。 

## <a name="portal"></a>门户

可以在门户中查看这两种类型的日志流。

### <a name="built-in-log-streaming"></a>内置日志流式处理

若要在门户中查看流式处理日志，请在函数应用中选择“平台功能”选项卡。 然后，在“监视”下，选择“日志流式处理” 。

![在门户中启用流式处理日志](./media/functions-monitoring/enable-streaming-logs-portal.png)

这会将应用连接到日志流式处理服务，窗口中将显示应用程序日志。 可以在“应用程序日志”和“Web 服务器日志”之间切换 。  

![在门户中查看流式处理日志](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>实时指标流

若要查看应用的实时指标流，请选择函数应用的“概述”选项卡。 Application Insights 启用后，“配置的功能”下将显示“Application Insights”链接 。 使用此链接将转到应用的“Application Insights”页。

在 Application Insights 中，选择“实时指标流”。 “示例遥测”下降显示[采样日志条目](configure-monitoring.md#configure-sampling)。

![在门户中查看实时指标流](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 启用流式处理日志。 使用以下命令登录，选择订阅并流式传输日志文件：

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

可以使用 [Azure PowerShell](/powershell/azure/) 启用流式处理日志。 对于 PowerShell，请使用 [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) 命令在函数应用上启用日志记录，如以下代码片段所示： 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

有关详细信息，请参阅[完整代码示例](../app-service/scripts/powershell-monitor.md#sample-script)。 

## <a name="next-steps"></a>后续步骤

+ [监视 Azure Functions](functions-monitoring.md)
+ [在 Application Insights 中分析 Azure Functions 遥测数据](analyze-telemetry-data.md)
