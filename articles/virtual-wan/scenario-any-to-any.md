---
title: 方案： any 到 any
titleSuffix: Azure Virtual WAN
description: 用于路由的方案 - 任意位置到任意位置
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: a866c21e067293481a52dd563873892de8b5444c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955269"
---
# <a name="scenario-any-to-any"></a>方案：任意位置到任意位置

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在“任意位置到任意位置”方案中，任意分支都可以联系另一个分支。 如果存在多个中心，默认情况下会在标准虚拟 WAN 中启用中心到中心路由（也称为“中心间”）。 可以使用多种不同的方法（例如 Azure 门户或 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways/)）来创建此配置。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。 

## <a name="design"></a><a name="design"></a>设计

为了确定虚拟 WAN 方案中将会需要多少路由表，可以构建一个连接矩阵，其中每个单元格都表示源（行）是否可以与目标（列）通信。

| 源 |   目标 |  VNet | *分支* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;| 直接 | 直接 |
| 分支   | &#8594;| 直接  | 直接 |

上表中的各单元格描述了虚拟 WAN 连接（流的“源”端，行标题）是否与目标前缀（流的“目标”端，斜体形式的列标题）通信。 在此方案中，没有防火墙或网络虚拟设备，因此通信直接通过虚拟 WAN 进行（因此在表中使用“直接”一词）。

由于来自 VNet 和分支（VPN、ExpressRoute 和用户 VPN）的所有连接都具有相同的连接要求，因此需要单个路由表。 这样，所有的连接都将会关联，并将传播到同一个路由表，即 Default 路由表：

* 虚拟网络：
  * 关联的路由表：**Default**
  * 传播到路由表：**Default**
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：**Default**

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>体系结构

在图 1 中，所有 VNet 和分支（VPN、ExpressRoute、P2S）都可以相互联系。 在虚拟中心，连接的工作方式如下：

* VPN 连接将 VPN 站点连接到 VPN 网关。
* 虚拟网络连接将虚拟网络连接到虚拟中心。 虚拟中心的路由器提供 VNet 之间的传输功能。
* ExpressRoute 连接将 ExpressRoute 线路连接到 ExpressRoute 网关。

这些连接（默认在创建时）都会关联到 Default 路由表，除非已将连接的路由配置设置为“无”，或者设置为自定义路由表。 这些连接也传播路由，默认情况下传播到 Default 路由表。 这种方式实现了“任意位置到任意位置”方案，其中任何分支（VNet、VPN、ER、P2S）都可以相互联系。

**图 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="图 1":::

## <a name="workflow"></a><a name="workflow"></a>工作流

对于标准虚拟 WAN，默认情况下会启用此方案。 如果 WAN 配置中禁用了分支到分支的设置，则不会在分支轮辐之间建立连接。 VPN/ExpressRoute/用户 VPN 在虚拟 WAN 中被视为分支

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
