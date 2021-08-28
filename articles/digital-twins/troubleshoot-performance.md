---
title: 排查性能问题
titleSuffix: Azure Digital Twins
description: 排查 Azure 数字孪生实例性能问题的相关提示。
author: baanders
ms.author: baanders
ms.date: 5/11/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 252eeea15e5c9ebde19c5925423615cd7c50234c
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493092"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>排查 Azure 数字孪生的问题：性能

如果在使用 Azure 数字孪生时遇到延迟问题或其他性能问题，请借助本文中的提示来进行故障排除。

## <a name="isolate-the-source-of-the-delay"></a>分离延迟源

确定延迟是来自 Azure 数字孪生还是来自解决方案中的另一服务。 若要调查此延迟，可以通过 Azure 门户使用 [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md) 中的“API 延迟”指标。 有关如何查看 Azure 数字孪生实例的 Azure Monitor 指标的说明，请参阅[故障排除：使用 Azure Monitor 查看指标](troubleshoot-metrics.md)。

## <a name="check-regions"></a>检查区域

如果解决方案将 Azure 数字孪生与其他 Azure 服务（如 Azure Functions）结合使用，请检查每个服务的部署区域。 将服务部署在不同区域中可能会增加整个解决方案的延迟。 除非有意创建分布式解决方案，否则应考虑在同一区域内部署所有服务实例，以免意外引入延迟。

## <a name="check-logs"></a>检查日志

Azure 数字孪生可以收集服务实例的日志，以帮助监视其性能和其他数据。 日志可以发送到 [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) 或自定义存储机制。 若要在实例中启用日志记录，请使用[故障排除：设置诊断](troubleshoot-diagnostics.md)中的说明。 可以分析日志的时间戳来度量延迟、评估它们是否一致并了解其来源。

## <a name="check-api-frequency"></a>检查 API 频率

可能影响性能的另一个因素是重新授权 API 调用所用的时间。 请考虑 API 调用的频率。 如果两次调用之间的间隔超过 15 分钟，系统可能会对每次调用进行重新授权，因此需要额外的时间。 可以在代码中添加计时器或类似内容，以确保至少每 15 分钟调用一次 Azure 数字孪生，从而防止此问题的发生。

## <a name="contact-support"></a>联系支持人员

如果在使用上述步骤进行故障排除后仍遇到性能问题，可以通过“Azure 帮助 + 支持”创建支持请求，以获得更多故障排除帮助。 

执行以下步骤：

1. 收集实例的[指标](troubleshoot-metrics.md)和[日志](troubleshoot-diagnostics.md)。
2. 导航到 Azure 门户中的 [Azure 帮助 + 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 使用提示来提供问题详细信息，查看建议的解决方案，共享指标/日志文件，并提交支持团队可用于帮助调查问题的任何其他信息。 有关创建支持请求的详细信息，请参阅[创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解排查 Azure 数字孪生实例问题的其他方法：
* [故障排除：通过 Azure Monitor 查看指标](troubleshoot-metrics.md)
* 故障排除：设置诊断。
* [故障排除：设置警报](troubleshoot-alerts.md)
* [故障排除：了解资源运行状况](troubleshoot-resource-health.md)
