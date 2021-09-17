---
title: 在 Azure 门户中创建仪表板
description: 本文介绍如何在 Azure 门户中创建和自定义仪表板。
ms.topic: how-to
ms.date: 08/19/2021
ms.openlocfilehash: af26cc36f73088c9a4231e59f3414efaa828d452
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597710"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>在 Azure 门户中创建仪表板

仪表板是 Azure 门户中具有针对性和组织有序的云资源视图。 可将仪表板用作工作区，在其中可以监视资源，以及快速启动日常操作的任务。 例如，基于项目、任务或用户角色生成自定义仪表板。

Azure 门户提供默认仪表板作为起点。 可以编辑默认仪表板，然后创建和自定义其他仪表板。

> [!NOTE]
> 每个用户最多可以创建 100 个专用仪表板。 若[发布并分享该仪表板](azure-portal-dashboard-share-access.md)，其将在订阅中实现为 Azure 资源，并且不会计入此限制。

本文介绍如何创建新的仪表板并对其进行自定义。 有关共享仪表板的信息，请参阅[使用 Azure 基于角色的访问控制共享 Azure 仪表板](azure-portal-dashboard-share-access.md)。

## <a name="create-a-new-dashboard"></a>创建新的仪表板

此示例演示如何创建具有指定名称的新专用仪表板。 所有仪表板在创建时即为专用仪表板，不过如果你愿意，可以选择向组织中的其他用户发布和分享仪表板。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“仪表板”  。 默认视图可能已设置为“仪表板”。

    :::image type="content" source="media/azure-portal-dashboards/portal-menu-dashboard.png" alt-text="已选择“仪表板”的 Azure 门户的屏幕截图。":::

1. 依次选择“新建仪表板”、“空白仪表板” 。

    :::image type="content" source="media/azure-portal-dashboards/create-new-dashboard.png" alt-text="“新建仪表板”选项的屏幕截图。":::

    此操作会打开可供你选择磁贴的“磁贴库”，以及可供你排列磁贴的空网格。

1. 选择仪表板标签中的“我的仪表板”文本，并输入一个名称以帮助你轻松识别自定义仪表板。 

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="空网格和磁贴库的屏幕截图。":::

1. 要按原样保存仪表板，请在页眉中选择“完成自定义”。 或继续执行下一部分的步骤 2，添加磁贴并保存仪表板。

此时，仪表板视图将显示新仪表板。 选择仪表板名称旁的箭头，以查看可供你使用的仪表板。 此列表可能包括其他用户已创建和共享的仪表板。

## <a name="edit-a-dashboard"></a>编辑仪表板

现在，让我们编辑仪表板以添加、调整和排列代表你的 Azure 资源的磁贴。

### <a name="add-tiles-from-the-tile-gallery"></a>从磁贴库添加磁贴

若要将磁贴添加到仪表板，请执行以下步骤：

1. 从仪表板的页眉选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-edit.png" alt-text="突出显示“编辑”选项的仪表板的屏幕截图。":::

1. 浏览“磁贴库”，或使用搜索字段查找某个磁贴。 选择要添加到仪表板的磁贴。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="库的屏幕截图。":::

1. 选择“添加”，以使用默认大小和位置将磁贴添加到仪表板中。  或者将磁贴拖到网格中，并将其放到所需的位置。 添加所需的任何磁贴；下面是几点思路：

    - 添加“所有资源”可查看所有已创建的资源。

    - 如果你在多家组织中工作，可将“组织标识”磁贴添加到仪表板，以明确显示资源所属的组织。 

