---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820804"
---
1. 找到创建的虚拟 WAN。 在虚拟 WAN 页上的“连接”部分下，选择“中心” 。 单击“新建中心”以打开“创建虚拟中心”页 。

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="屏幕截图显示了中心配置对话框，其中已选择“新建中心”。":::

1. 在“创建虚拟中心”页上，填写相应字段。

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="屏幕截图显示可在其中输入值的“基本信息”选项卡。":::

   * 区域：选择要在其中部署虚拟中心的区域。
   * 名称：想要用于称呼虚拟中心的名称。
   * 中心专用地址空间：用 CIDR 表示法来表示的中心地址范围。

1. 选择“ExpressRoute”选项卡。单击“是”以显示设置并填写字段 。 有关网关缩放单元的信息，请参阅[常见问题解答](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units)。

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="屏幕截图显示可在其中输入值的“ExpressRoute”选项卡。":::

1. 选择“查看 + 创建”以进行验证。
1. 选择“创建”以创建具有 ExpressRoute 网关的中心。 中心大约需要 30 分钟才能完成。 30 分钟后，“刷新”以在“中心”页上查看该中心 。 选择“转到资源”导航到资源。