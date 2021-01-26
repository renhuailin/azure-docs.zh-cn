---
title: 监视应用服务实例的运行状况
description: 了解如何使用运行状况检查监视应用服务实例的运行状况。
keywords: azure 应用服务，web 应用，运行状况检查，路由流量，正常实例，路径，监视，
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 60a210c6c336c1b820015304e8ab53bc894c17bf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792478"
---
# <a name="monitor-app-service-instances-using-health-check"></a>使用运行状况检查监视应用服务实例

![运行状况检查失败][2]

本文使用 Azure 门户中的运行状况检查来监视应用服务实例。 运行状况检查将删除不正常的实例，从而提高应用程序的可用性。 应该将 [应用服务计划](/overview-hosting-plans) 扩展到两个或更多实例，以使用运行状况检查。 运行状况检查路径应检查应用程序的关键组件。 例如，如果应用程序依赖于数据库和消息系统，则运行状况检查终结点应连接到这些组件。 如果应用程序无法连接到关键组件，路径应返回500级别的响应代码，以指示应用程序不正常。

## <a name="what-app-service-does-with-health-checks"></a>运行状况检查适用的应用服务

- 在应用上提供路径时，运行状况检查将在应用服务应用的所有实例上以1分钟为间隔 ping 执行此路径。
- 如果在两个或多个请求之后，实例未使用 200-299 (包含) 之间的状态代码进行响应，或者无法响应 ping，则系统会将其确定为不正常并将其删除。
- 删除后，运行状况检查将继续对不正常的实例进行 ping 操作。 如果继续响应失败，应用服务将重新启动基础 VM，以将实例恢复到正常状态。
- 如果实例在一小时内仍无法正常运行，则将其更换为新实例。
- 而且，在纵向扩展或缩减时，应用服务会对运行状况检查路径进行 ping 操作，以确保新实例已准备就绪。

> [!NOTE]
> 运行状况检查不遵循302重定向。 每小时最多更换一个实例，每个应用服务计划每天最多更换三个实例。
>

## <a name="enable-health-check"></a>启用运行状况检查

![Azure 门户中的运行状况检查导航][3]

- 若要启用运行状况检查，请浏览到 Azure 门户并选择应用服务应用。
- 在 " **监视**" 下，选择 " **运行状况检查**"。
- 选择“启用”，然后在应用程序上提供有效的 URL 路径，例如 `/health` 或 `/api/health`。
- 单击“保存”  。

> [!CAUTION]
> 运行状况检查配置更改将重新启动你的应用。 为了最大程度地降低对生产应用的影响，我们建议 [配置过渡槽](deploy-staging-slots.md) 并交换到生产环境。
>

### <a name="configuration"></a>配置

除了配置运行状况检查选项以外，还可以配置以下 [应用设置](configure-common.md)：

| 应用设置名称 | 允许的值 | 说明 |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Ping 故障的最大次数。 例如，当设置为时 `2` ，将在失败的 ping 操作后删除您的实例 `2` 。 此外，在纵向扩展或横向扩展时，应用服务会对运行状况检查路径进行 ping 操作，以确保新实例已准备就绪。 |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | 若要避免运行状况不佳的实例，则将不会排除超过一半的实例。 例如，如果将应用服务计划扩展为四个实例，三个实例运行不正常，则最多会排除两个实例。 另外两个实例 (一个正常，另一个不正常) 将继续接收请求。 在所有实例都运行不正常的最糟糕的情况下，将不包括任何实例。 若要重写此行为，请将应用设置设置为介于和之间的值 `0` `100` 。 值越高，会删除 (默认值为 50) 的更不正常的实例。 |

#### <a name="authentication-and-security"></a>身份验证和安全性

运行状况检查与应用服务的身份验证和授权功能集成。 如果启用这些安全功能，则不需要其他设置。 但是，如果使用自己的身份验证系统，运行状况检查路径必须允许匿名访问。 如果站点仅启用 HTTP **s**，则会通过 Http 发送运行状况检查 **请求。**

大型企业开发团队通常需要遵从公开 Api 的安全要求。 若要保护运行状况检查终结点，你应该首先使用诸如 [IP 限制](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、 [客户端证书](app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虚拟网络等功能来限制应用程序访问。 你可以通过要求传入请求的匹配项来保护运行状况检查终结点 `User-Agent` `ReadyForRequest/1.0` 。 该 User-Agent 不能被欺骗，因为该请求已被先前的安全功能保护。

## <a name="monitoring"></a>监视

提供应用程序的运行状况检查路径后，可以使用 Azure Monitor 来监视站点的运行状况。 在门户的“运行状况检查”边栏选项卡中，单击顶部工具栏中的“指标” 。 此操作将打开新的边栏选项卡，可以在其中查看站点的历史运行状况以及新建预警规则。 有关监视站点的更多信息，请[参阅 Azure Monitor 指南](web-sites-monitor.md)。

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
