---
title: 快速入门：创建集合
description: 本文介绍如何在 Azure Purview 中创建集合并添加权限和源
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 08/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: a1a62bb6253aa2788d8dad41d506ca898a049283
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475685"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>快速入门：在 Purview 中创建集合并分配权限

> [!NOTE]
> 本快速入门目前仅适用于在 2021 年 8 月 18 日或之后创建的 Purview 实例。 在 8 月 18 日之前创建的实例可以创建集合，但不会通过这些集合管理权限。 有关为 8 月 18 日之前创建的 Purview 实例创建集合的信息，请参阅页面底部的[旧版集合指南](#legacy-collection-guide)。
> 
> 在接下来的几周内，所有旧帐户都会被自动升级。 升级 Purview 帐户时，你将收到电子邮件通知。 有关升级帐户后将发生的更改的信息，请参阅[升级帐户指南](concept-account-upgrade.md)。

集合是 Purview 的一种工具，用于跨资产、源和信息管理所有权和访问控制。 它们还会将源和资产整理到为了匹配你的数据管理经验而自定义的类别中。 本指南说明如何设置第一个集合和集合管理员，从而为组织准备好 Purview 环境。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 一个有效的 [Purview 资源](create-catalog-portal.md)。

## <a name="check-permissions"></a>检查权限

若要在 Purview 中创建和管理集合，你需要成为 Purview 中的集合管理员。 可以在 [Purview Studio](use-purview-studio.md) 中检查这些权限。 在 [Azure 门户](https://portal.azure.com)中转到 Purview 资源，然后在概述页面选择“打开 Purview Studio”磁贴，即可找到 Purview Studio。

1. 在左侧窗格中选择“数据映射”>“集合”打开集合管理页。

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::

1. 选择你的根集合。 这是你的集合列表中的顶级集合，与你的 Purview 资源同名。 在以下示例中，该集合名为 Contoso Purview。

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了根集合。" border="true":::

1. 在集合窗口中选择“角色分配”。

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“角色分配”选项卡。" border="true":::

1. 若要创建集合，需要在“角色分配”下的“集合管理员”列表中操作。 如果已创建 Purview 资源，则应该已将你列为根集合下的集合管理员。 否则，你需要联系集合管理员为你授予权限。

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“集合管理员”部分。" border="true":::

## <a name="create-a-collection-in-the-portal"></a>在门户中创建集合

若要创建集合，我们将从 [Purview Studio](use-purview-studio.md) 开始。 在 Azure 门户中转到 Purview 资源，然后在概述页面选择“打开 Purview Studio”磁贴，即可找到 Purview Studio。

1. 在左侧窗格中选择“数据映射”>“集合”打开集合管理页。

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::

1. 选择“+ 添加集合”。
1. 在右侧面板中，输入集合的名称和描述，然后搜索要添加为集合管理员的用户。

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了“新建集合”窗口，选择了显示名称和集合管理员，并突出显示了“创建”按钮。" border="true":::

1. 选择“创建”。 集合信息将反映在页面上。

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了新建的集合的窗口。" border="true":::

## <a name="assign-permissions-to-collection"></a>向集合分配权限

现在你已有一个集合，可向它分配权限来管理对 Purview 的用户访问权限。

### <a name="roles"></a>角色

分配的所有角色适用于应用了角色的集合中的源、资产和其他对象。

* **集合管理员** - 可编辑集合及其详细信息，管理集合中的访问权限以及添加子集合。
* 数据源管理员 - 可以管理数据源和数据扫描。
* **数据管护者** - 可以对目录数据对象执行创建、读取、修改和删除操作。
* 数据读取者 - 可以访问（但不能修改）目录数据对象。

### <a name="assign-permissions"></a>分配权限

1. 选择“角色分配”选项卡以查看集合中的所有角色。

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中突出显示了“角色分配”选项卡。" border="true":::

1. 选择“编辑角色分配”或人像图标以编辑每个角色成员。

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中选择了“编辑角色分配”下拉列表。" border="true":::

1. 在文本框中键入内容，以搜索要添加到角色成员的用户。 选择“确定”，保存更改。

## <a name="legacy-collection-guide"></a>旧版集合指南

> [!NOTE]
> 本旧版集合指南仅适用于在 2021 年 8 月 18 日之前创建的 Purview 实例。 在该时间之后创建的实例应遵循前面的指南。

### <a name="create-a-legacy-collection"></a>创建旧版集合

1. 在左侧窗格中选择“数据映射”打开数据映射。 使用映射视图可以查看集合及其下面列出的源。

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-view.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”。" border="true":::

1. 选择“+ 新建集合”。

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-create.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“+ 新建集合”。" border="true":::

1. 为集合命名，然后选择父级或“无”。 选择“创建”。 集合信息将反映在数据映射中。

    :::image type="content" source="./media/quickstart-create-collection/legacy-collection-name.png" alt-text="Purview Studio 中“新建集合”弹出窗口的屏幕截图。" border="true":::

## <a name="next-steps"></a>后续步骤

创建集合后，可以遵循以下指南添加资源和扫描以及管理集合。

* [将源注册到集合](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [通过集合进行访问管理](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)
