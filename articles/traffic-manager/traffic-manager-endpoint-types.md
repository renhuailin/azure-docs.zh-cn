---
title: 流量管理器终结点类型 | Microsoft 文档
description: 本文介绍可以通过 Azure 流量管理器来使用的不同类型的终结点
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051221"
---
# <a name="traffic-manager-endpoints"></a>流量管理器终结点

使用 Microsoft Azure 流量管理器可以控制如何将网络流量分布到在不同数据中心运行的应用程序部署。 需要在流量管理器中将每个应用程序部署配置为一个“终结点”。 当流量管理器收到 DNS 请求时，将选择要在 DNS 响应中返回的可用终结点。 流量管理器根据当前终结点状态和流量路由方法做出这种选择。 相关详细信息，请参阅[流量管理器工作原理](traffic-manager-how-it-works.md)。

流量管理器支持三种类型的终结点：

* **Azure 终结点** 用于在 Azure 中托管的服务。
* **外部终结点** 用于 IPv4/IPv6 地址、fqdn 或在 Azure 外部托管的服务。 这些服务可以是本地服务，也可以是其他宿主提供程序。
* **嵌套终结点** 用于将流量管理器配置文件组合在一起来创建更灵活的流量路由方案，从而满足更大、更复杂部署的需求。

对于不同类型的终结点在单个流量管理器配置文件中的组合方式没有限制。 每个配置文件都可以包含任何组合形式的终结点类型。

以下各节更进一步地描述了每个终结点类型。

## <a name="azure-endpoints"></a>Azure 终结点

Azure 终结点用于流量管理器中基于 Azure 的服务。 支持以下 Azure 资源类型：

* PaaS 云服务。
* Web 应用
* Web 应用槽
* PublicIPAddress 资源（可直接或通过 Azure 负载均衡器连接到 VM）。 必须为 publicIpAddress 分配一个 DNS 名称，才能在流量管理器配置文件中使用它。

PublicIPAddress 资源属于 Azure 资源管理器资源。 它们不存在于经典部署模型中，仅在流量管理器的 Azure 资源管理器体验中受支持。 其他终结点类型通过 Resource Manager 和经典部署模型受到支持。

