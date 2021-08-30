---
title: 使用自定义设置通过 NVA 路由流量
titleSuffix: Azure Virtual WAN
description: 了解虚拟 WAN 路由场景，以通过网络虚拟设备 (NVA) 来路由流量。 在此场景中，你将针对发往 Internet 的流量和自定义设置使用不同的 NVA，以通过 NVA 路由流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 84cb22956b682a9acb23f4f391faf046eccc47a7
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378135"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>方案：使用自定义设置通过 NVA 路由流量

使用 Azure 虚拟 WAN 虚拟中心路由时，有许多的选项可供使用。 本文重点介绍如何通过一个用于在虚拟网络与分支之间通信的网络虚拟设备 (NVA) 来路由流量，并针对发往 Internet 的流量使用不同的 NVA。 有关详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a>设计

* 支路，表示已连接到虚拟中心的虚拟网络。 （例如，本文内稍后的示意图中的 VNet 1、VNet 2 和 VNet 3。）
* 服务 VNet，表示用户已在其中部署了用于检查非 Internet 流量的 NVA 并且其中可能带有供支路访问的通用服务的虚拟网络。 （例如，本文内稍后的示意图中的 VNet 4。）
* 外围 VNet，表示用户已在其中部署了要用于检查发往 Internet 的流量的 NVA 的虚拟网络。 （例如，本文内稍后的示意图中的 VNet 5。）
* 中心，表示由 Microsoft 管理的虚拟 WAN 中心。

下表汇总了此方案支持的连接：

| 从          | 功能|分支|服务 VNet|分支|Internet|
|---|:---:|:---:|:---:|:---:|:---:|
| **分支**| ->| 直接提交 |直接提交 | 通过服务 VNet |通过外围 VNet |
| **服务 VNet**| ->| 直接提交 |不适用| 直接提交 | |
| **分支** | ->| 通过服务 VNet |直接提交| 直接提交 |  |

连接对照表中的每个单元格都说明了连接是直接通过虚拟 WAN 建立，还是通过带有 NVA 的某个虚拟网络建立。

请注意下列详细信息：

* 支路：
  * 支路直接通过虚拟 WAN 中心接入其他支路。
  * 支路通过指向服务 VNet 的静态路由连接到分支。 它们不会从这些分支获知特定前缀，因为这些前缀更具体，并替代了汇总值。
  * 支路会通过直接 VNet 对等互连将 Internet 流量发送到外围 VNet。
* 分支将通过指向服务 VNet 的静态路由接入支路。 它们不会从虚拟网络获知替代了已汇总的静态路由的特定前缀。
* 服务 VNet 类似于需要能够从每个虚拟网络和每个分支访问的共享服务 VNet。
* 外围 VNet 不需要通过虚拟 WAN 建立连接，因为它仅支持通过直接虚拟网络对等互连传送的流量。 不过，为了简化配置，请使用与外围 VNet 相同的连接模型。

有三种不同的连接模式，它们对应于三个路由表。 与不同虚拟网络的关联如下：

* 支路：
  * 关联的路由表：RT_V2B
  * 传播到路由表：RT_V2B 和 RT_SHARED 
* NVA VNet（服务 VNet 和外围 VNet）：
  * 关联的路由表：RT_SHARED
  * 传播到路由表：RT_SHARED
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：RT_SHARED 和 Default

> [!NOTE]
> 请确保支路 VNet 不会传播到 Default 标签。 这可以确保从分支到支路 VNet 的流量被转发到 NVA。

这些静态路由确保发往/发自虚拟网络和分支的流量流经“服务 VNet”(VNet 4) 中的 NVA：

| 描述 | 路由表 | 静态路由              |
| ----------- | ----------- | ------------------------- |
| 分支    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| NVA 支路  | 默认     | 10.1.0.0/16 -> vnet4conn  |

现在，可以使用虚拟 WAN 选择要将数据包发送到的正确连接。 此外，需要使用虚拟 WAN 来选择收到这些数据包时要执行的正确操作。 为此可以使用连接路由表，如下所示：

| 说明 | 连接 | 静态路由            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

有关详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a>体系结构

以下示意图显示了本文前面所述的体系结构。

示意图中有一个中心：中心 1。

* 中心 1 直接连接到 NVA VNet“VNet 4”和“VNet 5”  。

* VNet 1、2、3 与分支之间的流量预期会通过 VNet 4 NVA 10.4.0.5 传送。

