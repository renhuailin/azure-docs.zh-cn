---
title: Azure 中的路由首选项
description: 了解如何使用路由首选项选择流量在 Azure 和 Internet 之间的路由方式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: 7fdd23aea5e435c94493a0cbe86b6ffcdb0d2e10
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539063"
---
# <a name="what-is-routing-preference-preview"></a>什么是路由首选项（预览版）？

Azure 路由首选项允许你选择流量在 Azure 和 Internet 之间的路由方式。 可以选择通过 Microsoft 网络或通过 ISP 网络（公共 Internet）来路由流量。 这些选项也分别称为“冷土豆路由”和“热土豆路由”。 出口数据传输价格因路由选择而有所不同。 创建公共 IP 地址时，可以选择路由选项。 公共 IP 地址可与虚拟机、虚拟机规模集、面向 Internet 的负载均衡器等资源相关联。还可以为 Azure 存储资源（例如 blob、文件、Web 和 Azure DataLake）设置路由首选项。 默认情况下，所有 Azure 服务的流量都是通过 Microsoft 全球网络路由的。

> [!IMPORTANT]
> 路由首选项目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="routing-via-microsoft-global-network"></a>通过 Microsoft 全球网络路由

当你通过 Microsoft 全球网络来路由流量时，流量将通过横跨超过 160,000 英里光纤的全球最大网络之一传送，该网络上有超过 165 个边缘接入点 (POP)。 该网络适当预配了多个冗余光纤路径，以确保极高的可靠性和可用性。 流量工程由软件定义的 WAN 控制器管理，确保为流量选择低延迟路径，并提供优质的网络性能。

![通过 Microsoft 全球网络路由](media/routing-preference-overview/route-via-microsoft-global-network.png)

入口流量：全球 BGP Anycast 公告确保入口流量进入距离用户最近的 Microsoft 网络。 例如，如果来自新加坡的用户访问托管在美国芝加哥的 Azure 资源，则流量将进入位于新加坡 Edge POP 的 Microsoft 全球网络，并通过 Microsoft 网络传输到托管在芝加哥的服务。

出口流量：出口流量遵循相同的原则。 流量传输大部分是在 Microsoft 全球网络上进行的，并在距离用户最近的网络退出。 例如，如果来自 Azure 芝加哥的流量最终传输给来自新加坡的用户，那么流量就会通过 Microsoft 网络从芝加哥传输到新加坡，并退出位于新加坡 Edge POP 的 Microsoft 网络。

大部分入口和出口流量传输都在 Microsoft 全球网络上进行。 这也称为“冷土豆路由”。


## <a name="routing-over-public-internet-isp-network"></a>通过公共 Internet 路由（ISP 网络）

新路由选择“Internet 路由”最大程度减少了在 Microsoft 全球网络上的传输历程，并使用传输 ISP 网络来路由流量。 这个经过成本优化的路由选项可提供与其他云提供商相当的网络性能。

![通过公共 Internet 路由](media/routing-preference-overview/route-via-isp-network.png)

入口流量：入口路径使用热土豆路由，这意味着流量进入最接近托管服务区域的 Microsoft 网络。 例如，如果来自新加坡的用户访问托管在芝加哥的 Azure 资源，则流量将通过公共 Internet 传输，并进入芝加哥的 Microsoft 全球网络。

出口流量：出口流量遵循相同的原则。 流量会在托管服务的同一区域退出 Microsoft 网络。 例如，如果来自 Azure 芝加哥服务的流量最终传输给来自新加坡的用户，流量将离开芝加哥的 Microsoft 网络，并通过公共 Internet 传输给新加坡的用户。

## <a name="supported-services"></a>支持的服务

带有路由首选项“Microsoft 全球网络”的公共 IP 可与任何 Azure 服务相关联。 但是，带有路由首选项“Internet”的公共 IP 可以与以下 Azure 资源关联：

* 虚拟机
* 虚拟机规模集
* Azure Kubernetes 服务 (AKS)
* 面向 Internet 的负载均衡器
* 应用程序网关
* Azure 防火墙

对于存储，主终结点始终使用“Microsoft 全球网络”。 可以使用“Internet”作为流量路由的选项来启用辅助终结点。 支持的存储服务包括：

* Blob
* 文件
* Web
* Azure DataLake

## <a name="pricing"></a>定价
这两个选项之间的价格差异反映在 Internet 出口数据传输定价中。 通过 Microsoft 全球网络路由的数据传输价格与当前 Internet 出口价格相同。 有关最新定价信息，请访问 [Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)。 

## <a name="limitations"></a>限制

* 路由首选项仅兼容公共 IP 地址的区域冗余标准 SKU。 不支持公共 IP 地址的基本 SKU。
* 路由首选项目前仅支持 IPv4 公共 IP 地址。 不支持 IPv6 公共 IP 地址。
* 具有多个 NIC 的虚拟机只能有一种类型的路由首选项。


## <a name="next-steps"></a>后续步骤

* [使用 Azure PowerShell 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-powershell.md)
* [使用 Azure CLI 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-cli.md)
