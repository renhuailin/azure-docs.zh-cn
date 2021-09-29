---
title: 使用 P2S VPN 和多个身份验证类型连接到 VNet：门户
titleSuffix: Azure VPN Gateway
description: 了解如何使用多种身份验证类型通过 P2S 连接到 VNet。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: 83267eb652e4adf0b8261c45cebfc118cec867c0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754534"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>使用多个身份验证类型配置与 VNet 的点到站点 VPN 连接：Azure 门户

本文介绍如何将运行 Windows、Linux 或 macOS 的单个客户端安全地连接到 Azure VNet。 若要从远程位置连接到 VNet，例如从家里或会议室进行远程通信，则可使用点到站点 VPN。 如果只有一些客户端需要连接到 VNet，也可使用 P2S VPN 来代替站点到站点 VPN。 点到站点连接不需要 VPN 设备或面向公众的 IP 地址。 P2S 基于 SSTP（安全套接字隧道协议）或 IKEv2 创建 VPN 连接。 有关点到站点 VPN 的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="从计算机连接到 Azure VNet - 点到站点连接示意图":::

有关点到站点 VPN 的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。 若要使用 Azure PowerShell 创建此配置，请参阅[使用 Azure PowerShell 配置点到站点 VPN](vpn-gateway-howto-point-to-site-rm-ps.md)。

## <a name="prerequisites"></a>先决条件

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

仅 OpenVPN 隧道类型支持同一 VPN 网关上的多个身份验证类型。

### <a name="example-values"></a><a name="example"></a>示例值

可使用以下值创建测试环境，或参考这些值以更好地理解本文中的示例：

* **VNet 名称：** VNet1
* **地址空间：** 10.1.0.0/16<br>对于此示例，我们只使用一个地址空间。 VNet 可以有多个地址空间。
* **子网名称：** FrontEnd
* **子网地址范围：** 10.1.0.0/24
* **订阅：** 如果有多个订阅，请确保使用正确的订阅。
* **资源组：** TestRG1
* **位置：** 美国东部
* **网关子网：** 10.1.255.0/27<br>
* SKU：VpnGw2
* 代系：第 2 代
* **网关类型：** VPN
* **VPN 类型：** 基于路由
* 公共 IP 地址名称：VNet1GWpip
* 连接类型：点到站点
* 客户端地址池：172.16.201.0/24<br>使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自客户端地址池的 IP 地址。

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>创建虚拟网络

开始之前，请确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>虚拟网络网关

在此步骤中，为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

>[!NOTE]
>基本网关 SKU 不支持 OpenVPN 隧道类型。
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

可以在网关的“概述”页上查看部署状态。 网关通常可能需要 45 分钟或更长时间才能完全创建和部署。 创建网关后，可以通过在门户中查看虚拟网络，来查看已分配给网关的 IP 地址。 网关显示为连接的设备。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>客户端地址池

客户端地址池是指定的专用 IP 地址的范围。 通过点到站点 VPN 进行连接的客户端动态接收此范围内的 IP 地址。 使用专用 IP 地址范围时，该范围不得与要通过其进行连接的本地位置重叠，也不得与要连接到其中的 VNet 重叠。 如果配置了多个协议，并且 SSTP 是其中一个协议，则配置的地址池将在配置的协议之间平均分配。

1. 创建虚拟网关后，请导航到虚拟网关页的“设置”部分。 在“设置”中，选择“点到站点配置” 。 选择“立即配置”，打开配置页。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="点到站点配置页的屏幕截图。" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. 在“点到站点配置”页上，可以配置各种设置。 在“地址池”框中，添加要使用的专用 IP 地址范围。 VPN 客户端动态接收指定范围内的 IP 地址。 主动/被动配置的最小子网掩码为 29 位，主动/主动配置的最小子网掩码为 28 位。

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="地址池的屏幕截图。":::

1. 转到下一部分以配置身份验证和隧道类型。

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>身份验证和隧道类型

在本部分中，你将配置身份验证类型和隧道类型。 如果在“点到站点配置”页上未显示“隧道类型”或“身份验证类型”，则表示网关使用的是基本 SKU  。 基本 SKU 不支持 IKEv2 或 RADIUS 身份验证。 若要使用这些设置，需要使用另一网关 SKU 删除并重新创建网关。

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="身份验证类型的屏幕截图。":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>隧道类型

在“点到站点配置”页上，选择“OpenVPN (SSL)”作为隧道类型。

### <a name="authentication-type"></a><a name="authenticationtype"></a>身份验证类型

对于“身份验证类型”，请选择所需的类型。 选项包括：

* Azure 证书
* RADIUS
* Azure Active Directory

根据所选的身份验证类型，将看到需要填写的不同配置设置字段。 填写所需信息，然后选择页面顶部的“保存”以保存所有配置设置。

有关身份验证类型的详细信息，请参阅：

* [Azure 证书](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN 客户端配置包

必须用客户端配置设置来配置 VPN 客户端。 VPN 客户端配置包包含的文件具有将 VPN 客户端配置为通过 P2S 连接连接到 VNet 的设置。

有关生成和安装 VPN 客户端配置文件的说明，请查看与配置相关的文章：

* [为本机 Azure 证书身份验证 P2S 配置创建并安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)。
* [Azure Active Directory 身份验证：配置用于 P2S OpenVPN 协议连接的 VPN 客户端](openvpn-azure-ad-client.md)。

## <a name="point-to-site-faq"></a><a name="faq"></a>点到站点常见问题解答

本部分包含有关点到站点配置的常见问题解答信息。 还可以查看 [VPN 网关常见问题解答](vpn-gateway-vpn-faq.md)，了解有关 VPN 网关的其他信息。

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](../index.yml)。 若要详细了解网络和虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-network/network-overview.md)。

有关 P2S 故障排除信息，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。