---
title: 关于 VPN 网关中的 NAT（网络地址转换）
titleSuffix: Azure VPN Gateway
description: 了解 Azure VPN 中的 NAT（网络地址转换）以连接具有重叠地址空间的网络。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759561"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>关于 Azure VPN 网关中的 NAT（预览版）

本文概述了 Azure VPN 网关中的网络地址转换 (NAT)。 NAT 定义了在 IP 数据包中将一个 IP 地址转换为另一个 IP 地址的机制。 NAT 有多种方案：

* 连接具有重叠 IP 地址的多个网络
* 从具有专用 IP 地址 (RFC1918) 的网络连接到 Internet（Internet 分支）
* 从 IPv6 网络连接到 IPv4 网络 (NAT64)

> [!IMPORTANT]
> Azure VPN 网关 NAT 支持第一种方案，即将本地网络或分支机构连接到具有重叠 IP 地址的 Azure 虚拟网络。 不支持连接 Internet 分支和 NAT64。

## <a name="overlapping-address-spaces"></a><a name="why"></a>重叠的地址空间

组织通常使用 RFC1918 中定义的专用 IP 地址在专用网络中进行内部通信。 当这些网络通过 Internet 或专用 WAN 使用 VPN 进行连接时，地址空间不得重叠，否则通信将失败。 若要连接两个或多个具有重叠 IP 地址的网络，请在连接网络的网关设备上部署 NAT。 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>NAT 类型：静态和动态

网关设备上的 NAT 根据 NAT 策略或规则转换源和/或目标 IP 地址，以避免地址冲突。 有不同类型的 NAT 转换规则：

* **静态 NAT**：静态规则定义固定地址映射关系。 对于给定的 IP 地址，它将被映射到目标池中的相同地址。 静态规则的映射是无状态的，因为该映射是固定的。

* **动态 NAT**：对于动态 NAT，可以基于可用性，或者使用不同的 IP 地址和 TCP/UDP 端口组合将 IP 地址转换为不同的目标 IP 地址。 后者也称为 NAPT，即网络地址和端口转换。 动态规则将根据任何给定时间的流量流产生有状态的转换映射。

另一个注意事项是用于转换的地址池大小。 如果目标地址池大小与原始地址池相同，请使用静态 NAT 规则按顺序定义 1:1 映射。 如果目标地址池小于原始地址池，请使用动态 NAT 规则来适应差异。

> [!IMPORTANT]
> * Azure VPN 网关 NAT 仅支持静态的 1:1 NAT 规则。 不支持动态 NAT 规则。
> * 以下 SKU 支持 NAT：VpnGw2~5、VpnGw2AZ~5AZ.。
> * 仅支持在 IPsec 跨界连接中使用 NAT。 不支持 VNet 到 VNet 连接或 P2S 连接。

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>NAT 模式：入口和出口

每个 NAT 规则为相应的网络地址空间定义地址映射或转换关系：

* 入口：IngressSNAT 规则将本地网络地址空间映射到转换后的地址空间，以避免地址重叠。

* 出口：EgressSNAT 规则将 Azure VNet 地址空间映射到另一个已转换的地址空间。 

对于每个 NAT 规则，以下两个字段指定转换之前和之后的地址空间：

* **内部映射**：这是转换之前的地址空间。 对于入口规则，此字段对应于本地网络的原始地址空间。 对于出口规则，这是原始 VNet 地址空间。

* **外部映射**：这是本地网络（入口）或 VNet（出口）转换之后的地址空间。 对于连接到 Azure VPN 网关的不同网络，所有外部映射的地址空间不得相互重叠，也不得与没有 NAT 连接的网络重叠。

## <a name="nat-and-routing"></a><a name="routing"></a>NAT 和路由

为连接定义 NAT 规则后，连接的有效地址空间将随规则变化。 如果在 Azure VPN 网关上启用了 BGP，请选择“启用 BGP 路由转换”，以自动转换在具有 NAT 规则的连接上识别和播发的路由：

