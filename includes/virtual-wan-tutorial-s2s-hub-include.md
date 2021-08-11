---
title: 包含文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: aacd6a780562476ea338b4c08795e14b6ba39760
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579149"
---
1. 找到你已创建的虚拟 WAN。 在虚拟 WAN 页上的“连接性”部分下，选择“中心” 。
1. 在“中心”页上，选择“+ 新建中心”以打开“创建虚拟中心”页  。

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="屏幕截图显示已选择“基本”选项卡的“创建虚拟中心”窗格。" border="false":::
1. 在“创建虚拟中心”页上的“基本”选项卡上，请填写以下字段 ：

   * 区域（以前称为“位置”）
   * 名称
   * 中心专用地址空间 - 用于创建中心的最小地址空间为 /24。 如果使用从 /25 到 /32 的范围中的任何空间，则该空间会在创建过程中产生错误。 无需为虚拟中心内的服务显式计划子网地址空间。 由于 Azure 虚拟 WAN 是一项托管服务，因此它在虚拟中心为不同的网关/服务（例如，VPN 网关、ExpressRoute 网关、用户 VPN 点到站点网关、防火墙、路由，等等）创建相应的子网。
