---
title: 什么是 Azure 虚拟网络 IP 服务？
description: Azure 虚拟网络 IP 服务概述。 了解 IP 服务的工作原理以及如何在 Azure 中使用 IP 资源。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subService: ip-services
ms.topic: overview
ms.date: 10/01/2021
ms.custom: template-overview
ms.openlocfilehash: 4215fb4682e29a06a7003e30ae53656e09f6f246
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369152"
---
# <a name="what-is-azure-virtual-network-ip-services"></a>什么是 Azure 虚拟网络 IP 服务？

IP 服务是 IP 地址相关服务的集合，这些服务支持在 Azure 虚拟网络中进行通信。 Azure 中使用公共和专用 IP 地址进行资源之间的通信。 与资源的通信可以在专用 Azure 虚拟网络和公共 Internet 中进行。

IP 服务包括：

* 公共 IP 地址

* 公共 IP 地址前缀

* 专用 IP 地址

* 路由首选项

* 不按流量计费的路由首选项

## <a name="public-ip-addresses"></a>公共 IP 地址

Internet 资源使用公共 IP 与 Azure 中的资源进行入站通信。 公共 IP 地址可使用 IPv4 或 IPv6 地址创建。 你还可以选择使用 IPv4 和 IPv6 地址创建双堆栈部署。 公共 IP 地址以“标准”和“基本”SKU 提供 。 公共 IP 地址既可以静态分配，也可以动态分配。

公共 IP 地址是具有自身属性的资源。 可与公共 IP 地址关联的部分资源包括：

* 虚拟机网络接口
* 面向 Internet 的负载均衡器
* 虚拟网络网关 (VPN/ER)
* NAT 网关
* 应用程序网关
* Azure 防火墙
* 堡垒主机

有关公共 IP 地址的详细信息，请参阅[公共 IP 地址](../../virtual-network/public-ip-addresses.md)和[创建、更改或删除 Azure 公共 IP 地址](../../virtual-network/virtual-network-public-ip-address.md)

## <a name="public-ip-address-prefixes"></a>公共 IP 地址前缀

公共 IP 前缀是 Azure 中 IP 地址的保留范围。 公共 IP 地址前缀由 IPv4 或 IPv6 地址组成。  在部署了可用性区域的区域中，可以创建区域冗余的或与特定可用性区域关联的公共 IP 地址前缀。 创建公共 IP 前缀后，可以创建公共 IP 地址。

可使用以下公共 IP 前缀：

-  /28 (IPv4) 或 /124 (IPv6) = 16 个地址
-  /29 (IPv4) 或 /125 (IPv6) = 8 个地址
-  /30 (IPv4) 或 /126 (IPv6) = 4 个地址
-  /31 (IPv4) 或 /127 (IPv6) = 2 个地址

前缀大小指定为无类域间路由 (CIDR) 掩码大小。

在订阅中创建的前缀数量没有限制。 创建的范围数不能超过订阅中允许的静态公共 IP 地址数。 有关详细信息，请参阅 [Azure 限制](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

有关公共 IP 地址前缀的详细信息，请参阅[公共 IP 地址前缀](../../virtual-network/public-ip-address-prefix.md)和[创建、更改或删除公共 IP 地址前缀](../../virtual-network/manage-public-ip-address-prefix.md)

## <a name="private-ip-addresses"></a>专用 IP 地址

使用专用 IP 可在 Azure 中的资源之间进行通信。 Azure 从资源所在的虚拟网络子网的地址范围中为资源分配专用 IP 地址。 Azure 中的专用 IP 地址既可以静态分配，也可以动态分配。

可与专用 IP 地址关联的部分资源包括：

* 虚拟机
* 内部负载均衡器
* 应用程序网关
* 专用终结点

有关专用 IP 地址的详细信息，请参阅[专用 IP 地址](../../virtual-network/private-ip-addresses.md)。

## <a name="routing-preference"></a>路由首选项

Azure 路由首选项允许你选择流量在 Azure 和 Internet 之间的路由方式。 可以选择通过 Microsoft 网络或通过 ISP 网络（公共 Internet）来路由流量。 创建公共 IP 地址时，可以选择路由选项。 默认情况下，所有 Azure 服务的流量都是通过 Microsoft 全球网络路由的。 

路由首选项选项包括：

* **Microsoft 网络** - 大部分入口和出口流量传输都在 Microsoft 全球网络上进行。 此路由也称为“冷土豆路由”。

* **公共 Internet（ISP 网络）** - 新路由选择“Internet 路由”最大程度减少了在 Microsoft 全球网络上的传输历程，并使用传输 ISP 网络来路由流量。 此路由也称为“热土豆路由”。

有关路由首选项的详细信息，请参阅[什么是路由首选项？](../../virtual-network/routing-preference-overview.md)。

## <a name="routing-preference-unmetered"></a>不按流量计费的路由首选项

不按流量计费的路由首选项适用于其客户将其源内容托管在 Azure 中的内容分发网络 (CND) 提供商。 此服务允许 CDN 提供商在不同位置与 Microsoft 全局网络边缘路由器建立直接对等互连。

从 Azure 流出到 CDN 提供商的网络流量受益于直接连接。

* 通过这些直接链接路由的 Azure 资源中流出的流量的数据传输帐单是免费的。

* CDN 提供商和 Azure 中的原点之间的直接连接可提供最佳性能，因为之间没有跃点。 此连接有利于经常从原点提取数据的 CDN 工作负载。

有关不按流量计费的路由首选项的详细信息，请参阅[什么是不按流量计费的路由首选项？](../../virtual-network/routing-preference-unmetered.md)

## <a name="next-steps"></a>后续步骤

开始创建 IP 服务资源：

- [使用 Azure 门户创建公共 IP 地址](../../virtual-network/create-public-ip-portal.md)。
- [使用 Azure 门户创建公共 IP 地址前缀](../../virtual-network/create-public-ip-prefix-portal.md)。
- [使用 Azure 门户为 VM 配置专用 IP 地址](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。
- [使用 Azure 门户配置公共 IP 地址的路由首选项](../../virtual-network/routing-preference-portal.md)。
