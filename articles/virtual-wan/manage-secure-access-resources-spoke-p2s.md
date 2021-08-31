---
title: 在 P2S 客户端的分支 VNet 中管理对资源的安全访问
titleSuffix: Azure Virtual WAN
description: 本文将向你介绍如何使用 Azure 虚拟 WAN 和 Azure 防火墙规则来管理用户 VPN（点到站点）客户端对虚拟网络的安全访问。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: e46f9c5e3d59c4f30d0a9a0a9dddcf98cd636e40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734686"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>在用户 VPN 客户端的分支 VNet 中管理对资源的安全访问

本文将向你介绍如何使用虚拟 WAN 和 Azure 防火墙规则和筛选器来管理对通过点到站点 IKEv2 或 Open VPN 连接到 Azure 中资源的连接的安全访问。 如果某些远程用户要限制对 Azure 资源的访问或保护 Azure 中资源的安全，此配置非常有用。

本文中的步骤可帮助你在下图中创建体系结构，以允许用户 VPN 客户端访问连接到虚拟中心的分支 VNet 中的特定资源 (VM1)，但不能访问其他资源 (VM2)。 使用此体系结构示例作为基本准则。

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="示意图：安全虚拟中心" :::

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* 你具有要使用的身份验证配置的可用值。 例如 RADIUS 服务器、Azure Active Directory 身份验证或[生成和导出证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

## <a name="create-a-virtual-wan"></a>创建虚拟 WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>定义 P2S 配置参数

点到站点 (P2S) 配置定义连接远程客户端的参数。 本部分介绍如何定义 P2S 配置参数，然后创建将用于 VPN 客户端 配置文件的配置。 遵循的说明取决于要使用的身份验证方法。

### <a name="authentication-methods"></a>身份验证方法

选择身份验证方法时，有三种选择。 每种方法都有特定的要求。 选择以下某种方法，然后完成步骤。

* **Azure Active Directory 身份验证：** 获取以下内容：

   * 在 Azure AD 租户中注册的 Azure VPN 企业应用程序的应用程序 ID。
   * 颁发者。 示例：`https://sts.windows.net/your-Directory-ID`。
   * Azure AD 租户。 示例：`https://login.microsoftonline.com/your-Directory-ID`。

* **基于 RADIUS 的身份验证：** 获取 RADIUS 服务器 IP、RADIUS 服务器机密和证书信息。

* **Azure 证书：** 此配置需要证书。 你需要生成或获取证书。 每个客户端都需要客户端证书。 此外，需要上传根证书信息（公钥）。 有关所需证书的详细信息，请参阅[生成和导出证书](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)。

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>创建中心和网关

在本部分中，创建具有点到站点网关的虚拟中心。 配置时，可以使用以下示例值：

* **中心专用 IP 地址空间：** 10.0.0.0/24
* **客户端地址池：** 10.5.0.0/16
* **自定义 DNS 服务器：** 最多可以列出 5 个 DNS 服务器

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>生成 VPN 客户端配置文件

在本部分中，生成并下载配置配置文件。 这些文件用于在客户端计算机上配置本机 VPN 客户端。 有关客户端配置文件的内容的信息，请参阅[点到站点配置 - 证书](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md)。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>配置 VPN 客户端

使用下载的配置文件配置远程访问客户端。 每个操作系统的过程均不同，请按照适用于你的系统的说明进行操作。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>连接分支 VNet

此步骤在中心与分支 VNet 之间创建连接。

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>创建虚拟机

在本部分，在 VNet 中创建两个 VM：VM1 和 VM2。 在网络示意图中，我们使用 10.18.0.4 和 10.18.0.5。 配置 VM 时，请确保选择所创建的虚拟网络（位于“网络”选项卡上）。 有关创建 VM 的步骤，请参阅 [快速入门：创建 VM](../virtual-machines/windows/quick-create-portal.md) 的说明创建一个。

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>保护虚拟中心

标准虚拟中心没有用于保护分支虚拟网络中的资源的内置安全策略。 安全虚拟中心使用 Azure 防火墙或第三方提供程序来管理传入和传出的流量，从而保护 Azure 中的资源。

使用以下文章将中心转换为安全中心：[在虚拟 WAN 中心配置 Azure 防火墙](howto-firewall.md)。

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> 创建规则以管理和筛选流量

创建指示 Azure 防火墙行为的规则。 通过保护中心，我们确保进入虚拟中心的所有数据包在访问 Azure 资源之前都要经过防火墙处理。

完成这些步骤后，需要创建一个体系结构，以允许 VPN 用户访问专用 IP 地址为 10.18.0.4 的 VM，但不能访问专用 IP 地址为 10.18.0.5 的 VM

1. 在 Azure 门户中，导航到“防火墙管理器”。
1. 在“安全性”下，选择“Azure 防火墙策略”。
1. 选择“创建 Azure 防火墙策略”。
1. 在“策略详细信息”下，输入名称，然后选择要部署虚拟中心的区域。
1. 在完成时选择“下一步:DNS 设置(预览)”。
1. 在完成时选择“下一步:规则”。
1. 在“规则”选项卡上，选择“添加规则集合”。
1. 为集合提供一个名称。 将类型设置为“网络”。 添加优先级值 100。
1. 填写规则的名称、源类型、源、协议、目标端口和目标类型，如下例所示。 然后选择“添加”。 此规则允许 VPN 客户端池中的任何 IP 地址访问专用 IP 地址为 10.18.04 的 VM，但不能访问连接到虚拟中心的任何其他资源。 根据所需的体系结构和权限规则创建所需的任何规则。

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="防火墙规则" :::

1. 在完成时选择“下一步:**威胁智能”** 。
1. 在完成时选择“下一步:中心”。
1. 在“中心”选项卡上，选择“关联虚拟中心”。
1. 选择此前创建的虚拟中心，然后选择“添加”。
1. 选择“查看 + 创建”。
1. 选择“创建”。

此过程可能需要 5 分钟或更长时间才能完成。

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>通过 Azure 防火墙路由流量

在本部分，需要确保流量通过 Azure 防火墙路由。

1. 在门户中，从“防火墙管理器”中选择“安全虚拟中心” 。
1. 选择创建的虚拟中心。
1. 在“设置”下，选择“安全配置” 。
1. 在“专用流量”下，选择“通过 Azure 防火墙发送” 。
1. 验证 Azure 防火墙是否保护 VNet 连接和分支连接专用流量。
1. 选择“保存”。 

## <a name="validate"></a><a name="validate"></a>验证

验证安全中心的设置。

1. 通过 VPN 从客户端设备连接到“安全虚拟中心”。
1. 从客户端对 IP 地址 10.18.0.4 执行 Ping 操作。 应该会看到响应。
1. 从客户端对 IP 地址 10.18.0.5 执行 Ping 操作。 应该看不到响应。

### <a name="considerations"></a>注意事项

* 确保安全虚拟中心上的“有效路由表”具有防火墙的专用流量的下一个跃点。 要访问“有效路由表”，请导航到“虚拟中心”资源。 在“连接性”下，选择“路由”，然后选择“有效路由”  。 在此处，选择“默认”路由表。
* 验证是否在[创建规则](#create-rules)部分创建了规则。 如果缺少这些步骤，则你创建的规则实际上将不会与中心关联，并且路由表和数据包流将不会使用 Azure 防火墙。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 常见问题解答](virtual-wan-faq.md)。
* 有关 Azure 防火墙的详细信息，请参阅 [Azure 防火墙常见问题解答](../firewall/firewall-faq.yml)。
