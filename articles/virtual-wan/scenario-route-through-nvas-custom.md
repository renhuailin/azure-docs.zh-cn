---
title: 使用自定义设置通过 Nva 路由流量
titleSuffix: Azure Virtual WAN
description: 此方案可帮助你通过 Nva 使用不同的 NVA 路由流量来路由流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8916fbc7c2a0b9789dcc73697324cee370f1fc1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704899"
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
|---|:---:|:---:|:---:|:---:|:---:|
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
> 请确保辐射 Vnet 不会传播到默认标签。 这可确保从分支到辐射 Vnet 的流量转发到 Nva。

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

下图显示了本文前面所述的体系结构。

在关系图中，有一个中心; **中心 1**。

* **集线器 1** 直接连接到 NVA vnet **Vnet 4** 和 **vnet 5**。

* Vnet 1、2、3和分支之间的流量应通过 **VNet 4 NVA** 10.4.0.5。

* 来自 Vnet 1、2和3的所有 internet 绑定流量应通过 **VNet 5 NVA** 10.5.0.5。

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="网络体系结构示意图。":::

## <a name="workflow"></a><a name="workflow"></a>工作流

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="工作流关系图。" lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

若要通过 NVA 设置路由，请考虑以下步骤：

1. 要使 internet 绑定的流量通过 VNet 5，需要 Vnet 1、2和3才能直接通过虚拟网络对等互连连接到 VNet 5。 还需要在虚拟网络中为 0.0.0.0/0 和下一跃点10.5.0.5 设置用户定义的路由。

   如果不想将 Vnet 1、2和3连接到 VNet 5，而只是在 VNet 4 中使用 NVA 来路由 0.0.0.0/0 流量 (本地 VPN 或 ExpressRoute 连接) ，请转到 [备用工作流](#alternate)。

   但是，如果你想要通过 NVA 传输 VNet 到 VNet 流量，则需要将 VNet 1、2、3从虚拟中心断开连接，并将其连接到 NVA 辐射 VNet4 上，或将其堆栈到它上面。 在虚拟 WAN 中，VNet 到 VNet 流量上流动通过虚拟 WAN 集线器或虚拟 WAN 集线器的 Azure 防火墙 (安全中心) 。 如果 Vnet 对等节点直接使用 VNet 对等互连，则它们可以直接通过虚拟中心直接进行通信。

1. 在 Azure 门户中，请前往虚拟中心，并创建名为 **RT_Shared** 的自定义路由表。 此表通过从所有虚拟网络和分支连接传播的传播了解路由。 可以在下图中看到此空表。

   * **路由：** 无需添加任何静态路由。

   * **关联：** 选择 Vnet 4 和5，这意味着这些虚拟网络的连接与 **RT_Shared** 的路由表相关联。

   * **传播：** 因为你希望所有分支和虚拟网络连接都动态地将其路由传播到此路由表，请选择 "分支和所有虚拟网络"。

1. 创建名为 **RT_V2B** 的自定义路由表，用于将流量从 vnet 1、2和3定向到分支。

   * **路由：** 为分支添加聚合静态路由条目，并将下一个跃点作为 VNet 4 连接。 在 VNet 4 的连接中为分支前缀配置静态路由。 将下一跃点指定为 VNet 4 中 NVA 的特定 IP。

   * **关联：** 选择所有 **vnet 1、2和 3**。 这意味着 VNet 连接1、2和3将与此路由表相关联，并且可以通过传播) 此路由表中的传播来了解 (静态和动态的路由。

   * **传播：** 连接将路由传播到路由表。 选择 Vnet 1、2和3，可将路由从 Vnet 1、2和3传播到此路由表。 无需将路由从分支连接传播到 **RT_V2B**，因为分支虚拟网络流量通过 VNet 4 中的 NVA。
  
1. 编辑默认路由表 **DefaultRouteTable**。

   所有 VPN、Azure ExpressRoute 和用户 VPN 连接都与默认路由表相关联。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。

   * **路由：** 为 Vnet 1、2和3添加聚合静态路由条目，并将下一个跃点作为 VNet 4 连接。 为 VNet 1、2和3聚合前缀配置 VNet 4 的连接中的静态路由。 将下一跃点指定为 VNet 4 中 NVA 的特定 IP。

   * **关联：** 请确保选择 "分支 (VPN/ER/P2S) 的选项，确保本地分支连接与默认路由表相关联。

   * **传播自：** 请确保选择 "分支 (VPN/ER/P2S) 的选项，确保本地连接正在将路由传播到默认路由表。

## <a name="alternate-workflow"></a><a name="alternate"></a>备用工作流

在此工作流中，你不将 Vnet 1、2和3连接到 VNet 5。 相反，请使用 VNet 4 中的 NVA 来路由 0.0.0.0/0 流量 (本地 VPN 或 ExpressRoute 连接) 的分支。

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="备用工作流关系图。" lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

若要通过 NVA 设置路由，请考虑以下步骤：

1. 在 Azure 门户中，请前往虚拟中心，并创建名为 **RT_NVA** 的自定义路由表，用于通过 NVA 10.4.0.5 定向流量  

   * **路由：**  无需任何操作。

   * **关联：**  选择 **VNet4**。 这意味着 VNet 连接4将与此路由表相关联，并且可以通过传播) 此路由表中的传播来了解 (静态和动态的路由。

   * **传播：**  连接将路由传播到路由表。 选择 Vnet 1、2和3，可将路由从 Vnet 1、2和3传播到此路由表。 选择 "分支" (VPN/ER/P2S) 将启用从分支/本地连接传播到此路由表的路由。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。

1. 创建名为 **RT_VNET** 的自定义路由表，用于将流量从 vnet 1、2和3定向到分支或 internet (0.0.0.0/0) 通过 VNet4 NVA。 VNet 到 VNet 流量将直接传输，而不是通过 VNet 4 的 NVA。 如果希望流量通过 NVA，请断开 VNet 1、2和3的连接，并使用 VNet 对等互连将其连接到 VNet4。

   * **到达**  

     * 添加一个聚合路由 "10.2.0.0/16"，其中包含下一跃点，作为从 Vnet 1、2和3到分支的流量的 VNet 4 连接。 在 VNet4 连接中，为 "10.2.0.0/16" 配置路由，并指示下一跃点是 VNet 4 中 NVA 的特定 IP。

     * 添加下一个跃点作为 VNet 4 连接的路由 "0.0.0.0/0"。 添加了 "0.0.0.0/0" 以表示将流量发送到 internet。 它并不表示与 Vnet 或分支相关的特定地址前缀。 在 VNet4 连接中，为 "0.0.0.0/0" 配置路由，并指示下一跃点是 VNet 4 中 NVA 的特定 IP。

   * **关联：**  选择所有 **vnet 1、2和 3**。 这意味着 VNet 连接1、2和3将与此路由表相关联，并且可以通过传播) 此路由表中的传播来了解 (静态和动态的路由。

   * **传播：**  连接将路由传播到路由表。 选择 Vnet 1、2和3会启用将路由从 Vnet 1、2和3传播到此路由表。 请确保未选择 " (VPN/ER/P2S) 的分支选项"。 这可以确保本地连接无法直接访问 Vnet 1、2和3。

1. 编辑默认路由表 **DefaultRouteTable**。

   所有 VPN、Azure ExpressRoute 和用户 VPN 连接都与默认路由表相关联。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。

   * **到达**  

     * 为 **vnet 1、2和 3** 添加聚合路由 "10.1.0.0/16"，并将下一跃点作为 **VNet 4 连接**。

     * 添加下一个跃点作为 **VNet 4 连接** 的路由 "0.0.0.0/0"。 添加了 "0.0.0.0/0" 以表示将流量发送到 internet。 它并不表示与 Vnet 或分支相关的特定地址前缀。 在 VNet4 连接的上一步中，你已配置了 "0.0.0.0/0" 路由，下一跃点是 VNet 4 中 NVA 的特定 IP。

   * **关联：**  请确保已选择 "用于分支 **(VPN/ER/P2S)** " 选项。 这可以确保本地分支连接与默认路由表相关联。 所有 VPN、Azure ExpressRoute 和用户 VPN 连接仅与默认路由表相关联。

   * **传播自：**  请确保已选择 "用于分支 **(VPN/ER/P2S)** " 选项。 这可以确保本地连接将路由传播到默认路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到 "同一组路由表"。

   >[!Note]
   >
   > * 门户用户必须启用 "传播到默认路由" (VPN/ER/P2S/VNet) ，才能使 0.0.0.0/0 路由生效。
   > * PS/CLI/REST 用户必须将 "enableinternetsecurity" 标志设置为 true，以使 0.0.0.0/0 路由生效。
   >

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
