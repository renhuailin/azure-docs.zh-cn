---
title: 在 P2S 客户端的辐射 Vnet 中管理对资源的安全访问
titleSuffix: Azure Virtual WAN
description: 本文可帮助你使用 Azure 虚拟 WAN 和 Azure 防火墙规则来管理 (点到站点) 客户端的用户 VPN 的虚拟网络的安全访问。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 12/10/2020
ms.author: cherylmc
ms.openlocfilehash: d4257e5ade1e32ec971bb77f9d5a686d30195735
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111207"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>为用户 VPN 客户端管理对轮辐 Vnet 中资源的安全访问

本文介绍如何使用虚拟 WAN 和 Azure 防火墙规则和筛选器，通过点到站点 IKEv2 或打开 VPN 连接管理对 Azure 中资源的连接的安全访问。 如果你的远程用户要限制对 Azure 资源的访问权限，或者在 Azure 中保护资源，则此配置非常有用。

本文中的步骤可帮助你在下图中创建体系结构，以允许用户 VPN 客户端访问连接到虚拟中心的分支 VNet 中 (VM1) 的特定资源，但不 (VM2) 的其他资源。 使用此体系结构示例作为基本指导原则。

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="关系图：受保护的虚拟中心" :::

## <a name="prerequisites"></a>必备条件

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 你具有可用于要使用的身份验证配置的值。 例如，RADIUS 服务器、Azure Active Directory 身份验证，或者 [生成和导出证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

## <a name="create-a-virtual-wan"></a>创建虚拟 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>定义 P2S 配置参数

点到站点 (P2S) 配置定义用于连接远程客户端的参数。 本节帮助你定义 P2S 配置参数，并创建将用于 VPN 客户端配置文件的配置。 你遵循的说明取决于你要使用的身份验证方法。

### <a name="authentication-methods"></a>身份验证方法

选择身份验证方法时，有三种选择。 每种方法都有特定的要求。 选择以下方法之一，然后完成这些步骤。

* **Azure Active Directory 身份验证：** 获取在 Azure AD 租户、 [颁发者](https://sts.windows.net/your-Directory-ID/)和 [Azure AD 租户](https://login.microsoftonline.com/your-Directory-ID)中注册的 Azure VPN 企业应用程序的应用程序 ID。

* **基于 Radius 的身份验证：** 获取 Radius 服务器 IP、Radius 服务器机密和证书信息。

* **Azure 证书：** 对于此配置，证书是必需的。 需要生成或获取证书。 每个客户端都需要客户端证书。 此外，需要上传 (公钥) 的根证书信息。 有关所需证书的详细信息，请参阅 [生成和导出证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

下面的示例演示了 Azure 证书身份验证。

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>创建集线器和网关

在本部分中，将使用点到站点网关创建虚拟中心。 配置时，可以使用以下示例值：

* **中心专用 IP 地址空间：** 10.0.0.0/24
* **客户端地址池：** 10.5.0.0/16
* **自定义 DNS 服务器：** 最多可以列出5个 DNS 服务器

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>生成 VPN 客户端配置文件

在本部分中，你将生成并下载配置文件。 这些文件用于配置客户端计算机上的本机 VPN 客户端。 有关客户端配置文件的内容的信息，请参阅 [点到站点配置-证书](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>配置 VPN 客户端

使用下载的配置文件配置远程访问客户端。 每个操作系统的过程均不同，请按照适用于你的系统的说明进行操作。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>连接轮辐 VNet

在本部分中，会将辐射虚拟网络附加到虚拟 WAN 集线器。

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>创建虚拟机

在本部分中，将在 VNet、VM1 和 VM2 中创建两个 Vm。 在网络图中，使用10.18.0.4 和10.18.0.5。 配置 Vm 时，请确保选择在 "网络" 选项卡上 (找到的虚拟网络) "。 有关创建 VM 的步骤，请参阅 [快速入门：创建 vm](../virtual-machines/windows/quick-create-portal.md)。

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>保护虚拟中心

标准虚拟中心没有内置安全策略来保护辐射虚拟网络中的资源。 受保护的虚拟中心使用 Azure 防火墙或第三方提供程序来管理传入和传出流量，以保护 Azure 中的资源。

使用以下文章将集线器转换为受保护的中心： [在虚拟 WAN 中心配置 Azure 防火墙](howto-firewall.md)。

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> 创建规则以管理和筛选流量

创建用于规定 Azure 防火墙行为的规则。 通过保护集线器，我们可以确保进入虚拟中心的所有数据包在访问 Azure 资源之前都要经过防火墙处理。

完成这些步骤后，你将创建一个体系结构，该体系结构允许 VPN 用户使用专用 IP 地址10.18.0.4 访问 VM，但 **不能** 访问具有专用 ip 地址的 vm 10.18.0。5

1. 在 Azure 门户中，导航到 " **防火墙管理器**"。
1. 在 "安全性" 下，选择 " **Azure 防火墙策略**"。
1. 选择“创建 Azure 防火墙策略”。
1. 在 " **策略详细信息**" 下，键入名称，并选择要在其中部署虚拟中心的区域。
1. 在完成时选择“下一步:DNS 设置(预览)”。
1. 在完成时选择“下一步:规则”。
1. 在“规则”选项卡上，选择“添加规则集合”。
1. 提供集合的名称。 将 "类型" 设置为 " **网络**"。 添加优先级值 **100**。
1. 填写规则、源类型、源、协议、目标端口和目标类型的名称，如以下示例中所示。 然后选择 " **添加**"。 此规则允许来自 VPN 客户端池的任何 IP 地址使用专用 IP 地址10.18.04 访问 VM，而不允许使用连接到虚拟中心的任何其他资源。 根据所需的体系结构和权限规则创建所需的任何规则。

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="防火墙规则" :::

1. 在完成时选择“下一步:**威胁智能”** 。
1. 在完成时选择“下一步:中心”。
1. 在“中心”选项卡上，选择“关联虚拟中心”。
1. 选择前面创建的虚拟中心，然后选择 " **添加**"。
1. 选择“查看 + 创建”。
1. 选择“创建”  。

完成此过程可能需要5分钟或更长时间。

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>通过 Azure 防火墙路由流量

在本部分中，需要确保通过 Azure 防火墙路由流量。

1. 在门户中的 " **防火墙管理器**" 中，选择 " **受保护的虚拟中心**"。
1. 选择创建的虚拟中心。
1. 在“设置”下，选择“安全配置” 。
1. 在“专用流量”下，选择“通过 Azure 防火墙发送” 。
1. 验证 Azure 防火墙是否保护 VNet 连接和分支连接专用流量。
1. 选择“保存”。

## <a name="validate"></a><a name="validate"></a>验证

验证受保护的中心的设置。

1. 通过 VPN 从客户端设备连接到 **受保护的虚拟中心** 。
1. 从客户端 Ping IP 地址 **10.18.0.4** 。 应该会看到响应。
1. 从客户端 Ping IP 地址 **10.18.0.5** 。 你应无法看到响应。

### <a name="considerations"></a>注意事项

* 请确保受保护的虚拟中心上的 **有效路由表** 具有防火墙的专用流量的下一跃点。 若要访问有效的路由表，请导航到 **虚拟中心** 资源。 在 " **连接**" 下，选择 " **路由**"，然后选择 " **有效路由**"。 在该列表中，选择 **默认** 的路由表。
* 验证是否在 " [创建规则](#create-rules) " 部分中创建了规则。 如果这些步骤丢失，你创建的规则将不会实际关联到中心，并且路由表和数据包流将不会使用 Azure 防火墙。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。
* 有关 Azure 防火墙的详细信息，请参阅 [Azure 防火墙常见问题解答](../firewall/firewall-faq.md)。
