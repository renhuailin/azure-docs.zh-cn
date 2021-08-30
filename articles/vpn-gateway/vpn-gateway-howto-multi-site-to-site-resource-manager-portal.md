---
title: 向 VNet 添加多个 VPN 网关站点到站点连接：Azure 门户
description: 了解如何将其他站点到站点连接添加到 VPN 网关。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 304d96e9f7566ab80ac71d176fc52ccf3c852b49
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729528"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>将其他 S2S 连接添加到 VNet：Azure 门户

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell（经典）](vpn-gateway-multi-site.md)
>

本文可帮助你将其他站点到站点 (S2S) 连接添加到现已有连接的 VPN 网关。 此体系结构通常称为“多站点”配置。 可将 S2S 连接添加到已有 S2S 连接、点到站点连接或 VNet 到 VNet 连接的 VNet。 添加连接时，请注意一些限制。 在开始配置之前，请查看本文的[先决条件](#before)部分以进行验证。

本文适用于具有基于路由的 VPN 网关的资源管理器 VNet。 本文中的步骤不适用于新的 ExpressRoute/站点到站点共存连接配置。 但是，如果只是将新的 VPN 连接添加到已有的共存配置，则可以使用这些步骤。 有关共存连接的信息，请参阅 [ExpressRoute/S2S 共存连接](../expressroute/expressroute-howto-coexist-resource-manager.md)。

## <a name="prerequisites"></a><a name="before"></a>先决条件

确认以下各项：

* 你没有配置新的 ExpressRoute 和 VPN 网关共存配置。
* 你有一个使用[资源管理器部署模型](../azure-resource-manager/management/deployment-models.md)创建的虚拟网络，其中包含现有连接。
* VNet 的虚拟网络网关是 RouteBased 类型。 如果使用 PolicyBased VPN 网关，必须先删除虚拟网络网关，然后创建新的 RouteBased VPN 网关。
* 此 VNet 连接到的任何 VNet 都不存在地址范围重叠的情况。
* 有一台兼容的 VPN 设备，并且可对其进行配置。 请参阅 [关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。 如果不熟悉 VPN 设备的配置，或者不熟悉本地网络配置中的 IP 地址范围，则需咨询能够提供此类详细信息的人员。
* VPN 设备有一个面向外部的公共 IP 地址。

## <a name="configure-a-connection"></a><a name="configure"></a>配置连接

1. 从浏览器导航到 [Azure 门户](https://portal.azure.com)，并在必要时用 Azure 帐户登录。
1. 选择“所有资源”，从资源列表中找到“虚拟网络网关”并选择它。
1. 在“虚拟网络网关”页上，选择“连接” 。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN 网关连接":::
1. 在“连接”页上，选择“+添加” 。
1. 此时会打开“添加连接”页。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="“添加连接”页":::
1. 在“添加连接”页面上，填写以下字段：

   * **名称：** 想与其建立连接的站点的名称。
   * **连接类型：** 选择“站点到站点(IPsec)”。

## <a name="add-a-local-network-gateway"></a><a name="local"></a>添加本地网关

1. 对于“本地网关”字段，请选择“选择本地网关”***。此时会打开“选择本地网关”页***。
1. 选择“+ 新建”，打开“创建本地网关”页 。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="“创建本地网络网关”页":::
1. 在“创建本地网络网关”页面上，填写以下字段：

   * **名称：** 要分配给本地网络网关资源的名称。
   * **终结点：** 要连接到的站点上的 VPN 设备的公共 IP 地址，或终结点的 FQDN。
   * **地址空间：** 要路由到新本地网络站点的地址空间。
1. 在“创建本地网关”页上选择“确定”，保存所做的更改 。

## <a name="add-the-shared-key"></a><a name="part3"></a>添加共享密钥

1. 创建本地网关后，请返回到“添加连接”页。
1. 填写其余字段。 对于“共享密钥(PSK)”，可以从 VPN 设备获取共享密钥，也可以在此处创建一个共享密钥，并将 VPN 设备配置为使用这个共享密钥。 重要的一点是，这两个密钥必须完全相同。

## <a name="create-the-connection"></a><a name="create"></a>创建连接

1. 在页面底部，选择“确定”以创建连接。 此时将立即开始创建该连接。
1. 连接完成后，可以查看并验证它。

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>查看并验证 VPN 连接

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机学习路径](/learn/paths/deploy-a-website-with-azure-virtual-machines/)。