1. 如果需要，[重设磁贴的大小或重新排列磁贴](#resize-or-rearrange-tiles)。

1. 要保存更改，请在页眉中选择“保存”。 通过在页眉中选择“预览”还可以在不保存的情况下预览更改。 此预览模式还可用于查看[筛选器](#set-and-override-dashboard-filters)如何影响磁贴。 在预览屏幕上，可以选择“保存”以保留更改，选择“放弃”以删除更改，也可以选择“编辑”以返回到编辑选项并进行进一步更改  。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="“预览”、“保存”和“放弃”选项的屏幕截图。":::

> [!NOTE]
> 使用 markdown 磁贴可以在仪表板上显示自定义的静态内容。 这可能是基本说明、一幅图像、一组超链接甚至联系信息。 有关使用 Markdown 磁贴的详细信息，请参阅[在 Azure 仪表板上使用 Markdown 磁贴显示自定义内容](azure-portal-markdown-tile.md)。

### <a name="pin-content-from-a-resource-page"></a>固定资源页中的内容

将磁贴添加到仪表板的另一种方法是直接从资源页添加。

许多资源页在页眉中包含固定图标，这意味着可以固定表示资源页的磁贴。 在某些情况下，固定图标也可能会显示在页面中的特定内容旁边，这意味着可以固定该特定内容的磁贴，而不是整个页面。

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-blade.png" alt-text="包含固定图标的页面命令栏屏幕截图。":::

如果选择此图标，可以将磁贴固定到现有的专用或共享仪表板。 还可以选择“新建”来新建仪表板，其中将包含此固定。

:::image type="content" source="media/azure-portal-dashboards/dashboard-pin-pane.png" alt-text="“固定到仪表板”选项的屏幕截图。":::

### <a name="copy-a-tile-to-a-new-dashboard"></a>将磁贴复制到新仪表板

如果想要在不同仪表板上重新使用磁贴，可以将其从一个仪表板复制到另一个仪表板。 为此，请选择右上角的上下文菜单，然后选择“复制”。

:::image type="content" source="media/azure-portal-dashboards/copy-dashboard.png" alt-text="显示如何在 Azure 门户中复制磁贴的屏幕截图。":::

然后，可以选择是将磁贴复制到现有的专用或共享仪表板，还是在已在其中工作的仪表板内创建磁贴的副本。 还可以选择“新建”来新建仪表板，其中将包含磁贴的副本。

### <a name="resize-or-rearrange-tiles"></a>调整或重新排列磁贴

若要在仪表板上更改磁贴大小或重新排列磁贴，请执行以下步骤：

1. 从页头中选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”。 

1. 选择磁贴右上角的上下文菜单。 然后选择磁贴大小。 支持任何大小的磁贴还会在右下角提供“控点”，用于拖动调整磁贴的大小。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="磁贴大小菜单已打开的仪表板的屏幕截图。":::

1. 选择一个磁贴，并将其拖到网格上的新位置以排列仪表板。

### <a name="set-and-override-dashboard-filters"></a>设置和替代仪表板筛选器

仪表板顶部附近会显示用于设置仪表板中显示的数据的“自动刷新”和“时间设置”的选项，以及用于添加其他筛选器的选项。 

:::image type="content" source="media/azure-portal-dashboards/dashboard-global-filters.png" alt-text="显示仪表板的全局筛选器的屏幕截图。":::

默认情况下，每小时刷新一次数据。 若要更改此设置，请选择“自动刷新”，并选择新的刷新间隔。 完成选择后，选择“应用”。

默认时间设置为“UTC 时间”，显示“过去 24 小时”的数据。  若要更改此设置，请选择按钮并选择新的时间范围、时间粒度和/或时区，然后选择“应用”。

若要应用其他筛选器，请选择“添加筛选器”。 将要看到的选项因仪表板中的磁贴而异。 例如，可能只能显示特定订阅或位置的数据。 选择要使用的筛选器并进行选择。 然后，筛选器将应用于数据。 若要删除筛选器，请在其按钮中选择“X”。

支持筛选的磁贴在磁贴的左上角有一个![筛选器图标](./media/azure-portal-dashboards/dashboard-filter.png)筛选器图标。 某些磁贴允许用特定于该磁贴的筛选器替代全局筛选器。 为此，请从上下文菜单中选择“配置磁贴数据”，或选择筛选器图标，然后应用所需筛选器。

如果为特定磁贴设置筛选器，则该磁贴左上角将显示一个双筛选器图标，表示该磁贴中的数据反映其自己的筛选器。

:::image type="content" source="media/azure-portal-dashboards/dashboard-filter-override.png" alt-text="显示带筛选器替代的磁贴图标的屏幕截图。":::

## <a name="modify-tile-settings"></a>修改磁贴设置

某些磁贴可能需要额外配置才能显示所需的信息。 例如，“指标图表”磁贴必须经过设置才能显示 Azure Monitor 中的指标。 还可以自定义磁贴数据以替代仪表板的默认时间设置和筛选器。

### <a name="complete-tile-configuration"></a>完成磁贴配置

需要设置的任何磁贴在自定义它之前会显示一个横幅。 例如，在“指标图表”中，横幅写着“在指标中编辑”。  其他横幅可能会使用不同的文本，如“配置磁贴”。

若要自定义磁贴，请执行以下操作：

1. 在页标题中，选择“保存”退出编辑模式。

1. 请选择横幅，然后执行所需的设置。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-configure-tile.png" alt-text="需要配置的磁贴的屏幕截图":::

### <a name="customize-time-span-for-a-tile"></a>为磁贴自定义时间跨度

仪表板上的数据基于全局筛选器显示活动和刷新。 某些磁贴将允许仅为一个磁贴选择不同的时间跨度。 为此，请执行下列步骤：

1. 从上下文菜单或磁贴左上角的![筛选器图标](./media/azure-portal-dashboards/dashboard-filter.png)中选择“自定义磁贴数据”。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-customize-tile-data.png" alt-text="磁贴上下文菜单的屏幕截图。":::

1. 选中“在磁贴级别替代仪表板时间设置”对应的复选框。 

    :::image type="content" source="media/azure-portal-dashboards/dashboard-override-time-settings.png" alt-text="用于配置磁贴时间设置的对话框屏幕截图。":::

1. 选择要为此磁贴显示的时间跨度。 可以选择过去 30 分钟到过去 30 天的时间，或者自定义范围。

1. 选择要显示的时间粒度。  可以使用 1 分钟到 1 个月的增量作为显示粒度。

1. 选择“应用”。 

### <a name="change-the-title-and-subtitle-of-a-tile"></a>更改磁贴的标题和副标题

部分磁贴允许编辑其标题和副标题。 为此，请从上下文菜单中选择“配置磁贴设置”。

:::image type="content" source="media/azure-portal-dashboards/dashboard-tile-rename.png" alt-text="显示“配置磁贴设置”选项的屏幕截图。":::

对磁贴的标题和/或副标题作出任意更改，然后选择“应用”。

:::image type="content" source="media/azure-portal-dashboards/dashboard-title-subtitle.png" alt-text="显示如何更改磁贴标题和副标题的屏幕截图。":::
 
## <a name="delete-a-tile"></a>删除磁贴

要从仪表板中删除磁贴，请执行以下操作之一：

- 选择磁贴右上角的上下文菜单，然后选择“从仪表板中删除”。 

- 选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”进入自定义模式。  将鼠标悬停在磁贴的右上角，然后选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)删除图标从仪表板中删除该磁贴。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-tile.png" alt-text="演示如何从仪表板中删除磁贴的屏幕截图。":::

## <a name="clone-a-dashboard"></a>克隆仪表板

若要使用现有仪表板作为新仪表板的模板，请执行以下步骤：

1. 确保仪表板视图显示了要复制的仪表板。

1. 在  页头中，选择![克隆图标](./media/azure-portal-dashboards/dashboard-clone.png)“克隆”。 

1. 随后会在编辑模式下打开名为“<你的仪表板名称>的副本”的仪表板副本。   使用本文前面所述的步骤重命名和自定义该仪表板。

## <a name="publish-and-share-a-dashboard"></a>发布和共享仪表板

创建仪表板时，默认该仪表板是专用的，这意味着只有你才可以看到它。 要使仪表板可供其他人使用，可以发布和共享这些仪表板。 有关详细信息，请参阅[使用 Azure 基于角色的访问控制共享 Azure 仪表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>打开共享的仪表板

若要查找并打开共享的仪表板，请执行以下步骤：

1. 选择仪表板名称旁边的箭头。

1. 从显示的仪表板列表中进行选择。 如果要打开的仪表板未列出，请执行以下操作：

    1. 选择“浏览所有仪表板”  。

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse.png" alt-text="仪表板选择菜单的屏幕截图。":::

    1. 在“类型”字段中，选择“共享的仪表板”。  

        :::image type="content" source="media/azure-portal-dashboards/dashboard-browse-all.png" alt-text="所有仪表板选择菜单的屏幕截图。":::

    1. 选择一个或多个订阅。 还可以输入文本以按名称筛选仪表板。

    1. 从共享仪表板列表中选择一个仪表板。

## <a name="delete-a-dashboard"></a>删除仪表板

若要永久删除专用或共享仪表板，请执行以下步骤：

1. 从仪表板名称旁的列表中选择要删除的仪表板。

1. 从页头中选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)“删除”。 

1. 对于专用仪表板，请在确认对话框中选择“确定”以删除仪表板。  对于共享仪表板，请在确认对话框中选中相应的复选框，以确认发布的仪表板不再可供其他人查看。 选择“确定”。 

    :::image type="content" source="media/azure-portal-dashboards/dashboard-delete-dash.png" alt-text="确认删除操作的屏幕截图。":::

## <a name="recover-a-deleted-dashboard"></a>恢复已删除的仪表板

如果你在 Azure 全球云中操作，并在 Azure 门户中删除了已发布的仪表板，可以在删除后的 14 天内恢复该仪表板。 有关详细信息，请参阅[在 Azure 门户中恢复已删除的仪表板](recover-shared-deleted-dashboard.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 基于角色的访问控制共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
- [以编程方式创建 Azure 仪表板](azure-portal-dashboards-create-programmatically.md)