* 已识别的路由：通过与 IngressSNAT 规则的连接识别到的路由的目标前缀将从内部映射前缀（NAT 前）转换为这些规则的外部映射前缀（NAT 后）。

* 播发的路由：Azure VPN 网关将播发 VNet 地址空间的 EgressSNAT 规则的外部映射（NAT 后）前缀，以及来自其他连接，具有 NAT 后地址前缀的已识别的路由。

* NAT 本地网络的 BGP 对等 IP 地址注意事项：
   * APIPA（169.254.0.1 到 169.254.255.254）地址：无需 NAT 规则；直接在本地网关中指定 APIPA 地址。
   * 非 APIPA 地址：指定本地网络网关上转换后的或 NAT 后的 IP 地址 。 使用转换后的或 NAT 后的 Azure BGP IP 地址来配置本地 VPN 路由器 。 确保为预期转换定义了 NAT 规则。

> [!NOTE]
> 不会转换在没有 IngressSNAT 规则的连接上识别的路由。 也不会转换播发给没有 EgressSNAT 规则的连接的 VNet 路由。
>

## <a name="nat-example"></a><a name="example"></a>NAT 示例

下图显示了 Azure VPN NAT 配置的示例：

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="NAT 关系图。" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

该图显示了一个 Azure VNet 和两个本地网络，所有网络的地址空间均为 10.0.1.0/24。 若要将这两个网络连接到 Azure VNet 和 VPN 网关，请创建以下规则：

* IngressSNAT 规则 1：此规则将本地地址空间 10.0.1.0/24 转换为 100.0.2.0/24。

* IngressSNAT 规则 2：此规则将本地地址空间 10.0.1.0/24 转换为 100.0.3.0/24。

* EgressSNAT 规则 1：此规则将 VNet 地址空间 10.0.1.0/24 转换为 100.0.1.0/24。

图中的每个连接资源都具有以下规则：

* 连接 1 (VNet-Branch1)：
    * IngressSNAT 规则 1
    * EgressSNAT 规则 1

* 连接 2 (VNet-Branch2)
    * IngressSNAT 规则 2
    * EgressSNAT 规则 1

根据与连接关联的规则，以下是每个网络的地址空间：

| 网络  | 原始    | 已转换   |
| ---      | ---         | ---          |
| VNet     | 10.0.1.0/24 | 100.0.1.0/24 |
| 分支 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| 分支 2 | 10.0.1.0/24 | 100.0.3.0/24 |

下图显示了 NAT 转换之前和之后从分支 1 到 VNet 的 IP 数据包：

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="显示 NAT 转换之前和之后的关系图。" lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> 单个 SNAT 规则定义特定网络的双向转换：
>
> * IngressSNAT 规则定义从本地网络进入 Azure VPN 网关的源 IP 地址的转换。 它还会处理离开 VNet 并进入同一本地网络的目标 IP 地址的转换。
> * EgressSNAT 规则定义离开 Azure VPN 网关并进入本地网络的源 IP 地址的转换。 它还会处理使用 EgressSNAT 规则通过这些连接进入 VNet 的数据包的目标 IP 地址转换。
> * 在任一情况下，都不需要 DNAT 规则。

## <a name="nat-configuration"></a><a name="config"></a>NAT 配置

若要实现如上所示的 NAT 配置，请首先在 Azure VPN 网关中创建 NAT 规则，然后创建与相应 NAT 规则关联的连接。 有关为跨界连接配置 NAT 的步骤，请参阅[在 Azure VPN 网关上配置 NAT](nat-howto.md)。

## <a name="nat-faq"></a><a name="faq"></a>NAT 常见问题解答

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>后续步骤

有关为跨界连接配置 NAT 的步骤，请参阅[在 Azure VPN 网关上配置 NAT](nat-howto.md)。