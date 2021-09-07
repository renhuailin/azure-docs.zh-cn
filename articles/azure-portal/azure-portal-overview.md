---
title: Azure 门户概述
description: Azure 门户是一个图形用户界面，可用于管理 Azure 服务。 了解如何在 Azure 门户中导航和查找资源。
keywords: portal
ms.date: 08/30/2021
ms.topic: overview
ms.openlocfilehash: 32ca1f0032ab5ced2157bb73ccf5c3f40df86707
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224510"
---
# <a name="azure-portal-overview"></a>Azure 门户概述

本文介绍 Azure 门户及其页面元素，并帮助你熟悉 Azure 门户的管理体验。

## <a name="what-is-the-azure-portal"></a>什么是 Azure 门户？

Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。 通过 Azure 门户，可以使用图形用户界面管理 Azure 订阅。 可以生成、管理和监视从简单 Web 应用到复杂云部署的所有资源。 创建自定义仪表板以有序地显示资源。 配置辅助功能选项以提供最佳体验。

Azure 门户旨在实现复原能力和持续可用性。 每个 Azure 数据中心都提供该门户。 此配置使得 Azure 门户能够灵活应对各种数据中心故障，此外，由于部署位置靠近用户，可以避免网络减速。 Azure 门户会持续更新，并且无需停机进行维护活动。

## <a name="azure-portal-menu"></a>Azure 门户菜单

可以[选择门户菜单的默认模式](set-preferences.md#set-menu-behavior)。 可以停靠菜单，或者以浮出面板的形式显示菜单。

当门户菜单处于浮出模式时，在调用之前它会隐藏。 选择菜单图标可以打开或关闭菜单。

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png" alt-text="Azure 门户菜单栏（浮出模式）的屏幕截图。":::

如果为门户菜单选择停靠模式，则它始终可见。 可以折叠菜单以提供更多的工作空间。

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png" alt-text="Azure 门户菜单栏（停靠模式）的屏幕截图。":::

## <a name="azure-home"></a>Azure 主页

作为 Azure 服务的新订阅者，[登录到门户](https://portal.azure.com)后，看到的第一个画面就是“Azure 主页”。 在此页面中，可以编译资源来帮助你充分利用 Azure 订阅。 我们已包含免费在线课程、文档、核心服务和有用网站的链接，可让你保持最新状态并管理贵组织的变更。 为了方便你快速访问正在处理的工作，该页面还会显示最近访问的资源列表。

你无法自定义此页，但可以选择是将“主页”还是“仪表板”视为默认视图 。 首次登录时，页面顶部会出现提示，可在此处保存你的首选项。 你可以[随时在“门户设置”中更改启动页面选择](set-preferences.md#startup-page)。

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png" alt-text="显示 Azure 门户中启动页选项的屏幕截图。":::

## <a name="azure-dashboard"></a>Azure 仪表板

仪表板提供订阅中对你最重要的资源的聚焦视图。 我们提供了一个默认仪表板来帮助你入门。 可以自定义此仪表板，以将最常用的资源放到单个视图中。 对默认视图所做的任何更改仅影响你的体验。 但是，你可以创建其他仪表板供自己使用，也可以发布自定义仪表板，并与组织中的其他用户共享这些仪表板。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

如上所述，如果想要在登录 Azure 门户时查看最近使用的[仪表板](azure-portal-dashboards.md)，则可以[将启动页设置为仪表板](set-preferences.md#startup-page)。

## <a name="getting-around-the-portal"></a>门户的布局

了解基本门户布局及其交互方式会很有用。 在这里，我们将介绍用户界面的组件以及用于说明的一些术语。 如需详细了解门户概览，请参阅课程[导航到门户](/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 门户菜单和页眉是始终存在的全局元素。 这些持久功能是与每个单独服务或功能关联的用户界面的“外壳”，而标头可提供对全局控件的访问。 资源的配置页（有时称为“边栏选项卡”）还可能会提供一个资源菜单，以帮助你在功能之间切换。

下图标记了 Azure 门户的基本元素，而每个元素都在下表中进行了说明。 本示例中，当前焦点是一个虚拟机，但无论使用的是何种类型的资源或服务，都适用相同的元素。

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-callouts.png" alt-text="显示全屏门户视图和 UI 元素项的屏幕截图。":::

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png" alt-text="显示扩展的门户视图和 UI 元素项的屏幕截图。":::

|键|说明
|:---:|---|
|1|页头。 显示在每个门户页面的顶部，包含全局元素。|
|2|全局搜索。 使用搜索栏可以快速查找特定的资源、服务或文档。|
|3|全局控件。 与所有全局元素一样，这些功能会在整个门户中保留，包括：Cloud Shell、订阅筛选器、通知、门户设置、帮助和支持，以及向我们发送反馈。|
|4|你的帐户。 查看有关帐户的信息、切换目录、注销，或使用其他帐户登录。|
|5|Azure 门户菜单。 此全局元素有助于在服务之间进行导航。 有时称为边栏。 （列表中的项 9 和 10 显示在此菜单中。）|
|6|资源菜单。 许多服务都提供一个资源菜单用于帮助管理服务。 此元素有时称为左窗格。 此时，你将看到与当前焦点相关的命令。|
|7|命令栏。 这些控件与当前焦点相关。|
|8|工作窗格。 显示有关当前焦点所在资源的详细信息。|
|9|痕迹导航。 可以使用痕迹导航链接在工作流中返回一个级别。|
|10|用于在当前订阅中创建新资源的主控件。 展开或打开 Azure 门户菜单，查找“+ 创建资源”。 还可以在“主页”上找到此选项。 然后，搜索或浏览 Azure 市场以获取要创建的资源类型。|
|11|Azure 门户菜单中的“收藏夹”列表。 若要了解如何自定义此列表，请参阅[添加、删除和排序收藏夹](../azure-portal/azure-portal-add-remove-sort-favorites.md)。|

## <a name="get-started-with-services"></a>服务入门

如果你是新订阅者，则必须先创建资源，然后才能进行管理。 选择“+ 创建资源”查看 Azure 市场中提供的服务。 你将在这里找到来自许多提供商的数百个应用程序和服务，它们都经过认证，可以在 Azure 上运行。

我们预先填充了边栏中的[收藏夹](../azure-portal/azure-portal-add-remove-sort-favorites.md)与常用服务的链接。  若要查看所有可用服务，请在侧栏中选择“所有服务”。

> [!TIP]
> 使用全局页头中的“搜索”可以最快地找到资源、服务或文档。

观看此视频，了解如何在 Azure 门户中使用全局搜索。

> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 门户中使用全局搜索](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>后续步骤

* 在[支持的浏览器和设备](../azure-portal/azure-portal-supported-browsers-devices.md)中详细了解运行 Azure 门户的位置。
* 随时随地通过 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)保持连接。
* 通过 [Azure 快速入门中心](../azure-portal/azure-portal-quickstart-center.md)载入和设置云环境。