---
title: Azure 虚拟 WAN：在中心中创建网络虚拟设备 (NVA)
description: 了解如何在虚拟 WAN 中心中部署网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 0252305167f0f77d310810af0a147106fae9e0c6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730884"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>如何在 Azure 虚拟 WAN 中心创建网络虚拟设备

本文介绍如何使用虚拟 WAN 通过 Azure 中的网络虚拟设备 (NVA) 连接到 Azure 中的资源。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关虚拟 WAN 的详细信息，请参阅[什么是虚拟 WAN？](virtual-wan-about.md)。

本文中的步骤可帮助你在虚拟 WAN 中心中创建 Barracuda CloudGen WAN 网络虚拟设备。 要完成此练习，在开始之前，必须具有 Barracuda 云本地设备 (CPE) 和要部署到中心中的 Barracuda CloudGen WAN 设备的许可证。

有关 Azure 虚拟 WAN 中“Cisco SD-WAN”的部署文档，请参阅[适用于多云的 Cisco Cloud OnRamp](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)。 

有关 Azure 虚拟 WAN 中“VMware SD-WAN”的部署文档，请参阅[虚拟 WAN 中心中 VMware SD-WAN 的部署指南](https://kb.vmware.com/s/article/82746)

## <a name="prerequisites"></a>先决条件

在开始配置之前，请验证你是否符合以下条件：

* 获取 Barracuda CloudGen WAN 网关的许可证。 若要详细了解如何执行此操作，请参阅 [Barracuda CloudGen WAN 文档](https://www.barracuda.com/products/cloudgenwan)

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与连接到本地站点的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>创建虚拟 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>创建中心

中心是一种虚拟网络，可包含适用于站点到站点、ExpressRoute、点到站点或网络虚拟设备功能的网关。 创建中心后，即使你没有附加任何站点，也会对该中心收取费用。 如果选择创建站点到站点 VPN 网关，则需要 30 分钟，才能在虚拟中心中创建站点到站点 VPN 网关。 与站点到站点、ExpressRoute 或点到站点不同，必须首先创建中心，然后才能将网络虚拟设备部署到中心 VNet 中。

1. 找到创建的虚拟 WAN。 在“虚拟 WAN”页上的“连接性”部分下，选择“中心”  。
1. 在“中心”页上，选择“+新建中心”以打开“创建虚拟中心”页 。

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="基础知识":::
1. 在“创建虚拟中心”页上的“基本”选项卡上，请填写以下字段 ：

   **项目详细信息**

   * 区域（之前称为位置）
   * 名称
   * 中心专用地址空间。 用于创建中心的最小地址空间是 /24，这表示在创建过程中从 /25 到 /32 的任何范围都将产生错误。 Azure 虚拟 WAN 是一项 Microsoft 托管服务，它在虚拟中心为不同的网关/服务创建适当子网。 （例如：网络虚拟设备、VPN 网关、ExpressRoute 网关、用户 VPN/点到站点网关、防火墙、路由等）。 用户无需在虚拟中心内为各项服务显式规划子网地址空间，因为这是 Microsoft 所提供服务的一部分。
1. 选择“查看 + 创建”以进行验证。
1. 选择“创建”以创建中心。

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>在中心中创建网络虚拟设备

在此步骤中，将在中心中创建网络虚拟设备。 对于每个 NVA 合作伙伴的产品，每个 NVA 的过程各不相同。 在此示例中，我们将创建 Barracuda CloudGen WAN 网关。

1. 找到在上一步中创建的虚拟 WAN 中心，然后将其打开。

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="虚拟中心":::
1. 找到“网络虚拟设备”磁贴，选择“创建”链接。
1. 在“网络虚拟设备”边栏选项卡中，选择“Barracuda CloudGen WAN”，然后选择“创建”按钮  。

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="选择 NVA":::
1. 这会转到适用于 Barracuda CloudGen WAN 网关的 Azure 市场产品/服务。 阅读条款，然后在准备就绪时选择“创建”按钮。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA 基本信息":::
1. 在“基本信息”页上，你需要提供以下信息：

   * **订阅** - 选择用于部署虚拟 WAN 和中心的订阅。
   * **资源组** - 选择用于部署虚拟 WAN 和中心的同一资源组。
   * **区域** - 选择虚拟中心资源所在的同一区域。
   * **应用程序名称** - Barracuda NextGen WAN 是托管应用程序。 选择一个易于标识此资源的名称，因为将在订阅中显示该名称。
   * **托管资源组** - 这是托管资源组的名称，Barracuda 将在其中部署由其托管的资源。 为此，应预先填充该名称。
1. 选择“下一步: CloudGen WAN 网关”按钮。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN 网关":::
1. 在此处提供以下信息：

   * **虚拟 WAN 中心** - 要将此 NVA 部署到其中的虚拟 WAN 中心。
   * **NVA 基础结构单元** - 指示要用来部署此 NVA 的 NVA 基础结构单元的数量。 选择要在所有分支站点（将通过此 NVA 连接到此中心）中提供的聚合带宽容量的数量。
   * **令牌** - Barracuda 要求你在此处提供身份验证令牌，以便将你自己标识为该产品的已注册用户。 需要从 Barracuda 获取此令牌。
1. 选择“查看和创建”按钮以继续。
1. 在此页上，系统将要求你接受共同管理员访问协议的条款。 这是托管应用程序的标准配置，其中发布服务器可以访问此部署中的某些资源。 勾选“我同意上述条款和条件”框，然后选择“创建” 。

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>将 VNet 连接到中心

在本部分中，将创建中心和 VNet 之间的连接。

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>后续步骤

* 若要详细了解虚拟 WAN，请参阅[什么是虚拟 WAN？](virtual-wan-about.md)页。
* 若要详细了解虚拟 WAN 中心中的 NVA，请参阅[关于虚拟 WAN 中心中的网络虚拟设备](about-nva-hub.md)。
