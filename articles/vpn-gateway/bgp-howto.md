---
title: 为 VPN 网关配置 BGP：门户
titleSuffix: Azure VPN Gateway
description: 了解如何为 Azure VPN 网关配置 BGP。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/26/2021
ms.author: yushwang
ms.openlocfilehash: 5748b68940027269acaafe27148f0c4819cefc0a
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720327"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>如何在 Azure VPN 网关上配置 BGP

本文介绍了使用 Azure 门户在跨界站点到站点 (S2S) VPN 连接和 VNet 到 VNet 连接上启用 BGP 的步骤。

## <a name="about-bgp"></a><a name="about"></a>关于 BGP

BGP 是通常在 Internet 上使用的，用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 BGP 允许 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）交换“路由”，这些路由将通知这两个网关这些前缀的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点来允许在多个网络之间传输路由。

若要详细了解 BGP 优点，以及了解使用 BGP 的技术要求和注意事项，请参阅 [Azure VPN 网关的 BGP 概述](vpn-gateway-bgp-overview.md)。

## <a name="getting-started"></a>入门

本文帮助你在网络连接中启用 BGP，其中的每一部分都是一个基本构建基块。 如果完成所有三个部分，则你构建了如图示 1 所示的拓扑：

**图示 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="显示了网络体系结构和设置的图示" border="false":::

可以将这些部分组合在一起，生成更复杂的多跃点传输网络，以满足需求。

### <a name="prerequisites"></a>先决条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>第 1 部分：在虚拟网络网关上配置 BGP

在本部分中，你将创建并配置一个虚拟网络，创建并配置包含 BGP 参数的虚拟网络网关，并获取 Azure BGP 对等节点 IP 地址。 图示 2 显示了使用本部分中的步骤时要使用的配置设置。

**图示 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="显示了虚拟网络网关设置的图示" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1.创建并配置 TestVNet1

在此步骤中，你将创建并配置 TestVNet1。 使用[创建网关教程](./tutorial-create-gateway-portal.md)中的步骤创建并配置 Azure 虚拟网络和 VPN 网关。 使用下面屏幕截图中的参考设置。

* 虚拟网络：

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="带有相应地址前缀的 TestVNet1":::

* 子网：

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1 子网":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2.使用 BGP 参数为 TestVNet1 创建 VPN 网关

在此步骤中，你将创建采用相应 BGP 参数的 VPN 网关。

1. 在 Azure 门户中，从市场中导航到“虚拟网络网关”资源，然后选择“创建”。 

1. 填写参数，如下所示：

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="创建 VNG1":::

1. 在页面的突出显示的“配置 BGP”部分中，配置以下设置：

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="配置 BGP":::

   * 选择“配置 BGP” - “已启用”以显示 BGP 配置部分。

   * 填写你的 ASN（自治系统编号）。

   * “Azure APIPA BGP IP 地址”字段是可选的。 如果本地 VPN 设备使用 BGP 的 APIPA 地址，则必须从 VPN 的 Azure 预留 APIPA 地址范围中选择一个地址，该地址范围是从 **169.254.21.0** 到 **169.254.22.255**。 本示例使用 169.254.21.11。

   * 如果要创建主动-主动 VPN 网关，则 BGP 部分会显示一个额外的 IP 地址，即 **第二个自定义 Azure APIPA BGP IP 地址**。 从允许的 APIPA 范围（169.254.21.0 到 169.254.22.255）中，选择其他 IP 地址 。 第二个 IP 地址必须不同于第一个地址。

   > [!IMPORTANT]
   >
   > * 默认情况下，Azure 会将 GatewaySubnet 前缀范围内的一个专用 IP 地址自动分配为 Azure VPN 网关上的 Azure BGP IP 地址。 如果本地 VPN 设备使用 APIPA 地址（169.254.0.1 到169.254.255.254）作为 BGP IP，则需要一个自定义 Azure APIPA BGP 地址。 Azure VPN 网关会选择自定义 APIPA 地址的前提是，相应的本地网络网关资源（本地网络）将 APIPA 地址作为 BGP 对等节点 IP。 如果本地网络网关使用常规 IP 地址（而不是 APIPA），则 Azure VPN 网关会恢复使用 GatewaySubnet 范围内的专用 IP 地址。
   >
   > * APIPA BGP 地址在本地 VPN 设备和所有已连接的 Azure VPN 网关之间不得重叠。
   >
   > * 在 Azure VPN 网关上使用 APIPA 地址时，网关不会使用 APIPA 源 IP 地址启动 BGP 对等互连会话。 本地 VPN 设备必须启动 BGP 对等互连连接。
   >

1. 选择“查看 + 创建”  ，运行验证。 验证通过后，选择“创建”  以部署 VPN 网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。 可以在网关的“概述”页上查看部署状态。

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3.获取 Azure BGP 对等节点 IP 地址

