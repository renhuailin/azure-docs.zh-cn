---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: dd49171093815c7f91eeebaea0c35a25bb469d3c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321867"
---
1. 在“创建虚拟中心”页上，单击“站点到站点”以打开“站点到站点”选项卡。

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="屏幕截图显示已选择“站点到站点”的“创建虚拟中心”窗格。":::

1. 在“站点到站点”选项卡上填写以下字段：

   * 选择“是”以创建站点到站点 VPN。
   * AS 编号：“AS 编号”字段是无法编辑的。
   * 网关缩放单元：从下拉列表中选择“网关缩放单元”值 。 缩放单元允许选择在虚拟中心内创建的、要将站点连接到的 VPN 网关的聚合吞吐量。 如果选择 1 个 500 Mbps 的缩放单元，则表示会创建两个实例以实现冗余，每个实例的最大吞吐量为 500 Mbps。 例如，如果你有 5 个分支，每个分支执行 10 Mbps 的 IO，则前端的聚合吞吐量需要达到 50 Mbps。 应在评估支持中心的分支数量所需的容量后，再规划 Azure VPN 网关的聚合容量。
   * 路由首选项：选择合适的“路由首选项” 。 Azure 路由首选项允许你选择流量在 Azure 和 Internet 之间的路由方式。 可以选择通过 Microsoft 网络或通过 ISP 网络（公共 Internet）来路由流量。 这些选项也分别称为“冷土豆路由”和“热土豆路由”。 虚拟 WAN 中的公共 IP 地址由服务基于所选路由选项分配。 有关通过 Microsoft 网络或 ISP 设置路由首选项的详细信息，请参阅[路由首选项](../articles/virtual-network/routing-preference-overview.md)。
1. 选择“查看 + 创建”以进行验证。
1. 选择“创建”以创建中心和网关。 此步骤可能最多需要 30 分钟。 30 分钟后，“刷新”以在“中心”页上查看该中心 。 选择“转到资源”导航到资源。