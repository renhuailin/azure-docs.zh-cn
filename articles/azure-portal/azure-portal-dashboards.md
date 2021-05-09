---
title: 在 Azure 门户中创建仪表板
description: 本文介绍如何在 Azure 门户中创建和自定义仪表板。
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726034"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>在 Azure 门户中创建仪表板

仪表板是 Azure 门户中具有针对性和组织有序的云资源视图。 可将仪表板用作工作区，在其中可以监视资源，以及快速启动日常操作的任务。 例如，基于项目、任务或用户角色生成自定义仪表板。

Azure 门户提供默认仪表板作为起点。 可以编辑默认仪表板，然后创建和自定义其他仪表板。

> [!NOTE]
> 每个用户最多可以创建 100 个专用仪表板。 如果你[发布并分享该仪表板](azure-portal-dashboard-share-access.md)，它将在你的订阅中实现为 Azure 资源，并且不会计入此限制。

本文介绍如何创建新的仪表板并对其进行自定义。 有关共享仪表板的信息，请参阅[使用 Azure 基于角色的访问控制共享 Azure 仪表板](azure-portal-dashboard-share-access.md)。

## <a name="create-a-new-dashboard"></a>创建新的仪表板

此示例演示如何创建具有指定名称的新专用仪表板。 所有仪表板在创建时即为专用仪表板，不过如果你愿意，可以选择向组织中的其他用户发布和分享仪表板。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“仪表板”  。 默认视图可能已设置为“仪表板”。

    ![已选择“仪表板”的 Azure 门户的屏幕截图。](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. 依次选择“新建仪表板”、“空白仪表板” 。

    ![“新建仪表板”选项的屏幕截图。](./media/azure-portal-dashboards/create-new-dashboard.png)

    此操作会打开可供你选择磁贴的“磁贴库”，以及可供你排列磁贴的空网格。

1. 选择仪表板标签中的“我的仪表板”文本，并输入一个名称以帮助你轻松识别自定义仪表板。 

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="空网格和磁贴库的屏幕截图。":::

1. 要按原样保存仪表板，请在页眉中选择“完成自定义”。 或转到下一部分，添加磁贴并保存仪表板。

此时，仪表板视图将显示新仪表板。 选择仪表板名称旁的箭头，以查看可供你使用的仪表板。 此列表可能包括其他用户已创建和共享的仪表板。

## <a name="edit-a-dashboard"></a>编辑仪表板

现在，让我们编辑仪表板以添加、调整和排列代表你的 Azure 资源的磁贴。

### <a name="add-tiles-from-the-tile-gallery"></a>从磁贴库添加磁贴

若要将磁贴添加到仪表板，请执行以下步骤：

1. 从仪表板的页眉选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”。

    ![突出显示“编辑”选项的仪表板的屏幕截图。](./media/azure-portal-dashboards/dashboard-edit.png)

1. 浏览“磁贴库”，或使用搜索字段查找某个磁贴。 选择要添加到仪表板的磁贴。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="库的屏幕截图。":::

1. 选择“添加”，以使用默认大小和位置将磁贴添加到仪表板中。  或者将磁贴拖到网格中，并将其放到所需的位置。 添加所需的任何磁贴；下面是几点思路：

    - 添加“所有资源”可查看所有已创建的资源。

    - 如果你在多家组织中工作，可将“组织标识”磁贴添加到仪表板，以明确显示资源所属的组织。 

1. 如果需要，请通过拖放磁贴右下角来调整磁贴的大小。

1. 要保存更改，请在页眉中选择“保存”。 通过在页眉中选择“预览”还可以在不保存的情况下预览更改。 在预览屏幕上，可以选择“保存”以保留更改，选择“放弃”以删除更改，也可以选择“编辑”以返回到编辑选项并进行进一步更改  。

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="“预览”、“保存”和“放弃”选项的屏幕截图。":::

### <a name="pin-content-from-a-resource-page"></a>固定资源页中的内容

将磁贴添加到仪表板的另一种方法是直接从资源页添加。

许多资源页在命令栏都包含固定图标。 如果选择此图标，则可以将表示源页的磁贴固定到现有仪表板或创建的新仪表板。

![包含固定图标的页面命令栏屏幕截图](./media/azure-portal-dashboards/dashboard-pin-blade.png)

在某些情况下，固定图标也可能会显示在页面中的特定内容旁边，这意味着可以固定该特定内容的磁贴，而不是整个页面。

### <a name="resize-or-rearrange-tiles"></a>调整或重新排列磁贴

若要在仪表板上更改磁贴大小或重新排列磁贴，请执行以下步骤：

1. 从页头中选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”。 

1. 选择磁贴右上角的上下文菜单。 然后选择磁贴大小。 支持任何大小的磁贴还会在右下角提供“控点”，用于拖动调整磁贴的大小。

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="磁贴大小菜单已打开的仪表板的屏幕截图。":::

1. 选择一个磁贴，并将其拖到网格上的新位置以排列仪表板。

### <a name="additional-tile-configuration"></a>其他磁贴配置

某些磁贴可能需要额外配置才能显示所需的信息。 例如，“指标图表”磁贴必须经过设置才能显示 Azure Monitor 中的指标。 还可以自定义磁贴数据以替代仪表板的默认时间设置。

需要设置的任何磁贴在自定义它之前会显示一个横幅。 对于“指标图表”，横幅为“在指标中编辑”。若要自定义磁贴，请执行以下操作 ：

1. 在页标题中，选择“保存”退出编辑模式。

1. 请选择横幅，然后执行所需的设置。

    ![需要配置的磁贴的屏幕截图](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> 使用 markdown 磁贴可以在仪表板上显示自定义的静态内容。 这可能是基本说明、一幅图像、一组超链接甚至联系信息。 有关使用 Markdown 磁贴的详细信息，请参阅[在 Azure 仪表板上使用 Markdown 磁贴显示自定义内容](azure-portal-markdown-tile.md)。

### <a name="customize-tile-data"></a>自定义磁贴数据

仪表板上的数据自动显示过去 24 小时的活动。 若要仅显示此磁贴的不同时间跨度，请执行以下步骤：

1. 从上下文菜单中选择“自定义磁贴数据”，或者从磁贴左上角选择![筛选器图标](./media/azure-portal-dashboards/dashboard-filter.png)筛选器。

    ![磁贴上下文菜单的屏幕截图。](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. 选中“在磁贴级别替代仪表板时间设置”对应的复选框。 

    ![用于配置磁贴时间设置的对话框屏幕截图。](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 选择要为此磁贴显示的时间跨度。 可以选择过去 30 分钟到过去 30 天的时间，或者自定义范围。

1. 选择要显示的时间粒度。 可以使用 1 分钟到 1 个月的增量作为显示粒度。

1. 选择“应用”。 

## <a name="delete-a-tile"></a>删除磁贴

要从仪表板中删除磁贴，请执行以下操作之一：

- 选择磁贴右上角的上下文菜单，然后选择“从仪表板中删除”。 

- 选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)“编辑”进入自定义模式。  将鼠标悬停在磁贴的右上角，然后选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)删除图标从仪表板中删除该磁贴。

   ![演示如何从仪表板中删除磁贴的屏幕截图。](./media/azure-portal-dashboards/dashboard-delete-tile.png)

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

        ![仪表板选择菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-browse.png)

    1. 在“类型”字段中，选择“共享的仪表板”。  

        ![所有仪表板选择菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 选择一个或多个订阅。 还可以输入文本以按名称筛选仪表板。

    1. 从共享仪表板列表中选择一个仪表板。

## <a name="delete-a-dashboard"></a>删除仪表板

若要永久删除专用或共享仪表板，请执行以下步骤：

1. 从仪表板名称旁的列表中选择要删除的仪表板。

1. 从页头中选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)“删除”。 

1. 对于专用仪表板，请在确认对话框中选择“确定”以删除仪表板。  对于共享仪表板，请在确认对话框中选中相应的复选框，以确认发布的仪表板不再可供其他人查看。 选择“确定”。 

    ![确认删除操作的屏幕截图。](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>恢复已删除的仪表板

如果你在 Azure 全球云中操作，并在 Azure 门户中删除了已发布的仪表板，可以在删除后的 14 天内恢复该仪表板。 有关详细信息，请参阅[在 Azure 门户中恢复已删除的仪表板](recover-shared-deleted-dashboard.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 基于角色的访问控制共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
- [以编程方式创建 Azure 仪表板](azure-portal-dashboards-create-programmatically.md)
