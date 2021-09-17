---
title: 监视应用服务实例的运行状况
description: 了解如何使用运行状况检查监视应用服务实例的运行状况。
keywords: azure app service, web app, health check, route traffic, healthy instances, path, monitoring, remove faulty instances, unhealthy instances, remove workers
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 3efac96949d701bbc0147abf8712d4995f781d47
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771842"
---
# <a name="monitor-app-service-instances-using-health-check"></a>使用运行状况检查监视应用服务实例

本文使用 Azure 门户中的运行状况检查来监视应用服务实例。 运行状况检查通过重新路由来自不正常实例的请求，并替换仍然不正常的实例来提高应用程序的可用性。 应该将[应用服务计划](./overview-hosting-plans.md)扩展到两个或更多实例，以充分利用运行状况检查。 运行状况检查路径应检查应用程序的关键组件。 例如，如果应用程序依赖于数据库和消息传递系统，则运行状况检查终结点应连接到这些组件。 如果应用程序无法连接到关键组件，则路径应返回介于 500 级别的响应代码，以指示应用运行不正常。

![运行状况检查失败][1]

## <a name="what-app-service-does-with-health-checks"></a>应用服务利用运行状况检查进行哪些方面的监视

- 在应用上给出路径时，运行状况检查将以 1 分钟为间隔在应用服务应用的所有实例上对此路径执行 ping 操作。
- 如果在两个或多个请求之后，实例未使用 200-299（包含）之间的状态代码进行响应，或者无法响应 ping，则系统会将其确定为运行不正常并将其删除。
- 删除后，运行状况检查将继续对运行不正常的实例执行 ping 操作。 如果继续响应失败，应用服务将重新启动基础 VM，以将实例恢复到正常状态。
- 如果实例在一小时内仍无法正常运行，则将其更换为新实例。
- 此外，纵向或横向扩展时，应用服务将对运行状况检查路径执行 ping 操作，确保新实例准备就绪。

> [!NOTE]
> 运行状况检查不遵循 302 重定向。 每小时最多更换一个实例，每个应用服务计划每天最多更换三个实例。
>

## <a name="enable-health-check"></a>启用运行状况检查

![Azure 门户中的运行状况检查导航][3]

- 若要启用运行状况检查，请浏览到 Azure 门户并选择应用服务应用。
- 在“监视”下，选择“运行状况检查”。
- 选择“启用”，然后在应用程序上提供有效的 URL 路径，例如 `/health` 或 `/api/health`。
- 单击“保存”  。

> [!CAUTION]
> 运行状况检查配置更改将重新启动你的应用。 为了最大程度地降低对生产应用的影响，我们建议[配置过渡槽](deploy-staging-slots.md)并交换到生产环境。
>

### <a name="configuration"></a>配置

除了配置运行状况检查选项以外，还可以配置以下[应用设置](configure-common.md)：

| 应用设置名称 | 允许的值 | 说明 |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | 实例被视为不正常并从负载均衡器中删除所需的失败请求数。 例如，当设置为 `2` 时，则在 `2` 次失败的 ping 操作后，将删除实例。 （默认值为 `10`） |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | 为避免其余的正常实例不堪重负，从负载均衡器中排除的实例不得过半。 例如，如果应用服务计划扩展到 4 个实例，且其中 3 个运行不正常，则将排除 2 个。 其他 2 个实例（1 个运行正常的实例和 1 个运行不正常的实例）将继续接收请求。 在所有实例都运行不正常的最糟糕的情况下，将不排除任何实例。 <br /> 若要重写此行为，请将应用设置设置为介于 `0` 和 `100` 之间的值。 值较高意味着将删除更多运行不正常的实例（默认值为 `50`）。 |

#### <a name="authentication-and-security"></a>身份验证和安全性

运行状况检查与应用服务的[身份验证和授权功能](overview-authentication-authorization.md)相集成。 如果启用了这些安全功能，则不需要其他设置。

