---
title: 监视应用服务实例的运行状况
description: 了解如何使用运行状况检查监视应用服务实例的运行状况。
keywords: Azure 应用服务，Web 应用，运行状况检查，路由流量，正常实例，路径，监视，
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 3cc8ba29629c36cc9fcb295b1cdd348fbcae1584
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109732895"
---
# <a name="monitor-app-service-instances-using-health-check"></a>使用运行状况检查监视应用服务实例

![运行状况检查失败][2]

本文使用 Azure 门户中的运行状况检查来监视应用服务实例。 运行状况检查将删除运行不正常的实例，从而提高应用程序的可用性。 应该将[应用服务计划](./overview-hosting-plans.md)扩展到两个或更多实例，以使用运行状况检查。 运行状况检查路径应检查应用程序的关键组件。 例如，如果应用程序依赖于数据库和消息传递系统，则运行状况检查终结点应连接到这些组件。 如果应用程序无法连接到关键组件，则路径应返回介于 500 级别的响应代码，以指示应用运行不正常。

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
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Ping 故障的最大次数。 例如，当设置为 `2` 时，则在 `2` 次失败的 ping 操作后，将删除实例。 此外，纵向或横向扩展时，应用服务将对运行状况检查路径执行 ping 操作，确保新实例准备就绪。 |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | 为避免运行状况实例不堪重负，排除的实例不得过半。 例如，如果应用服务计划扩展到 4 个实例，且其中 3 个运行不正常，则最多排除 2 个。 其他 2 个实例（1 个运行正常的实例和 1 个运行不正常的实例）将继续接收请求。 在所有实例都运行不正常的最糟糕的情况下，将不排除任何实例。 若要重写此行为，请将应用设置设置为介于 `0` 和 `100` 之间的值。 值较高意味着将删除更多运行不正常的实例（默认值为 50）。 |

#### <a name="authentication-and-security"></a>身份验证和安全性

运行状况检查与应用服务的身份验证和授权功能相集成。 如果启用了这些安全功能，则不需要其他设置。 但是，如果使用自己的身份验证系统，则必须允许匿名访问运行状况检查路径。 如果站点启用了“仅限 HTTPS”，则将通过 HTTPS 发送运行状况检查请求。

大型企业的开发团队通常需要遵守已公开 API 的安全性要求。 若要保护运行状况检查终结点，应先使用 [IP 限制](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、[客户端证书](app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虚拟网络等功能来限制对应用程序的访问。 可以要求传入请求的 `User-Agent` 与 `HealthCheck/1.0` 匹配，以保护运行状况检查终结点。 由于先前的安全功能已对该请求进行了保护，因此无法冒名顶替用户代理。

## <a name="monitoring"></a>监视

提供应用程序的运行状况检查路径后，可以使用 Azure Monitor 监视站点的运行状况。 在门户的“运行状况检查”边栏选项卡中，单击顶部工具栏中的“指标” 。 此操作将打开新的边栏选项卡，可以在其中查看站点的历史运行状况以及新建预警规则。 有关监视站点的更多信息，请[参阅 Azure Monitor 指南](web-sites-monitor.md)。

## <a name="limitations"></a>限制

不应在高级函数站点上启用运行状况检查。 由于高级函数的快速扩展，运行状况检查请求可能会导致 HTTP 流量产生不必要的波动。 高级函数具有其自己的内部运行状况探测，用于通知扩展决策。

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
