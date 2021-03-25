---
title: 教程：为 Microsoft 对等互连配置路由筛选器 - Azure 门户
description: 本教程介绍如何使用 Azure 门户为 Microsoft 对等互连配置路由筛选器。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109130"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>教程：使用 Azure 门户为 Microsoft 对等互连配置路由筛选器

> [!div class="op_single_selector"]
> * [Azure 门户](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由筛选器是通过 Microsoft 对等互连使用部分受支持服务的一种方法。 本文中的步骤可帮助配置和管理 ExpressRoute 线路的路由筛选器。

可通过 Microsoft 对等互连访问 Exchange Online、SharePoint Online 和 Skype for Business 等 Microsoft 365 服务。 如果在 ExpressRoute 线路中配置了 Microsoft 对等互连，则会通过建立的 BGP 会话播发与这些服务相关的所有前缀。 每个前缀附加有 BGP 团体值，以标识通过该前缀提供的服务。 有关 BGP 团体值及其映射到的服务的列表，请参阅 [BGP 团体](expressroute-routing.md#bgp)。

与所有 Azure 和 Microsoft 365 服务的连接会导致大量前缀通过 BGP 进行播发。 大量前缀会显著增加网络中路由器所维护的路由表的大小。 如果打算仅使用通过 Microsoft 对等互连提供的一部分服务，可通过两种方式减少路由表大小。 你可以：

* 通过在 BGP 团体上应用路由筛选器，筛选出不需要的前缀。 路由筛选是标准的网络做法，通常在多个网络中使用。

* 定义路由筛选器，并将其应用于 ExpressRoute 线路。 路由筛选器是一种新资源，可让你选择计划通过 Microsoft 对等互连使用的服务列表。 ExpressRoute 路由器仅发送属于路由筛选器中所标识服务的前缀列表。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 获取 BGP 社区值。
> - 创建路由筛选器和筛选器规则。
> - 将路由筛选器关联到 ExpressRoute 线路。

### <a name="about-route-filters"></a><a name="about"></a>关于路由筛选器

在 ExpressRoute 线路上配置 Microsoft 对等互连时，Microsoft 边缘路由器会通过连接服务提供商的边缘路由器建立一对 BGP 会话。 不会将任何路由播发到网络。 若要能够将路由播发到网络，必须关联路由筛选器。

使用路由筛选器可标识要通过 ExpressRoute 线路的 Microsoft 对等互连使用的服务。 它实质上是所有 BGP 社区值的允许列表。 定义路由筛选器资源并将其附加到 ExpressRoute 线路后，映射到 BGP 社区值的所有前缀均会播发到网络。

若要使用 Microsoft 365 服务附加路由筛选器，必须具备通过 ExpressRoute 使用 Microsoft 365 服务的权限。 如果未被授权通过 ExpressRoute 使用 Microsoft 365 服务，则附加路由筛选器的操作将失败。 若要深入了解授权过程，请参阅[适用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。
> 

## <a name="prerequisites"></a>必备条件

- 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

- 必须具备预配了 Microsoft 对等互连的活动 ExpressRoute 线路。 可使用以下说明完成这些任务：
  - 继续之前，请[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，并让连接服务提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。
  - 如果直接管理 BGP 会话，请[创建 Microsoft 对等互连](expressroute-howto-routing-portal-resource-manager.md)。 或者，让连接提供商为线路预配 Microsoft 对等互连。

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>获取前缀和 BGP 团体值的列表

### <a name="get-a-list-of-bgp-community-values"></a>获取 BGP 团体值列表

可从 [ExpressRoute 路由要求](expressroute-routing.md)页获取与可通过 Microsoft 对等互连访问的服务关联的 BGP 社区值。

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>列出要使用的值

列出要在路由筛选器中使用的 [BGP 社区值](expressroute-routing.md#bgp)列表。 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>创建路由筛选器和筛选器规则

1 个路由筛选器只能有 1 个规则，并且规则类型必须是“允许”。 此规则可以有与之关联的 BGP 团体值列表。

1. 选择“创建资源”，然后搜索“路由筛选器”，如下图所示：

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="显示“路由筛选器”页面的屏幕截图":::

1. 请将路由筛选器放置在资源组中。 确保该位置与 ExpressRoute 路线相同。 选择“查看 + 创建”，然后选择“创建” 。

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="显示“创建路由筛选器”页面的屏幕截图，其中输入了示例值":::

### <a name="create-a-filter-rule"></a>创建筛选器规则

1. 若要添加和更新规则，请为路由筛选器选择“管理规则”选项卡。

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="显示“概述”页面的屏幕截图，其中突出显示了“管理规则”操作":::

1. 从下拉列表中选择希望连接的服务，并在完成后保存规则。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="显示“管理规则”窗口的屏幕截图，其中的“允许的服务社区”下拉列表中年选择了服务":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>将路由筛选器附加到 ExpressRoute 线路

选择“+添加线路”按钮，并从下拉列表中选择 ExpressRoute 线路，将路由筛选器附加到线路中。

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="显示“概述”页面的屏幕截图，其中选择了“添加线路”操作":::

如果连接服务提供商为 ExpressRoute 线路配置了对等互连，请先从 ExpressRoute 线路页面中刷新该线路，再选择“+添加线路”按钮。

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="显示“概述”页面的屏幕截图，其中选择了“刷新”操作。":::

## <a name="common-tasks"></a><a name="tasks"></a>常见任务

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>获取路由筛选器的属性

在门户中打开资源时，可以查看路由筛选器的属性。

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="显示“概述”页的屏幕截图":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>更新路由筛选器的属性

1. 可通过选择“管理规则”按钮更新附加到线路的 BGP 社区值列表。

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="使用“管理规则”操作来更新路由筛选器":::

1. 选择所需的服务社区，然后选择“保存”。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="显示已选择服务的“管理规则”窗口的屏幕截图":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>从 ExpressRoute 线路分离路由筛选器

若要从路由筛选器中分离线路，请右键单击该线路，然后选择“取消关联”。

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="显示“概述”页面的屏幕截图，其中突出显示了“取消关联”操作":::


## <a name="clean-up-resources"></a>清理资源

可选择“删除”按钮来删除路由筛选器。 删除之前，请确保路由筛选器未关联到任何线路。

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="删除路由筛选器":::

## <a name="next-steps"></a>后续步骤

若要了解路由器配置示例，请参阅：

> [!div class="nextstepaction"]
> [用于设置和管理路由的路由器配置示例](expressroute-config-samples-routing.md)
