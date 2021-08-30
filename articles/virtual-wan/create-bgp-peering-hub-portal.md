---
title: 创建与虚拟中心的 BGP 对等互连（预览版）- Azure 门户
titleSuffix: Azure Virtual WAN
description: 了解如何创建与虚拟 WAN 中心路由器的 BGP 对等互连。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779712"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>如何创建与虚拟中心的 BGP 对等互连（预览版）- Azure 门户

本文可帮助你使用 Azure 门户将 Azure 虚拟 WAN 中心路由器配置为与虚拟网络中的网络虚拟设备 (NVA) 对等互连。 虚拟中心路由器从连接到虚拟 WAN 中心的分支 VNet 中的 NVA 获知路由。 虚拟中心路由器还会将虚拟网络路由播发到 NVA。 有关详细信息，请参阅[方案：与虚拟中心的 BGP 对等互连](scenario-bgp-peering-hub.md)。

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="配置图。":::

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 与虚拟 WAN 中心的 BGP 对等互连功能目前以封闭公开预览版形式提供。 如果有兴趣试用此功能，请发送电子邮件至 previewbgpwithvhub@microsoft.com 并附上虚拟 WAN 资源的资源 ID。 
>
> 要查找资源 ID，请打开 Azure 门户，导航到虚拟 WAN 资源，并单击“设置”>“属性”>“资源 ID”。<br> 示例： `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

在开始配置之前，请验证你是否符合以下条件：

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>创建虚拟 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>创建中心

中心是一种虚拟网络，可包含适用于站点到站点、ExpressRoute 或点到站点功能的网关。 创建中心后，即使你没有附加任何站点，也会对该中心收取费用。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>将 VNet 连接到中心

在本部分中，将创建中心和 VNet 之间的连接。

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>配置 BGP 对等机

1.  使用 [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2) 打开 [Azure 预览门户](https://aka.ms/azurecortexv2)。 与虚拟 WAN 中心的 BGP 对等互连功能目前提供托管预览版，“配置”页面在常规 Azure 门户中不可用。

1.  在虚拟 WAN 的门户页面中，选择“连接”部分中的“中心”，查看中心列表 。 单击某个中心，以配置 BGP 对等机。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="中心的屏幕截图。":::

1.  在“虚拟中心”页面中，选择“路由”部分下的“BGP 对等机”，然后单击“+ 添加”，以添加 BGP 对等机   。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  在“添加 BGP 对等机”页面，填写所有字段。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **名称** - 标识特定 BGP 对等机的资源名称。 
    * **ASN** - BGP 对等机的 ASN。
    * **IPv4 地址** - BGP 对等机的 IPv4 地址。
    * **虚拟网络连接** - 选择与托管 BGP 对等机的虚拟网络对应的连接标识符。

1.  单击“添加”，完成 BGP 对等机配置并查看该对等机。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="添加的对等机的屏幕截图。":::

## <a name="modify-a-bgp-peer"></a>修改 BGP 对等机

1. 在“虚拟中心”资源中，单击“BGP 对等机”并选择相应 BGP 对等机 。 单击“…” 然后单击“编辑”。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="“编辑”的屏幕截图。":::

1. 修改 BGP 对等机后，单击“添加”以保存。

## <a name="delete-a-bgp-peer"></a>删除 BGP 对等机

1. 在“虚拟中心”资源中，单击“BGP 对等机”并选择相应 BGP 对等机 。 单击“…” 然后单击“删除”。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="删除的对等机的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

* 要详细了解 BGP 方案，请参阅[方案：与虚拟中心的 BGP 对等互连](scenario-bgp-peering-hub.md)。
