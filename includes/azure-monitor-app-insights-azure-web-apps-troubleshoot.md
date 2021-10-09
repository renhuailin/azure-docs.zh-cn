---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698942"
---
### <a name="connection-string-and-instrumentation-key"></a>连接字符串和检测密钥

使用无代码监视时，只需要连接字符串。 但是，我们仍然建议设置检测密钥，以便在执行手动检测时保持与旧版 SDK 的后向兼容性。

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights 中的标准指标与 Azure 应用服务指标之间有何区别？

Application Insights 为向应用程序发出的那些请求收集遥测数据。 如果在 WebApps/WebServer 中发生故障，并且请求未到达用户应用程序，则 Application Insights 将不会有任何有关它的遥测数据。

Application Insights 算出的 `serverresponsetime` 持续时间不一定与 Web 应用观察到的服务器响应时间匹配。 这是因为 Application Insights 仅计算实际到达用户应用程序的持续时间。 如果请求在 WebServer 中停滞/排队，则该等待时间将包含在 Web 应用指标中，但不会包含在 Application Insights 指标中。


