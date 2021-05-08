---
title: 方案：通过网络虚拟设备 (NVA) 路由流量
titleSuffix: Azure Virtual WAN
description: 通过 NVA 路由流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519790"
---
# <a name="scenario-route-traffic-through-an-nva"></a>场景：通过 NVA 路由流量

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此 NVA 方案中，目标是要通过 NVA（网络虚拟设备）将分支的流量路由到 VNet 并将 VNet 的流量路由到分支。 有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

> [!NOTE]
> 如果你已经具有在新功能[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)变为可用之前设置的路由，请使用以下文章中关于这些版本的步骤：
>* [Azure 门户文章](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 文章](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>设计

在此方案中，我们将使用以下命名约定：

* “NVA Vnet”，用于用户已在其中部署了 NVA 并且已将其他虚拟网络作为分支连接的虚拟网络（本文后面的图 2 中的 VNet 2 和 VNet 4）。
* “NVA 分支”，用于连接到 NVA VNet的虚拟网络（本文后面图 2 中的 VNet 5、VNet 6、VNet 7 和 VNet 8）。
* “非 NVA Vnet”，用于连接到没有 NVA 或其他与之对等互连的 VNet 的虚拟 WAN 的虚拟网络（本文后面图 2 中的 VNet 1 和 VNet 3）。
* “中心”，用于 NVA Vnet 连接到的由 Microsoft 管理的虚拟 WAN 中心。 NVA 分支 Vnet 无需连接到虚拟 WAN 中心，只需要连接到 NVA Vnet。

下面的连接矩阵汇总了此方案中支持的流：

**连接矩阵**

| 源             | 到:|   NVA 分支|NVA VNet|非 NVA VNet|*分支*|
|---|---|---|---|---|---|
| NVA 分支   | &#8594; | 通过 NVA VNet | 对等互连 | 通过 NVA VNet | 通过 NVA VNet |
| NVA VNet    | &#8594; | 对等互连 | 直接 | 直接 | 直接 |
| 非 NVA VNet| &#8594; | 通过 NVA VNet | 直接 | 直接 | 直接 |
| **分支**     | &#8594; | 通过 NVA VNet | 直接 | 直接 | 直接 |

该连接矩阵中的每个单元格都描述了 VNet 或分支（流的“从”端，表中的行标题）如何与目标 VNet 或分支（流的“到”端，表中的列标题）通信。 “直接”表示由虚拟 WAN 以本机方式提供连接，“对等互连”表示由 VNet 中的用户定义的路由来提供连接，“通过 NVA VNet”表示该连接会穿过 NVA VNet 中部署的 NVA。 考虑以下情况：

* NVA 分支不由虚拟 WAN 来管理。 因此，这些分支与其他 Vnet 或分支通信时将采用的机制由用户来维护。 到 NVA VNet 的连接由 VNet 对等互连提供，并且指向 NVA 作为下一个跃点的到 0.0.0.0/0 的默认路由应该覆盖到 Internet 的、到其他轮幅的以及到分支的连接
* NVA VNet 会知道它们自己的 NVA 分支，但不会知道连接到其他 NVA VNet 的 NVA 分支。 例如，在本文后面图 2 中，VNet 2 知道 VNet 5 和 VNet 6，但不知道 VNet 7 和 VNet 8 等其他分支。 需要使用静态路由将其他分支的前缀注入到 NVA VNet 中
* 同样，分支和非 NVA VNet 不会知道任何 NVA 分支，因为 NVA 分支不会连接到虚拟 WAN 中心。 因此，这里还会需要静态路由。

考虑到 NVA 分支不受虚拟 WAN 管理，所有其他行都会显示相同的连接模式。 因此，单一的路由表（默认的路由表）将会执行以下操作：

* 虚拟网络（非中心 VNet 和用户中心 VNet）：
  * 关联的路由表：**默认**
  * 传播到路由表：**Default**
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：**默认**

但是，在此方案中，我们需要考虑要配置哪些静态路由。 每个静态路由都会有两个组成部分，一个部分位于虚拟 WAN 中心，指示虚拟 WAN 组件为每个分支使用哪个连接，另一个部分位于该特定连接中，指向分配到 NVA 的具体 IP 地址（或指向多个 NVA 前方的负载均衡器），如图 1 所示：

**图 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="图 1":::

这样，在默认表中需要的用于将流量发送到 NVA VNet 后方 NVA 分支的静态路由如下：

| 说明 | 路由表 | 静态路由              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | 默认     | 10.2.0.0/16 -> eastusconn |
| VNet 4       | 默认     | 10.4.0.0/16 -> weconn     |

现在，虚拟 WAN 知道要将数据包发送到哪个连接，但连接需要知道在接收这些包时要执行什么操作：这就是使用连接路由表的场合。 在这里，我们将使用较短的前缀（/24，而不是较长的 /16），以确保这些路由比从 NVA VNet（VNet 2 和 VNet 4）导入的路由更优先：

| 说明 | 连接 | 静态路由            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24 -> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24 -> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24 -> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 -> 10.4.0.5 |

现在，NVA VNet、非 NVA Vnet 和分支就知道如何访问所有的 NVA 分支了。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>体系结构

在图 2中，有两个中心：Hub1 和 Hub2  。

* Hub1 和 Hub2 直接连接到 NVA VNet（VNet 2 和 VNet 4）   。

* VNet 5 和 VNet 6 与 VNet 2 对等互连  。

* VNet 7 和 VNet 8 与 VNet 4 对等互连  。

* VNet 5、6、7、8 是间接分支，不直接连接到虚拟中心。

**图 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="图 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要通过 NVA 设置路由，请考虑执行以下步骤：

1. 标识 NVA 辐射 VNet 连接。 在图 2 中，它们是 VNet 2 连接 (eastusconn) 和 VNet 4 连接 (weconn)  。

   确保设置了 UDR：
   * 从 VNet 5 和 VNet 6 到 VNet 2 NVA IP
   * 从 VNet 7 和 VNet 8 到 VNet 4 NVA IP 
   
   不需要将 VNet 5、6、7 和 8 直接连接到虚拟中心。 确保 VNet 5、6、7、8 中的 NSG 允许分支 (VPN/ER/P2S) 的流量，或者允许连接到它们的远程 VNet 的 VNet 的流量。 例如，VNet 5、6 必须确保 NSG 允许本地地址前缀的以及连接到远程中心 2 的 VNet 7、8 的流量。

虚拟 WAN 不支持 VNet 5、6 连接到虚拟中心并通过 VNet 2 NVA IP 进行通信的方案；因此，需要将 VNet 5、6 连接到 VNet2，同样，将 VNet 7、8 连接到 VNet 4。

2. 将 VNet 2、5、6 的聚合静态路由条目添加到中心 1 的默认路由表。

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="示例":::

3. 在 VNet 2 的虚拟网络连接中为 VNet 5、6 配置静态路由。 若要为虚拟网络连接设置路由配置，请参阅[虚拟中心路由](how-to-virtual-hub-routing.md#routing-configuration)。

4. 将 VNet 4、7、8 的聚合静态路由条目添加到中心 1 的默认路由表。

5. 对于中心 2 的默认路由表，重复步骤 2、3 和 4。

这样将会导致路由配置更改，如图 3 所示。

**图 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="图 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
