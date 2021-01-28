---
title: 使用自定义设置通过 Nva 路由流量
titleSuffix: Azure Virtual WAN
description: 此方案可帮助你通过 Nva 使用不同的 NVA 路由流量来路由流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938577"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>方案：使用自定义设置通过 Nva 路由流量

使用 Azure 虚拟 WAN 虚拟中心路由时，有许多选项可供使用。 本文的重点是，如果你想要通过网络虚拟设备路由流量 (NVA) 用于虚拟网络和分支之间的通信，并为 internet 绑定流量使用不同的 NVA。 有关详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a>设计

* 连接到虚拟中心的虚拟网络的 **辐射**。 本文后面的关系图中 (例如 VNet 1、VNet 2 和 VNet 3。 ) 
* 适用于虚拟网络的 **服务 VNet** ，其中用户已部署 NVA 以检查非 internet 流量，并可能包含轮辐访问的公共服务。  (例如，本文后面的关系图中的 VNet 4。 )  
* 适用于虚拟网络的 **外围 VNet** ，其中用户已部署了 NVA，用于检查 internet 绑定的流量。  (例如，本文后面的关系图中的 VNet 5。 ) 
* 适用于 Microsoft 托管的虚拟 WAN 中心的 **中心**。

下表总结了此方案中支持的连接：

| From          | 功能|分支|服务 VNet|分支|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **分支**| ->| 直接提交 |直接提交 | 通过服务 VNet |通过外围 VNet |
| **服务 VNet**| ->| 直接提交 |不适用| 直接提交 | |
| **分支** | ->| 通过服务 VNet |直接提交| 直接提交 |  |

连接矩阵中的每个单元都说明了连接是直接通过虚拟广域网还是使用 NVA 在其中一个虚拟网络上流动。 

请注意下列详细信息：
* 辐射
  * 轮辐会直接通过虚拟 WAN 中心访问其他轮辐。
  * 轮辐将通过指向服务 VNet 的静态路由来连接到分支。 它们不会从分支中了解特定的前缀，因为这些前缀更为具体，并覆盖了摘要。
  * 轮辐通过直接 VNet 对等互连将 internet 流量发送到外围 VNet。
* 分支将通过指向服务 VNet 的静态路由来进入辐射。 它们不会从虚拟网络中了解用于替代已汇总静态路由的特定前缀。
* 服务 VNet 将类似于需要从每个虚拟网络和每个分支访问的共享服务 VNet。
* 外围网络不需要通过虚拟 WAN 建立连接，因为它只支持直接虚拟网络对等互连。 不过，若要简化配置，请使用与外围 VNet 相同的连接模型。

有三种不同的连接模式，它们转换为三个路由表。 与不同虚拟网络的关联是：

* 辐射
  * 关联的路由表： **RT_V2B**
  * 传播到路由表： **RT_V2B** 和 **RT_SHARED**
* NVA Vnet (Service VNet 和 DMZ VNet) ：
  * 关联的路由表：RT_SHARED
  * 传播到路由表： **RT_SHARED**
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：RT_SHARED 和 Default

> [!NOTE] 
> 请确保辐射 Vnet 不会传播到默认标签。 这可确保从分支到辐射 Vnet 的流量将转发到 Nva。

这些静态路由确保进出虚拟网络和分支的流量经过服务 VNet (VNet 4) 中的 NVA：

| 说明 | 路由表 | 静态路由              |
| ----------- | ----------- | ------------------------- |
| 分支    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA 轮辐  | 默认     | 10.1.0.0/16-> vnet4conn  |

现在，可以使用虚拟 WAN 选择正确的连接以将数据包发送到。 还需要使用虚拟 WAN 来选择接收这些数据包时要执行的正确操作。 为此使用连接路由表，如下所示：

| 说明 | 连接 | 静态路由            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0。5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0。5 |

有关详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a>体系结构

下面是本文前面所述的体系结构示意图。

有一个集线器，称为 **中心 1**。

* **集线器 1** 直接连接到 NVA vnet **Vnet 4** 和 **vnet 5**。

* Vnet 1、2和3之间的流量应通过 **VNet 4 NVA** 10.4.0.5。

* 来自 Vnet 1、2和3的所有 internet 绑定流量应通过 **VNet 5 NVA** 10.5.0.5。

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="网络体系结构示意图。":::

## <a name="workflow"></a>工作流

若要通过 NVA 设置路由，请执行以下步骤：

1. 要使 internet 绑定的流量通过 VNet 5，需要 Vnet 1、2和3才能直接通过虚拟网络对等互连连接到 VNet 5。 还需要在虚拟网络中为 0.0.0.0/0 和下一跃点10.5.0.5 设置用户定义的路由。 目前，虚拟 WAN 不允许虚拟中心的下一跃点 NVA 0.0.0.0/0。

1. 在 Azure 门户中，请前往虚拟中心，并创建名为 **RT_Shared** 的自定义路由表。 此表通过从所有虚拟网络和分支连接传播的传播了解路由。 可以在下图中看到此空表。

   * **路由：** 无需添加任何静态路由。

   * **关联：** 选择 Vnet 4 和5，这意味着这些虚拟网络的连接与 **RT_Shared** 的路由表相关联。

   * **传播：** 因为你希望所有分支和虚拟网络连接都动态地将其路由传播到此路由表，请选择 "分支和所有虚拟网络"。

1. 创建名为 **RT_V2B** 的自定义路由表，用于将流量从 vnet 1、2和3定向到分支。

   * **路由：** 为分支添加聚合静态路由条目，并将下一个跃点作为 VNet 4 连接。 为分支前缀在 VNet 4 的连接中配置静态路由，并指示下一跃点是 VNet 4 中 NVA 的特定 IP。

   * **关联：** 选择所有 Vnet 1、2和3。 这意味着 VNet 连接1、2和3将与此路由表相关联，并且可以通过传播) 此路由表中的传播来了解 (静态和动态的路由。

   * **传播：** 连接将路由传播到路由表。 选择 Vnet 1、2和3会启用将路由从 Vnet 1、2和3传播到此路由表。 无需将路由从分支连接传播到 **RT_V2B**，因为分支虚拟网络流量通过 VNet 4 中的 NVA。
  
1. 编辑默认路由表 **DefaultRouteTable**。

   所有 VPN、Azure ExpressRoute 和用户 VPN 连接都与默认路由表相关联。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。

   * **路由：** 为 Vnet 1、2和3添加聚合静态路由条目，并将下一个跃点作为 VNet 4 连接。 为 VNet 1、2和3聚合的前缀在 VNet 4 的连接中配置静态路由，并指示下一跃点是 VNet 4 中 NVA 的特定 IP。

   * **关联：** 请确保选择 "分支 (VPN/ER/P2S) 的选项，确保本地分支连接与默认路由表相关联。

   * **传播自：** 请确保选择 "分支 (VPN/ER/P2S) 的选项，确保本地连接正在将路由传播到默认路由表。

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="工作流关系图。" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