* 来自 VNet 1、2 和 3 的所有发往 Internet 的流量预期会通过 VNet 5 NVA 10.5.0.5 传送。

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="网络体系结构示意图。":::

## <a name="workflow"></a><a name="workflow"></a>工作流

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="工作流示意图。" lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

若要设置通过 NVA 进行路由，请考虑执行以下步骤：

1. 要使发往 Internet 的流量通过 VNet 5 传送，VNet 1、2 和 3 需要通过虚拟网络对等互连直接连接到 VNet 5。 此外，还需要在虚拟网络中为 0.0.0.0/0 和下一个跃点10.5.0.5 设置用户定义的路由。

   如果你不想要将 VNet 1、2 和 3 连接到 VNet 5，而只想使用 VNet 4 中的 NVA 路由来自分支的 0.0.0.0/0 流量（本地 VPN 或 ExpressRoute 连接），请转到[备用工作流](#alternate)。

   但是，如果你要通过 NVA 传输 VNet 到 VNet 的流量，则需要从虚拟中心断开与 VNet 1、2、3 的连接，并将其连接到 NVA 支路 VNet4，或将其堆叠在 VNet4 之上。 在虚拟 WAN 中，VNet 到 VNet 的流量将通过虚拟 WAN 中心或虚拟 WAN 中心的 Azure 防火墙（安全中心）传输。 如果 VNet 直接使用 VNet 对等互连来建立对等互连，则它们可以直接通信，而不必通过虚拟中心进行传输。

1. 在 Azure 门户中转到你的虚拟中心，并创建名为 RT_Shared 的自定义路由表。 此表通过来自所有虚拟网络和分支连接的传播获知路由。 在后面的示意图中可以看到这个空表。

   * 路由：无需添加任何静态路由。

   * 关联：选择 VNet 4 和 5，即，这些虚拟网络的连接将关联到 RT_Shared 路由表 。

   * 传播：由于你希望所有分支和虚拟网络连接将其路由动态传播到此路由表，因此请选择分支和所有虚拟网络。

1. 创建名为 RT_V2B 的自定义路由表，用于将来自 VNet 1、2 和 3 的流量定向到分支。

   * 路由：为分支添加聚合静态路由条目，并将下一个跃点设置为 VNet 4 连接。 在 VNet 4 的连接中为分支前缀配置一个静态路由。 将下一个跃点指定为 VNet 4 中 NVA 的特定 IP。

   * 关联：将 VNet 1、2 和 3 全部选中 。 这意味着，VNet 连接 1、2 和 3 将关联到此路由表，并且可以获知此路由表中的路由（通过传播获知静态和动态路由）。

   * 传播：连接将路由传播到路由表。 选择 VNet 1、2 和 3 可将 VNet 1、2 和 3 中的路由传播到此路由表。 无需将分支连接中的路由传播到 RT_V2B，因为分支虚拟网络流量将通过 VNet 4 中的 NVA 传送。
  
1. 编辑默认路由表 DefaultRouteTable。

   所有 VPN、Azure ExpressRoute 和用户 VPN 连接将关联到默认路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。

   * 路由：为 VNet 1、2 和 3 添加聚合静态路由条目，并将下一个跃点设置为 VNet 4 连接。 在 VNet 4 的连接中为 VNet 1、2 和 3 聚合前缀配置一个静态路由。 将下一个跃点指定为 VNet 4 中 NVA 的特定 IP。

   * 关联：确保已选择用于分支的选项 (VPN/ER/P2S)，并确保本地分支连接已关联到默认路由表。

   * 传播来源：确保已选择用于分支的选项 (VPN/ER/P2S)，并确保本地连接将路由传播到默认路由表。

## <a name="alternate-workflow"></a><a name="alternate"></a>备用工作流

此工作流不会将 VNet 1、2 和 3 连接到 VNet 5， 而是使用 VNet 4 中的 NVA 路由来自分支的 0.0.0.0/0 流量（本地 VPN 或 ExpressRoute 连接）。

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="备用工作流示意图。" lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

若要设置通过 NVA 进行路由，请考虑执行以下步骤：

1. 在 Azure 门户中，转到你的虚拟中心，并创建名为 RT_NVA 的自定义路由表用于通过 NVA 10.4.0.5 定向流量 ****  

   * 路由： 无需执行任何操作。 

   * 关联： 选择 VNet4。  这意味着，VNet 连接 4 将关联到此路由表，并且可以获知此路由表中的路由（通过传播获知静态和动态路由）。

   * 传播：连接将路由传播到路由表。  选择 VNet 1、2 和 3 可将 VNet 1、2 和 3 中的路由传播到此路由表。 选择分支 (VPN/ER/P2S) 可将分支/本地连接中的路由传播到此路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。

1. 创建名为 RT_VNET 的自定义路由表，用于通过 VNet4 NVA 将来自 VNet 1、2 和 3 的流量定向到分支或 Internet (0.0.0.0/0) **** 。 VNet 到 VNet 的流量是直接传送的，而不通过 VNet 4 的 NVA 传送。 如果你希望通过 NVA 传送流量，请断开与 VNet 1、2 和 3 的连接，并使用 VNet 对等互连将它们连接到 VNet4。

   * 路由：  

     * 针对从 VNet 1、2 和 3 发往分支的流量，添加聚合路由“10.2.0.0/16”并将下一个跃点设置为 VNet 4 连接。 在 VNet4 连接中，为“10.2.0.0/16”配置一个路由，并将下一个跃点指定为 VNet 4 中 NVA 的特定 IP。

     * 添加路由“0.0.0.0/0”并将下一个跃点设置为 VNet 4 连接。 添加“0.0.0.0/0”表示要将流量发送到 Internet。 它并不表示与 VNet 或分支相关的特定地址前缀。 在 VNet4 连接中，为“0.0.0.0/0”配置一个路由，并将下一个跃点指定为 VNet 4 中 NVA 的特定 IP。

   * 关联：将 VNet 1、2 和 3 全部选中。  这意味着，VNet 连接 1、2 和 3 将关联到此路由表，并且可以获知此路由表中的路由（通过传播获知静态和动态路由）。

   * 传播：连接将路由传播到路由表。  选择 VNet 1、2 和 3 可将 VNet 1、2 和 3 中的路由传播到此路由表。 确保未选择用于分支的选项 (VPN/ER/P2S)。 这可以确保本地连接无法直接接入 VNet 1、2 和 3。

1. 编辑默认路由表 DefaultRouteTable **** 。

   所有 VPN、Azure ExpressRoute 和用户 VPN 连接将关联到默认路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。

   * 路由：  

     * 为 VNet 1、2 和 3 添加聚合路由“10.1.0.0/16”，并将下一个跃点设置为 VNet 4 连接 。

     * 添加路由“0.0.0.0/0”并将下一个跃点设置为 VNet 4 连接。 添加“0.0.0.0/0”表示要将流量发送到 Internet。 它并不表示与 VNet 或分支相关的特定地址前缀。 在前面的步骤中配置 VNet4 连接时，你已为“0.0.0.0/0”配置了一个路由，并已将下一个跃点设置为 VNet 4 中 NVA 的特定 IP。

   * 关联：确保已选择用于分支的选项 (VPN/ER/P2S)。  这可以确保本地分支连接关联到默认路由表。 所有 VPN、Azure ExpressRoute 和用户 VPN 连接只会关联到默认路由表。

   * 传播来源：确保已选择用于分支的选项 (VPN/ER/P2S)。  这可以确保本地连接将路由传播到默认路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到“同一组路由表”。

   >[!Note]
   >
   > * 门户用户必须对连接 (VPN/ER/P2S/VNet) 启用“传播到默认路由”才能使 0.0.0.0/0 路由生效。
   > * PS/CLI/REST 用户必须将“enableinternetsecurity”标志设置为 true 才能使 0.0.0.0/0 路由生效。
   > * 如果某个包含下一个跃点 IP 的路由指定为公共 IP 地址或 0.0.0.0/0 (Internet)，则虚拟网络连接不支持“多重/唯一”下一个跃点 IP 指向支路 VNet 中的同一个网络虚拟设备
   > * 如果将 0.0.0.0/0 配置为虚拟网络连接上的静态路由，该路由将应用于所有流量，包括辐射网络内的资源。 这意味着所有流量都将转发到静态路由的下一个跃点 IP 地址（NVA 专用 IP）。 因此，在具有 0.0.0.0/0 路由的部署中，在辐射虚拟网络连接上配置了下一个跃点 NVA IP 地址，若要直接访问与 NVA 相同的虚拟网络中的工作负载（即流量不经过 NVA），请在辐射虚拟网络连接上指定 /32 路由。 例如，如果要直接访问 10.1.3.1，请在辐射虚拟网络连接上指定 10.1.3.1/32 下一个跃点 10.1.3.1。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
