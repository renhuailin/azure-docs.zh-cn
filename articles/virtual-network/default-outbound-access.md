---
title: Azure 中的默认出站访问
titleSuffix: Azure Virtual Network
description: 了解 Azure 中的默认出站访问。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: f4c06fb4c97ea38c494f18e5bf72e9eb015299a8
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515767"
---
# <a name="default-outbound-access-in-azure"></a>Azure 中的默认出站访问

在 Azure 中，在未定义显式出站连接的虚拟网络中创建的虚拟机将获分配默认出站公共 IP 地址。 此 IP 地址允许启用从资源到 Internet 的出站连接。 此访问称为默认出站访问。 

显式出站连接的示例均为虚拟机：

* 在与 NAT 网关关联的子网中创建。
* 在定义了出站规则的标准负载均衡器后端池中。
* 在基本公共负载均衡器后端池中。
* 具有与之显式关联的公共 IP 地址的虚拟机。

## <a name="how-is-default-outbound-access-provided"></a>如何提供默认出站访问？

用于访问的公共 IPv4 地址称为默认出站访问 IP。 此 IP 是隐式的，属于 Microsoft。 此 IP 地址可能会更改，因此不建议在生产工作负荷中依赖它。

## <a name="when-is-default-outbound-access-provided"></a>何时提供默认出站访问？

如果在 Azure 中部署虚拟机，但该虚拟机没有显式出站连接，则为其分配默认出站访问 IP。
## <a name="why-is-disabling-default-outbound-access-recommended"></a>为什么建议禁用默认出站访问？

* 默认保护
    
    * 默认情况下，不建议使用零信任网络安全原则在 Internet 打开虚拟网络。

* 显式与隐式

    * 在授予对虚拟网络中资源的访问权限时，建议使用显式的连接方法，而不是隐式方法。

* IP 地址丢失

    * 默认出站访问 IP 不归客户所有。 此 IP 可能随时发生变化。  此 IP 上的任何依赖项都可能会导致未来出现问题。

## <a name="how-can-i-disable-default-outbound-access"></a>我要如何禁用默认出站访问？

有多种方法可以关闭默认出站访问：

1.  添加显式出站连接方法

    * 将 NAT 网关关联到虚拟机的子网。

    * 将标准负载均衡器与配置的出站规则关联。

    * 将公共 IP 关联到虚拟机的网络接口。

2.  对虚拟机规模集使用灵活业务流程模式。

    * 默认情况下，灵活的规模集是安全的。 通过灵活规模集创建的任何实例不会拥有与其关联的默认出站访问 IP。 如需了解详细信息，请参阅[适用于虚拟机规模集的灵活业务流程模式](https://aka.ms/vmssflex/docs)

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>如果我需要出站访问，有什么建议方法？

NAT 网关是建立显式出站连接的建议方法。 防火墙还可用于提供此访问权限。

## <a name="limitations"></a>限制

* Windows 更新可能需要进行连接。
* 默认出站访问 IP 不支持分段数据包。 

## <a name="next-steps"></a>后续步骤

有关 Azure 中的出站连接和 Azure 虚拟网络 NAT（NAT 网关），请参阅：

* [用于出站连接的源网络地址转换 (SNAT)](../load-balancer/load-balancer-outbound-connections.md)。

* [什么是 Azure 虚拟网络 NAT？](./nat-gateway/nat-overview.md)
