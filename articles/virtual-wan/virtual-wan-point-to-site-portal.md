---
title: 教程：使用 Azure 虚拟 WAN 创建与 Azure 的点到站点连接
description: 在本教程中，学习如何使用 Azure 虚拟 WAN 创建与 Azure 的用户 VPN（点到站点）连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779889"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建用户 VPN 连接

本教程介绍如何使用虚拟 WAN 通过 OpenVPN 或 IPsec/IKE (IKEv2) VPN 连接（使用用户 VPN (P2S) 配置）来与 Azure 中的资源建立连接。 此类连接要求在每个正在连接的客户端计算机上配置本机 VPN 客户端。
* 如果要使用 Azure AD 身份验证创建用户 VPN 连接，请改为使用[配置用户 VPN 连接 - Azure Active Directory 身份验证](virtual-wan-point-to-site-azure-ad.md)一文。
* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟 WAN
> * 创建用户 VPN 配置
> * 创建虚拟中心和网关
> * 生成客户端配置文件
> * 配置 VPN 客户端
> * 连接到 VNet
> * 查看虚拟 WAN
> * 修改设置

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="虚拟 WAN 示意图。":::

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>创建虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>创建用户 VPN 配置

用户 VPN (P2S) 配置定义参数以便远程客户端进行连接。 遵循的说明取决于要使用的身份验证方法。

在以下步骤中，选择身份验证方法时，有三种选择。 每种方法都有特定的要求。 选择以下某种方法，然后完成步骤。

* **Azure 证书：** 此配置需要证书。 你需要生成或获取证书。 每个客户端都需要客户端证书。 此外，需要上传根证书信息（公钥）。 有关所需证书的详细信息，请参阅[生成和导出证书](certificates-point-to-site.md)。

* Azure Active Directory 身份验证：使用[配置用户 VPN 连接 - Azure Active Directory 身份验证](virtual-wan-point-to-site-azure-ad.md)一文，其中包含此配置所需的特定步骤。

* **基于 RADIUS 的身份验证：** 获取 RADIUS 服务器 IP、RADIUS 服务器机密和证书信息。

### <a name="configuration-steps"></a>配置步骤

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>创建虚拟中心和网关

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>生成客户端配置文件

使用用户 VPN (P2S) 连接到 VNet 时，可以使用在连接的操作系统上本机安装的 VPN 客户端。 VPN 客户端的所有必需配置设置都包含在 VPN 客户端 zip 配置文件中。 zip 文件中的设置有助于轻松配置 VPN 客户端。 生成的 VPN 客户端配置文件特定于网关的用户 VPN 配置。 在本部分中，你会生成并下载用于配置 VPN 客户端的文件。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>配置 VPN 客户端

使用下载的配置文件包配置远程访问 VPN 客户端。 每个操作系统的过程各不相同。 按照适用于你的系统的说明进行操作。
完成客户端配置后，即可连接。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>将 VNet 连接到中心

在本部分中，你会创建虚拟中心与 VNet 之间的连接。 对于本教程，无需配置路由设置。

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。

1. 在“概述”页上，地图中的每个点表示一个中心。

1. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="modify-settings"></a>修改设置

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>修改客户端地址池

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>修改 DNS 服务器

1. 导航到“虚拟中心”->“用户 VPN (点到站点)”。

1. 单击“自定义 DNS 服务器”旁边的值，以打开“编辑用户 VPN 网关”页面 。

1. 在“编辑用户 VPN 网关”页面上，编辑“自定义 DNS 服务器”字段 。 在“自定义 DNS 服务器”文本框中输入 DNS 服务器 IP 地址。 最多可指定五个 DNS 服务器。

1. 单击页面底部的“编辑”以验证您的设置。

1. 单击“确认”以保存设置。 此页上的任何更改可能需要长达 30 分钟才能完成。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

当不再需要所创建的资源时，请将其删除。 由于存在依赖关系，必须按特定顺序删除某些虚拟 WAN 资源。 大约需要 30 分钟才能完成删除。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>后续步骤


> [!div class="nextstepaction"]
> * [管理对分支 VNet 中资源的安全访问](manage-secure-access-resources-spoke-p2s.md)
