---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479530"
---
1. 打开虚拟网络网关的页面。 可通过转到你的 VNet 名称 ->“概述”->“已连接的设备”-> 你的网关名称来导航到网关（但也可以通过多种其他方法进行导航）。
1. 在网关的页面上，选择“连接”。 在“连接”页的顶部，选择“+添加”打开“添加连接”页。

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="站点到站点连接":::
1. 在“添加连接”页上，配置连接的值。

   * **名称：** 命名连接。
   * **连接类型：** 选择“站点到站点(IPsec)”。
   * **虚拟网络网关：** 由于要从此网关连接，因此该值是固定的。
   * **本地网络网关：** 选择“选择本地网关”并选择要使用的本地网关。
   * “共享密钥”：此处的值必须与用于本地 VPN 设备的值匹配。 此示例使用“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。
   * 将“使用 Azure 专用 IP 地址”保持为未选中状态。
   * 将“启用 BGP”保持为未选中状态。
   * 选择“IKEv2”。
   * 剩下的“订阅”、“资源组”和“位置”值是固定的。

1. 选择“确定”以创建连接。 会看到屏幕上闪烁“正在创建连接”。
1. 可在虚拟网络网关的“连接”页中查看连接。 “状态”会从“未知”转换为“正在连接”，再转换为“成功”。