使用 Azure 终结点时，流量管理器可检测 Web 应用的停止和启动时间。 此状态反映在终结点状态中。 有关详细信息，请参阅[流量管理器终结点监视](traffic-manager-monitoring.md#endpoint-and-profile-status)。 当基础服务停止时，流量管理器不会执行终结点运行状况检查或将流量定向到终结点。 已停止的实例不会发生流量管理器计费事件。 重新启动服务后，计费会恢复，终结点可以接收流量。 此检测不适用于 PublicIpAddress 终结点。

## <a name="external-endpoints"></a>外部终结点

外部终结点用于 IPv4/IPv6 地址、FQDN，或用于 Azure 外部的服务。 使用 IPv4/IPv6 地址终结点允许流量管理器检查终结点的运行状况，而无需为其指定 DNS 名称。 因此，在响应中返回终结点时，流量管理器可以使用 A/AAAA 记录响应查询。 Azure 外部的服务可以包括本地托管的服务或通过其他提供商托管的服务。 外部终结点可以单独使用，也可以在同一流量管理器配置文件中与 Azure 终结点结合使用。 此异常适用于指定为 IPv4 或 IPv6 地址的终结点，这些终结点只能是外部终结点。 可以将 Azure 终结点与外部终结点结合用于多种方案：

* 在主动-主动或主动-被动故障转移模型中，可以使用 Azure 为现有的本地应用程序提供增强的冗余。 
* 将流量路由到没有与其关联的 DNS 名称的终结点。 还可以通过无需运行另一个 DNS 查询来获取返回的 DNS 名称的 IP 地址，从而减少整个 DNS 查找滞后时间。
* 降低世界各地用户的应用程序延迟，将现有的本地应用程序扩展到 Azure 中的其他地理位置。 有关详细信息，请参阅[流量管理器“性能”流量路由](traffic-manager-routing-methods.md#performance)。
* 为现有的本地应用程序提供更大的容量，无论是连续使用，还是作为 "突发到云" 解决方案，使用 Azure 实现需求高峰。

在某些情况下，使用外部终结点来引用 Azure 服务会很有用。 有关示例，请参阅 [常见问题解答](traffic-manager-faqs.md#traffic-manager-endpoints) 。 运行状况检查按 Azure 终结点费率计费，而不是按外部终结点费率计费。 与 Azure 终结点不同，如果停止或删除基础服务，运行状况检查将继续计费。 在流量管理器中禁用或删除终结点后，计费将停止。

## <a name="nested-endpoints"></a>嵌套式终结点

嵌套的终结点合并多个流量管理器配置文件，创建灵活的流量路由方案，以支持更大和更复杂部署的需求。 使用嵌套式终结点时，会将“子”配置文件作为终结点添加到“父”配置文件。 子配置文件和父配置文件可包含任何类型的其他终结点，包括其他嵌套式配置文件。 

有关详细信息，请参阅[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)。

## <a name="web-apps-as-endpoints"></a>Web 应用作为终结点

在流量管理器中将 Web 应用配置为终结点时，需要考虑一些更多注意事项：

1. 仅“标准”SKU 或更高版 SKU 的 Web 应用可以用于流量管理器。 尝试添加 SKU 较低的 Web 应用会失败。 降低现有 Web 应用的 SKU 会导致流量管理器不再将流量发送到该 Web 应用。 有关支持的计划的详细信息，请参阅 [应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. 当某个终结点收到 HTTP 请求时，将使用请求中的“host”标头来确定应通过哪个 Web 应用来处理请求。 主机标头包含用于启动请求的 DNS 名称，例如 "contosoapp.azurewebsites.net"。 要对 Web 应用使用其他 DNS 名称，必须将该 DNS 名称注册为该应用的自定义域名。 将 Web 应用终结点添加为 Azure 终结点时，系统会自动为该应用注册流量管理器配置文件 DNS 名称。 删除终结点时，会自动删除该注册。
3. 每个流量管理器配置文件最多允许一个 Azure 区域有一个 Web 应用终结点。 若要克服这种约束，可为外部终结点配置一个 Web 应用。 有关详细信息，请参阅[常见问题解答](traffic-manager-faqs.md#traffic-manager-endpoints)。

## <a name="enabling-and-disabling-endpoints"></a>启用和禁用终结点

在流量管理器中禁用终结点可能有助于从处于维护模式或正在重新部署的终结点中临时删除流量。 在终结点再次运行后，可以重新启用该终结点。

可以通过流量管理器门户、PowerShell、CLI 或 REST API 来启用或禁用终结点。

> [!NOTE]
> 禁用某个 Azure 终结点对其在 Azure 中的部署状态没有任何影响。 Azure 服务（例如 VM 或 Web 应用）将保持运行并可接收流量，即使已在流量管理器中禁用。 流量可直接定向到该服务实例，而无需通过流量管理器配置文件 DNS 名称。 有关详细信息，请参阅 [流量管理器的工作原理](traffic-manager-how-it-works.md)。

目前，每个终结点接收流量的资格取决于以下因素：

* 配置文件状态（已启用/已禁用）
* 终结点状态（已启用/已禁用）
* 该终结点的运行状况检查结果

相关详细信息，请参阅[流量管理器终结点监视](traffic-manager-monitoring.md#endpoint-and-profile-status)。

> [!NOTE]
> 由于流量管理器是在 DNS 级别工作的，因此不可能影响任何终结点的现有连接。 当某个终结点不可用时，流量管理器会将新连接定向到另一个可用的终结点。 不过，已禁用或不正常的终结点后面的主机仍可通过现有连接继续接收流量，直到相关会话被终止。 应用程序应该限制会话持续时间，以便耗尽现有连接的流量。

如果配置文件中的所有终结点都处于禁用状态，或者配置文件本身被禁用，则流量管理器会向新的 DNS 查询发送 "NXDOMAIN" 响应。

## <a name="faqs"></a>常见问题解答

* [能否将流量管理器用于多个订阅的终结点？](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [能否将流量管理器用于云服务的“过渡”槽？](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [流量管理器是否支持 IPv6 终结点？](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [流量管理器是否可用于同一区域中的多个 Web 应用？](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [如何将流量管理器配置文件的 Azure 终结点移动到其他资源组？](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>后续步骤

* 了解 [流量管理器的工作原理](traffic-manager-how-it-works.md)。
* 了解流量管理器[终结点监视和自动故障转移](traffic-manager-monitoring.md)。
* 了解流量管理器[流量路由方法](traffic-manager-routing-methods.md)。