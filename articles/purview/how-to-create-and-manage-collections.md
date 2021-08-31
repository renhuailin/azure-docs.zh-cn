---
title: 如何创建和管理集合
description: 本文介绍如何在 Azure Purview 中创建和管理集合。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a5d0935955e10a1d520c88863af6a16b7160bc40
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398030"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>在 Azure Purview 中创建和管理集合

Purview 中的集合可用于按业务流程组织资产和源，但它们也是在整个 Purview 中用于管理访问权限的工具。 本指南将指导你创建和管理这些集合，并介绍注册源以及将资产添加到集合的步骤。

> [!NOTE]
> 目前，本快速入门仅适用于在 8 月 18 日或之后创建的 Purview 实例。 在 8 月 18 日之前创建的实例可以创建集合，但不会通过这些集合管理权限。 有关为 8 月 18 日之前创建的 Purview 实例创建集合的信息，请参阅页面底部的[旧版集合指南](#legacy-collection-guide)。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 一个有效的 [Purview 资源](create-catalog-portal.md)。

### <a name="check-permissions"></a>检查权限

若要在 Purview 中创建和管理集合，你需要成为 Purview 中的集合管理员。 可以在 [Purview Studio](use-purview-studio.md) 中检查这些权限。 在 Azure 门户中转到你的 Purview 资源，然后在概述页上选择“打开 Purview Studio”磁贴即可找到 Purview Studio。

1. 在左侧窗格中选择“数据映射”>“集合”打开集合管理页。
:::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::
1. 选择你的根集合。 这是你的集合列表中的顶级集合，与你的 Purview 资源同名。 在以下示例中，该集合名为 Contoso Purview。 或者，如果集合已经存在，则你可以选择要在其中创建子集合的任何集合。
:::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了根集合。" border="true":::
1. 在集合窗口中选择“角色分配”。
:::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“角色分配”选项卡。" border="true":::
1. 若要创建集合，需要在“角色分配”下的“集合管理员”列表中操作。 如果已创建 Purview 资源，则应该已将你列为根集合下的集合管理员。 否则，需要联系集合管理员为你授予权限。
:::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“集合管理员”部分。" border="true":::

## <a name="collection-management"></a>集合管理

### <a name="create-a-collection"></a>创建集合

需要成为集合管理员才能创建集合。 如果你不确定自己是否为集合管理员，请按照[上述指南](#check-permissions)检查权限。
 
1. 在左侧窗格中选择“数据映射”>“集合”打开集合管理页。
:::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::
1. 选择“+ 添加集合”。 同样，请注意只有[集合管理员](#check-permissions)能够管理集合。
1. 在右侧面板中，输入集合名称和说明。 如果需要，还可以添加用户或组作为新集合的集合管理员。
:::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了“新建集合”窗口，选择了显示名称和集合管理员，并突出显示了“创建”按钮。" border="true":::
1. 选择“创建”。 新集合的信息将反映在页面上。
:::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了新建的集合的窗口。" border="true":::

### <a name="edit-a-collection"></a>编辑集合

1. 在集合详细信息页或者集合的下拉菜单中选择“编辑”。
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中打开了集合窗口，所选集合窗口中以及集合名称旁边的省略号按钮下面突出显示了“编辑”按钮。" border="true":::
1. 当前可以编辑集合说明和集合管理员。 进行任何更改，然后选择“保存”以保存更改。
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Purview Studio 窗口的屏幕截图，其中打开了“编辑集合”窗口，已添加集合的说明，并突出显示了“保存”按钮。" border="true":::

### <a name="view-collections"></a>查看集合

1. 选择集合名称旁边的三角形图标展开或折叠集合层次结构。 单击集合名称进行导航。
1. 在列表顶部的筛选器框中键入内容以筛选集合。
1. 在根集合的上下文菜单中选择“刷新”以重载集合列表。
:::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“资源名称”旁边的按钮，并突出显示了刷新按钮。" border="true":::
1. 在集合详细信息页中选择“刷新”以重载单个集合。
:::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中突出显示了集合窗口下的刷新按钮。" border="true":::

## <a name="add-roles-and-restrict-access-through-collections"></a>通过集合添加角色和限制访问权限

由于权限是通过 Purview 中的集合进行管理的，因此，了解角色及其为用户授予的权限非常重要。 在某个集合中被授予权限的用户有权访问与该集合关联的源和资产，并会向子集合继承权限。 [可以限制](#restrict-inheritance)继承，但默认情况下允许继承。

以下指南介绍角色、如何对其进行管理以及权限继承。

### <a name="roles"></a>角色

分配的所有角色适用于应用了角色的集合中的源、资产和其他对象。

- 集合管理员 - 可以编辑集合及其详细信息，并可以添加子集合。 他们还可以在集合范围添加数据管护者、数据读取者和其他 Purview 角色。 无法删除从父集合自动继承的集合管理员。
- 数据源管理员 - 可以管理数据源和数据扫描。
- 数据管护者 - 可以对目录数据对象执行创建、读取、修改和删除操作，以及在对象之间建立关系。
- 数据读取者 - 可以访问（但不能修改）目录数据对象。

### <a name="add-role-assignments"></a>添加角色分配

1. 选择“角色分配”选项卡以查看集合中的所有角色。 只有集合管理员能够管理角色分配。
:::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中突出显示了“角色分配”选项卡。" border="true":::
1. 选择“编辑角色分配”或人像图标以编辑每个角色成员。
:::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“编辑角色分配”下拉列表。" border="true":::
1. 在文本框中键入内容，以搜索要添加到角色成员的用户。 选择“X”删除你不想要添加的成员。
1. 单击“确定”保存更改，然后你将看到新用户已反映在“角色分配”列表中。

### <a name="remove-role-assignments"></a>删除角色分配

1. 单击用户名旁边的“X”按钮删除角色分配。
:::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“角色分配”选项卡，并突出显示了某个名称旁边的“X”按钮。" border="true":::
1. 如果你确定要删除该用户，请选择“确认”。
:::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="确认弹出窗口的屏幕截图，其中突出显示了“确认”按钮。" border="true":::

### <a name="restrict-inheritance"></a>限制继承

集合权限是从父集合自动继承的。 例如，对根集合（位于列表中最前面的集合，与你的 Purview 资源同名）的所有权限将由其下面的所有集合继承。 随时可以使用“限制继承的权限”选项来限制从父集合继承。

限制继承后，需要直接将用户添加到受限制的集合才能为其授予访问权限。

1. 导航到要在其中限制继承的集合，然后选择“角色分配”选项卡。
1. 选择“限制继承的权限”并在弹出对话框中单击“限制访问权限”，以从此集合和所有子集合中删除继承的权限。 请注意，集合管理员权限不受影响。
:::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“角色分配”选项卡，并突出显示了“限制继承的权限”滑动按钮。" border="true":::
1. 限制后，继承的成员将从集合管理员所需的角色中删除。
1. 再次单击“限制继承的权限”切换按钮以恢复设置。
:::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“角色分配”选项卡，并突出显示了“取消限制继承的权限”滑动按钮。" border="true":::

## <a name="add-assets-to-collections"></a>将资产添加到集合

资产和源也与集合关联。 在扫描过程中，如果扫描已与某个集合关联，则资产将自动添加到该集合，但也可以手动添加。

1. 在资产详细信息中检查集合信息。 可以在资产详细信息页右上角的“集合路径”部分找到集合信息。
:::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="Purview Studio 资产窗口的屏幕截图，其中突出显示了“集合路径”。" border="true":::
1. 资产详细信息页中的权限：
    1. 请按照[前面有关添加角色以及限制对集合的访问权限的指南](#add-roles-and-restrict-access-through-collections)检查基于集合的权限模型。
    1. 如果你对某个集合拥有读取权限，则搜索结果中不会列出该集合下的资产。 如果你获取了某个资产的直接 URL 并将其打开，将会看到“无访问权限”页。 在这种情况下，请联系 Purview 管理员为你授予访问权限。 可以再次单击“刷新”按钮来检查权限。
    :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Purview Studio 资产窗口的屏幕截图，其中的用户没有权限，并且无权访问信息或选项。" border="true":::
    1. 如果你对一个集合拥有读取权限但没有写入权限，则可以浏览资产详细信息页，但以下操作将被禁用：
        - 编辑资产。 将禁用“编辑”按钮。
        - 删除资产。 将禁用“删除”按钮。
        - 将资产移到另一个集合。 将隐藏“集合路径”部分右上角的“...”按钮。
    :::image type="content" source="./media/how-to-create-and-manage-collections/read-access-only.png" alt-text="Purview Studio 资产窗口的屏幕截图，其中的用户只拥有读取权限，对选项只拥有部分访问权限。" border="true":::
    1. “层次结构”部分中的资产也受权限的影响。 没有读取权限的资产将会灰显。
    :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Purview Studio 层次结构窗口的屏幕截图，其中的用户只拥有读取权限，并且无权访问选项。" border="true":::

### <a name="move-asset-to-another-collection"></a>将资产移到另一个集合

1. 单击“集合路径”部分右上角的“...”按钮。
:::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Purview Studio 资产窗口的屏幕截图，其中已突出显示“集合路径”，并选择了“集合路径”旁边的省略号按钮。" border="true":::
1. 单击“移到另一集合”按钮。
1. 在右侧面板中，选择要移到的目标集合。 请注意，你只能看到你在其中拥有写入权限的集合。
:::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="Purview Studio 弹出窗口的屏幕截图，其中突出显示了“选择集合”下拉菜单。" border="true":::
1. 单击窗口底部的“移动”按钮以移动资产。

## <a name="search-and-browse-by-collections"></a>按集合进行搜索和浏览

### <a name="search-by-collection"></a>按集合进行搜索

在 Azure Purview 中，搜索栏位于 Purview 工作室 UX 的顶部。

:::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="屏幕截图显示 Azure Purview 搜索栏位置" border="true":::

单击搜索栏时，可以看到近期搜索历史记录和最近访问的资产。 选择“查看全部”以查看最近查看的所有资产。

:::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="屏幕截图显示在输入任何关键字之前的搜索栏" border="true":::

输入可帮助标识资产的关键字，如名称、数据类型、分类和术语表术语。 输入与所需资产相关的关键字时，Azure Purview 会显示有关搜索内容和可能的资产匹配项的建议。 若要完成搜索，请单击“查看搜索结果”或按“Enter”。

:::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="屏幕截图显示用户输入关键字时的搜索栏" border="true":::

搜索结果页将按相关性顺序显示与提供的关键字匹配的资产列表。 有多种因素可能会影响资产的相关性分数。 可以选择特定的集合、数据存储、分类、联系人、标签以及适用于所要查找的资产的术语表术语，来进一步筛选列表。

:::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="显示搜索结果的屏幕截图" border="true":::

 单击所需资产以查看该资产的详细信息页，可在其中查看包括架构、世系和资产所有者在内的属性。

:::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="该屏幕截图显示集合搜索结果。" border="true":::

### <a name="browse-by-collection"></a>按集合进行浏览

1. 可以在主页上选择 `Browse assets` 来浏览数据资产。
:::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="Purview Studio 目录窗口的屏幕截图，其中突出显示了“浏览资产”按钮。" border="true":::
1. 在“浏览资产”页上，选择 `By collection` 透视表。 集合将以分层表视图的形式列出。 若要进一步浏览每个集合中的资产，请单击相应的集合名称。
:::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="Purview Studio 资产窗口的屏幕截图，其中选择了“按集合”选项卡。"border="true":::
1. 在下一页上，将显示所选集合下的资产的搜索结果。 可以选择 facet 筛选器来缩小结果范围。 或者，可以单击子集合/相关集合名称来查看其他集合下的资产 :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="Purview Studio 目录窗口的屏幕截图，其中选择了“按集合”选项卡。"border="true":::
1. 若要查看某个资产的详细信息，请在搜索结果中单击相应的资产名称。 或者，可以检查资产并对其进行批量编辑。
:::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Purview Studio 目录窗口的屏幕截图，其中选择了“按集合”选项卡，并突出显示了资产复选框。"border="true":::

## <a name="register-source-to-a-collection"></a>将源注册到集合

1. 选择集合节点上的“注册”或注册图标以注册数据源。 请注意，只有数据源管理员能够注册源。
:::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="Purview Studio 数据映射窗口的屏幕截图，页面顶部以及集合下面突出显示了注册按钮。"border="true":::
1. 填写数据源名称和其他源信息。  窗体底部列出了你对其拥有扫描权限的所有集合。 可以选择一个集合。 此源下的所有资产都将属于所选的集合。
:::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="源注册窗口的屏幕截图。"border="true":::
1. 创建的数据源将放在所选的集合下。 单击“查看详细信息”以查看数据源。
:::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="Purview Studio 数据映射窗口的屏幕截图，其中突出显示了新添加的源卡。"border="true":::
1. 选择“新建扫描”以在数据源下创建扫描。
:::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="Purview Studio 源窗口的屏幕截图，其中突出显示了“新建扫描”按钮。"border="true":::
1. 同样，可在窗体底部选择一个集合，扫描的所有资产都将包含在该集合中。 请注意，此处列出的集合限制为数据源集合的子集合。 
:::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="新建扫描窗口的屏幕截图，其中突出显示了集合下拉列表。"border="true":::
1. 返回到该集合，你将看到有一个数据源已链接到该集合。
:::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="Purview Studio 数据映射窗口的屏幕截图，映射中突出显示了新添加的源卡。"border="true":::

## <a name="legacy-collection-guide"></a>旧版集合指南

> [!NOTE]
> 本旧版集合指南仅适用于在 8 月 18 日之前创建的 Purview 实例。 在该时间之后创建的实例应遵循前面的指南。

旧版集合只会在数据映射中组织源，且不管理这些源的权限。

### <a name="create-a-legacy-collection"></a>创建旧版集合

1. 在左侧窗格中选择“数据映射”打开数据映射。 使用映射视图可以查看集合及其下面列出的源。
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-view.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”。" border="true":::
1. 选择“+ 新建集合”。
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-create.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“+ 新建集合”。" border="true":::
1. 为集合命名，然后选择父级或“无”。 选择“创建”。 集合信息将反映在数据映射中。
:::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-name.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了“新建集合”窗口。" border="true":::

## <a name="next-steps"></a>后续步骤
创建集合后，可以遵循以下指南添加资源和扫描以及管理集合。

- [管理数据源](manage-data-sources.md)
- [支持的数据源](purview-connector-overview.md)
