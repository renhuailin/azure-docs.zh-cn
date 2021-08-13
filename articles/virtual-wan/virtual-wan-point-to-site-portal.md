---
title: 教程：使用 Azure 虚拟 WAN 创建与 Azure 的点到站点连接
description: 在本教程中，学习如何使用 Azure 虚拟 WAN 创建与 Azure 的用户 VPN（点到站点）连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: 266e6768af6ac78f70510ce77e3fb9b8890e2ce2
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362292"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建用户 VPN 连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE (IKEv2) 或 OpenVPN VPN 连接与 Azure 中的资源建立连接。 此类连接要求在客户端计算机上配置 VPN 客户端。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟 WAN
> * 创建 P2S 配置
> * 创建虚拟中心
> * 选择客户端地址池
> * 指定 DNS 服务器
> * 生成 VPN 客户端配置文件配置包
> * 配置 VPN 客户端
> * 查看虚拟 WAN

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>创建虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>创建 P2S 配置

点到站点 (P2S) 配置定义连接远程客户端的参数。

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>创建虚拟中心和网关

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> 选择 P2S 客户端地址池

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>指定 DNS 服务器

你可以在创建中心时配置此设置，也可以在以后对其进行修改。 若要进行修改，请找到虚拟中心。 在“用户 VPN(点到站点)”下，选择“配置”，然后在“自定义 DNS 服务器”文本框中输入 DNS 服务器 IP 地址  。 最多可以指定 5 个 DNS 服务器。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="自定义 DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>生成 VPN 客户端配置文件包

生成并下载 VPN 客户端配置文件包以配置 VPN 客户端。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>配置 VPN 客户端

使用下载的配置文件包配置远程访问 VPN 客户端。 每个操作系统的过程各不相同。 按照适用于你的系统的说明进行操作。
完成客户端配置后，即可连接。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
1. 在“概述”页上，地图中的每个点表示一个中心。
1. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

当不再需要所创建的资源时，请将其删除。 由于存在依赖关系，必须按特定顺序删除某些虚拟 WAN 资源。 大约需要 30 分钟才能完成删除。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>后续步骤

接下来，若要详细了解虚拟 WAN，请参阅：

> [!div class="nextstepaction"]
> * [虚拟 WAN 常见问题解答](virtual-wan-faq.md)
