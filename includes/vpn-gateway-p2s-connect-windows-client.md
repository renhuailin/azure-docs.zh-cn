---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041599"
---
>[!NOTE]
>在要从其进行连接的 Windows 客户端计算机上，你必须拥有管理员权限。
>

1. 若要连接到 VNet，请在客户端计算机上导航到 VPN 设置，找到创建的 VPN 连接。 其名称与虚拟网络的名称相同。 选择“连接”  。 可能会出现与使用证书相关的弹出消息。 选择“继续”，以便使用提升的权限。

1. 在“连接”状态页上，选择“连接”以启动连接。 如果看到 **“选择证书”** 屏幕，请确保所显示的客户端证书是要用于连接的证书。 如果不是，请使用下拉箭头选择正确的证书，并选择“确定”。

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="从 Windows 计算机连接":::

1. 连接已建立。

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="从计算机连接到 Azure VNet - 点到站点连接示意图":::
