---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081055"
---
| properties | 说明 |
|--|--|
|**`<DESTINATION>`**| 日志发送到的目标。 有效值为 `AppInsights` 和 `Blob`。<br/>使用 `AppInsights` 时，请确保[在函数应用中启用 Application Insights](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration)。<br/>将目标设置为 `Blob` 时，将在名为 `azure-functions-scale-controller` 的 blob 容器中创建日志，该容器位于 `AzureWebJobsStorage` 应用程序设置中设置的默认存储帐户中。 |
|**`<VERBOSITY>`** | 指定日志记录级别。 支持的值为 `None`、`Warning` 和 `Verbose`。<br/>设置为 `Verbose` 时，缩放控制器将记录辅助角色计数每次更改的原因，以及有关将这些因素纳入决策的触发器的信息。 详细日志包含触发器警告和缩放控制器运行前后触发器使用的哈希。 |

> [!TIP]
> 请记住，将“缩放控制器日志记录”保留为启用时，它会影响[监视函数应用的潜在成本](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits)。 请考虑启用日志记录，直到收集到的数据足以了解缩放控制器的行为方式，然后将其禁用。
