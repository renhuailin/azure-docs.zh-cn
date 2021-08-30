---
title: 配置主动-主动 VPN 网关：Azure 门户
titleSuffix: Azure VPN Gateway
description: 了解如何使用 Azure 门户配置主动-主动虚拟网络网关。
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746178"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>使用门户配置主动-主动 VPN 网关

本文将帮助你使用[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)和 Azure 门户创建高度可用的主动-主动 VPN 网关。 你也可使用 [PowerShell](vpn-gateway-activeactive-rm-powershell.md) 配置主动-主动网关。

若要实现跨界连接和 VNet 到 VNet 连接的高可用性，应该部署多个 VPN 网关，在网络与 Azure 之间建立多个并行连接。 有关连接选项和拓扑的概述，请参阅[高可用性跨界连接与 VNet 到 VNet 连接](vpn-gateway-highlyavailable.md)。

> [!IMPORTANT]
> 主动-主动模式适用于除基本或标准 SKU 之外的所有 SKU。 有关详细信息，请参阅[配置设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。
>

本文中的步骤可帮助你在主动-主动模式下配置 VPN 网关。 主动-主动和主动-待机模式之间有些许不同。 其他属性与非主动-主动网关相同。 

* 主动-主动网关有两个网关 IP 配置和两个公共 IP 地址。
* 主动-主动网关已启用主动-主动设置。
* 虚拟网络网关 SKU 不能为基本或标准 SKU。

如果已有 VPN 网关，可将现有的 VPN 网关从主动-待机模式[更新](#update)为主动-主动模式，或反之。

## <a name="create-a-vnet"></a><a name="vnet"></a>创建 VNet

如果还没有要使用的 VNet，请使用以下值创建 VNet：

* **资源组：** TestRG1
* **名称：** VNet1
* **区域：** （美国）美国东部
* **IPv4 地址空间：** 10.1.0.0/16
* **子网名称：** FrontEnd
* **子网地址空间：** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>创建主动-主动 VPN 网关

在此步骤中，为 VNet 创建主动-主动虚拟网络网关（VPN 网关）。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

使用以下值创建虚拟网络网关：

* **名称：** VNet1GW
* **区域：** 美国东部
* **网关类型：** VPN
* **VPN 类型：** 基于路由
* SKU：VpnGw2
* **代系：** 第 2 代
* **虚拟网络：** VNet1
* **网关子网地址范围：** 10.1.255.0/27
* **公共 IP 地址**：新建
* 公共 IP 地址名称：VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

可以在网关的“概述”页上查看部署状态。 创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a>更新现有 VPN 网关

此部分可帮助你将现有 Azure VPN 网关从主动-待机模式更改为主动-主动模式，或反之。 将主动-待机网关更改为主动-主动模式时，将另创建一个公共 IP 地址，然后会另添加一个网关 IP 配置。 

### <a name="change-active-standby-to-active-active"></a>从主动-待机更改为主动-主动

使用以下步骤将主动-待机模式网关转换为主动-主动模式。 如果网关是使用[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)创建的，则还可以升级该页面上的 SKU。

1. 导航到虚拟网络网关页面。

1. 在左侧菜单中，选择“配置”。

1. 在“配置”页面上，配置下列设置： 

   * 将主动-主动模式更改为“启用”。
   * 单击“创建另一个网关 IP 配置”。

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="屏幕截图显示了“配置”页面。":::

1. 在“选择公共 IP 地址”页面中，指定一个符合条件的现有公共 IP 地址，或者选择“+ 新建”来创建新的公共 IP 地址，以用于第二个 VPN 网关实例 。

1. 在“创建公共 IP 地址”页面，选择“基本”SKU，然后单击“确定”  。

1. 在“配置”页面顶部，单击“保存” 。 大约需要 30-45 分钟才能完成更新。

### <a name="change-active-active-to-active-standby"></a>从主动-主动更改为主动-待机

使用以下步骤将主动-主动模式网关转换为主动-待机模式。

1. 导航到虚拟网络网关页面。

1. 在左侧菜单中，选择“配置”。

1. 在“配置”页面，将主动-主动模式更改为“禁用” 。

1. 在“配置”页面顶部，单击“保存” 。

## <a name="next-steps"></a>后续步骤

若要配置连接，请参阅以下文章：

* [站点到站点 VPN 连接](./tutorial-site-to-site-portal.md)
* [VNet 到 VNet 连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)