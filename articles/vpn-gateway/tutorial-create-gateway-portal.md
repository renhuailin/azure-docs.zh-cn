---
title: 教程 - 创建和管理 VPN 网关：Azure 门户
description: 在本教程中，学习如何使用门户创建、部署和管理 Azure VPN 网关。
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 07/21/2021
ms.openlocfilehash: 6ac09f7d523cfbc661d2b0303012c8291cc6897a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729645"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>教程：使用 Azure 门户创建和管理 VPN 网关

Azure VPN 网关在客户本地与 Azure 之间提供跨界连接。 本教程介绍了基本的 Azure VPN 网关部署项目，例如创建和管理 VPN 网关。 也可以使用 [Azure CLI](create-routebased-vpn-gateway-cli.md) 或 [Azure PowerShell](create-routebased-vpn-gateway-powershell.md) 创建网关。 若想了解有关本教程中使用的配置设置的详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建 VPN 网关
> * 查看网关公共 IP 地址
> * 重设 VPN 网关大小（重设 SKU 大小）
> * 重置 VPN 网关

下图展示了本教程中创建的虚拟网络和 VPN 网关。

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="VNet 和 VPN 网关图表。":::

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 如果没有，请[免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>创建虚拟网络

使用以下值创建 VNet：

* **资源组：** TestRG1
* **名称：** VNet1
* **区域：** （美国）美国东部
* **IPv4 地址空间：** 10.1.0.0/16
* **子网名称：** FrontEnd
* **子网地址空间：** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>创建 VPN 网关

在此步骤中，为 VNet 创建虚拟网络网关（VPN 网关）。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

使用以下值创建虚拟网络网关：

* **名称：** VNet1GW
* **区域：** 美国东部
* **网关类型：** VPN
* **VPN 类型：** 基于路由
* SKU：VpnGw2
* 代系：第 2 代
* **虚拟网络：** VNet1
* **网关子网地址范围：** 10.1.255.0/27
* **公共 IP 地址**：新建
* 公共 IP 地址名称：VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

网关可能需要 45 分钟或更长时间才能完全创建和部署。 可以在网关的“概述”页上查看部署状态。 创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>查看公共 IP 地址

可以在网关的“概述”页面查看网关的公共 IP 地址。

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="概述页面屏幕截图。":::

若要查看有关公共 IP 地址对象的其他信息，请单击“公共 IP 地址”旁边的名称/IP 地址链接。

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>重设网关 SKU 大小

下面是关于重设网关 SKU 大小并对其进行更改的特定规则。 在本部分中，我们将重设 SKU 大小。 有关详细信息，请参阅[网关设置 - 重设 SKU 大小并对其进行更改](vpn-gateway-about-vpn-gateway-settings.md#resizechange)。

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>重置网关

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序或转到下一个教程，请按照以下步骤删除相关的资源：

1. 在门户顶部的“搜索”框中输入资源组的名称，并从搜索结果中选择资源组。

1. 选择“删除资源组”  。

1. 在“键入资源组名称”中输入资源组名称，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

有了 VPN 网关后，就可以配置连接。 下面的文章将有助于你创建一些最常见的配置：

> [!div class="nextstepaction"]
> [站点到站点 VPN 连接](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [点到站点 VPN 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