如果使用自己的身份验证系统，则必须允许匿名访问运行状况检查路径。 若要保护运行状况检查终结点，应先使用 [IP 限制](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、[客户端证书](app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虚拟网络等功能来限制对应用程序的访问。 可以要求传入请求的 `User-Agent` 与 `HealthCheck/1.0` 匹配，以保护运行状况检查终结点。 由于先前的安全功能已对该请求进行了保护，因此无法冒名顶替用户代理。

## <a name="monitoring"></a>监视

提供应用程序的运行状况检查路径后，可以使用 Azure Monitor 监视站点的运行状况。 在门户的“运行状况检查”边栏选项卡中，单击顶部工具栏中的“指标” 。 此操作将打开新的边栏选项卡，可以在其中查看站点的历史运行状况以及新建预警规则。 有关监视站点的更多信息，请[参阅 Azure Monitor 指南](web-sites-monitor.md)。

## <a name="limitations"></a>限制

- 不应在高级函数站点上启用运行状况检查。 由于高级函数的快速扩展，运行状况检查请求可能会导致 HTTP 流量产生不必要的波动。 高级函数具有其自己的内部运行状况探测，用于通知扩展决策。
- 可以为免费和共享应用服务计划启用运行状况检查，以便度量站点运行状况并设置警报，但由于免费和共享站点无法横向扩展，因此不会替换任何不正常的实例。    应纵向扩展到基本层或更高层，以便横向扩展到 2 个或更多实例，并充分利用运行状况检查的优势。 对于面向生产的应用程序，建议这样做，因为这样可提高应用的可用性和性能。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>如果我的应用在单个实例上运行，会发生什么情况？

如果应用仅扩展到一个实例并且变得不正常，则它将不会从负载均衡器中删除，因为这样会使应用程序完全崩溃。 横向扩展到两个或更多实例，以获得运行状况检查的重新路由优势。 如果应用在单个实例上运行，仍可使用运行状况检查的[监视](#monitoring)功能来跟踪应用程序的运行状况。
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>为什么前端日志中未显示运行状况检查请求？

运行状况检查请求在内部发送到站点，因此请求不会显示在[前端日志](troubleshoot-diagnostic-logs.md#enable-web-server-logging)中。 这也意味着请求的来源将为 `127.0.0.1`，因为该请求在内部发送。 可以在运行状况检查代码中添加日志语句，以保存运行状况检查路径 ping 时的日志。

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>运行状况检查请求是通过 HTTP 还是 HTTPS 发送？

如果站点启用了[“仅限 HTTPS”](configure-ssl-bindings.md#enforce-https)，则将通过 HTTPS 发送运行状况检查请求。 否则，将通过 HTTP 发送。

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>如果同一应用服务计划上有多个应用，该怎么做？

无论应用服务计划上的其他应用如何，始终会从负载均衡器轮换中删除不正常实例（最大删除百分比为 [`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration) 中指定的百分比）。 如果一个实例上的某个应用处于不正常状态超过一小时，仅当所有其他已启用运行状况检查的应用也不正常时，该实例才会被替换。 未启用运行状况检查的应用将不会被考虑在内。 

#### <a name="example"></a>示例 

假设你有两个启用了运行状况检查的应用程序（或一个带有槽的应用），分别称为“应用 A”和“应用 B”。它们位于同一应用服务计划中，并且该计划已横向扩展到 4 个实例。 如果应用 A 在两个实例上变得不正常，负载均衡器将停止向这两个实例上的应用 A 发送请求。 如果应用 B 正常，请求仍将路由到这些实例上的应用 B。 如果应用 A 在这两个实例上处于不正常状态超过一小时，仅当应用 B 在这些实例上也不正常时，这些实例才会被替换。 如果应用 B 正常，该实例将不会被替换。

![说明上述示例方案的直观图示。][2]

> [!NOTE]
> 如果计划（站点 C）中存在未启用运行状况检查的其他站点或槽，则不会考虑对其进行实例替换。

### <a name="what-if-all-my-instances-are-unhealthy"></a>如果所有实例都不正常，该怎么办？

如果应用程序的所有实例都不正常，应用服务会从负载均衡器中删除实例，最大删除百分比为 `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` 中指定的百分比。 在这种情况下，从负载均衡器轮换中删除所有不正常的应用实例实际上会导致应用程序出现故障。

### <a name="does-health-check-work-on-app-service-environments"></a>健康状况检查在应用服务环境中是否正常工作？

是，在应用服务环境 (ASE) 中，平台将在指定路径上 ping 实例，并从负载均衡器中删除任何运行不正常的实例，以便请求不会路由到这些实例。 但是，如果这些运行不正常的实例保持此状态 1 小时，则当前它们不会替换为新实例。

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [环境变量和应用设置参考](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