创建网关后，你可以在 Azure VPN 网关上获取 BGP 对等节点 IP 地址。 需要使用这些地址来配置本地 VPN 设备，以便与 Azure VPN 网关建立 BGP 会话。

1. 导航到“虚拟网络网关”资源，然后选择“配置”页面，查看 BGP 配置信息，如以下屏幕截图所示。 在此页面上，你可以查看 Azure VPN 网关上的所有 BGP 配置信息：ASN、公共 IP 地址和 Azure 端对应的 BGP 对等节点 IP 地址（默认值和 APIPA）。

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP 网关":::

1. 在“配置” 页上，你可以进行以下配置更改：

   * 如果需要，你可以更新 ASN 或 APIPA BGP IP 地址。
   * 如果你有主动-主动 VPN 网关，则此页会显示第二个 Azure VPN 网关实例的公共 IP 地址、默认值和 APIPA BGP IP 地址。

1. 如果进行了任何更改，请选择“保存”以提交对 Azure VPN 网关的更改。

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>第 2 部分：在跨界 S2S 连接上配置 BGP

若要建立跨界连接，需要创建本地网络网关来表示本地 VPN 设备，并创建一个连接以将 VPN 网关与本地网关连接在一起，如[创建站点到站点连接](./tutorial-site-to-site-portal.md)所述。  本文包含指定 BGP 配置参数所需的其他属性。

**图示 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="显示了 IPsec 的图示" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1.在本地网络网关上配置 BGP

在此步骤中，你将在本地网络网关上配置 BGP。 使用以下屏幕截图作为示例。 屏幕截图显示了本地网络网关 (Site5)，它采用了图示 3 中指定的参数。

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="为本地网络网关配置 BGP":::

#### <a name="important-configuration-considerations"></a>重要的配置注意事项

* ASN 和 BGP 对等节点 IP 地址必须与本地 VPN 路由器配置相匹配。
* 仅当使用 BGP 连接到此网络时，才能将“地址空间”留空。 Azure VPN 网关会在内部将 BGP 对等节点 IP 地址的路由添加到相应的 IPsec 隧道。 如果 **未** 在 Azure VPN 网关与此特定网络之间使用 BGP，则 **必须** 为“地址空间”提供有效地址前缀的列表。
* 如果需要，还可以选择使用 **APIPA IP 地址** (169.254.x.x) 作为你的本地 BGP 对等节点 IP。 但是，如本文前面所述，你还需要为你的 Azure VPN 网关指定 APIPA IP 地址，否则无法为此连接建立 BGP 会话。
* 你可以在创建本地网络网关的过程中输入 BGP 配置信息，也可以从本地网络网关资源的“配置”页中添加或更改 BGP 配置。

**示例**

此示例使用 APIPA 地址 (169.254.100.1) 作为本地 BGP 对等节点 IP 地址：

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="本地网络网关 APIPA 和 BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2.配置启用了 BGP 的 S2S 连接

在此步骤中，你将创建启用了 BGP 的新连接。 如果你已有一个连接，并且想要在其上启用 BGP，可以[更新现有连接](#update)。

#### <a name="to-create-a-connection-with-bgp-enabled"></a>创建启用了 BGP 的连接

若要创建启用了 BGP 的新连接，请在“添加连接”页上填写值，然后选中“启用 BGP”选项以在此连接上启用 BGP。 选择“确定”以创建连接。

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="采用 BGP 的 IPsec 跨界连接":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>更新现有连接

如果要更改连接上的 BGP 选项，请导航到连接资源的“配置”页面，然后切换“BGP”选项，如以下示例中突出显示的那样。 选择“保存”  以保存所有更改。

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="为连接更新 BGP":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>第 3 部分：在 VNet 到 VNet 连接上配置 BGP

在 VNet 到 VNet 连接上启用或禁用 BGP 的步骤与[第 2 部分](#crosspremises)中的 S2S 步骤相同。 你可以在创建连接时启用 BGP，也可以更新现有 VNet 到 VNet 连接上的配置。

>[!NOTE] 
>不使用 BGP 的 VNet 到 VNet 连接会将通信范围限制为已连接的两个 VNet。 启用 BGP 能够将路由功能扩展到其他 S2S 连接或者这两个 VNet 的 VNet 到 VNet 连接。
>

有关上下文，请参考 **图示 4**。如果在 TestVNet2 和 TestVNet1 之间禁用了 BGP，则 TestVNet2 不会获知本地网络 Site5 的路由，因此无法与 Site5 进行通信。 启用 BGP 后，如图示 4 所示，所有三个网络都将能够通过 IPsec 和 VNet 到 VNet 连接进行通信。

**图示 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="显示了完整网络的图示" border="false":::

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/windows/quick-create-portal.md) 以获取相关步骤。