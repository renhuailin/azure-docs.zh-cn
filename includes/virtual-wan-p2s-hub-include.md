---
title: 包含文件
description: 包含文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1abf76ee8e6c44eaa61d9de38b163f230327edd
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362291"
---
1. 在左侧窗格的虚拟 WAN 页上，选择“中心”。 在“中心”页上，选择“+新建中心”。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="新建中心的屏幕截图。":::

1. 在“创建虚拟中心”页上，请填写以下字段：

   * **区域** - 选择要在其中部署虚拟中心的区域。
   * **名称** - 输入要用于称呼虚拟中心的名称。
   * **中心专用地址空间** - 用 CIDR 表示法来表示的中心地址范围。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="创建虚拟中心的屏幕截图。":::

1. 在“点到站点”选项卡上，填写以下字段：

   * **网关缩放单元** - 表示用户 VPN 网关的聚合容量。
   * **点到站点配置** - 已在上一步中创建。
   * **客户端地址池** - 用于远程用户。
   * **自定义 DNS 服务器 IP**。
   * 路由首选项 - 选择合适的“路由首选项”。 Azure 路由首选项允许你选择流量在 Azure 和 Internet 之间的路由方式。 可以选择通过 Microsoft 网络或通过 ISP 网络（公共 Internet）来路由流量。 这些选项也分别称为“冷土豆路由”和“热土豆路由”。 虚拟 WAN 中的公共 IP 地址由服务基于所选路由选项分配。 有关通过 Microsoft 网络或 ISP 设置路由首选项的详细信息，请参阅[路由首选项](../articles/virtual-network/routing-preference-overview.md)一文。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="选择了点到站点的虚拟中心配置的屏幕截图。":::

1. 选择“查看 + 创建”。
1. 在“验证已通过”页上，选择“创建” 。