---
title: Azure Purview 帐户升级信息
description: 2021 年 8 月 18 日之前创建的 Azure Purview 帐户将自动升级到最新版本的 Purview。 本文介绍将更改的内容以及所需的任何后续步骤。
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: a2953402d95822e8392bf8a0b607d8427e03cb88
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481395"
---
# <a name="azure-purview-account-upgrade-information"></a>Azure Purview 帐户升级信息

Azure Purview 在 2021 年 8 月 18 日发布了一些新功能，包括弹性数据映射、更新的集合和更新的访问控制。 2021 年 8 月 18 日之后创建的 Azure Purview 帐户在创建时已经具备所有这些新功能。

2021 年 8 月 18 日之前创建的帐户（旧帐户）将自动升级以包含这些功能。

如果你在 2021 年 8 月 18 日之前创建了 Purview 帐户，那么在你的 Purview 帐户升级时，你将收到电子邮件通知。

本文档介绍升级帐户后会看到的更改，以及使用新功能需要执行的任何步骤。

## <a name="elastic-data-map"></a>弹性数据映射

升级后的 Azure Purview 帐户使用新的数据映射系统，它根据使用情况进行动态缩放，这称为“弹性数据映射”。 弹性数据映射从一个容量单位开始，并根据需要增减容量单位。
这项新功能不会影响与 Purview 直接交互的方式，但会影响计费。 它根据你管理数据状况所需的大小进行缩放，你只需为使用的内容付费。

若要详细了解新的弹性数据映射及其计费方式，请参阅[弹性数据映射页](concept-elastic-data-map.md)。

若要了解所有 Purview 的计费详情，请参阅[定价计算器](https://azure.microsoft.com/pricing/details/azure-purview/)。

帐户升级后，无需进行任何更改即可使用弹性数据映射。 它会自动启用。

## <a name="collections"></a>集合

集合存在于旧的 Purview 帐户中，但在升级后的 Purview 帐户中具有新的功能，并以不同的方式进行管理。

旧集合是在 Purview 帐户中整理数据源和项目的一种方式。 集合仍用于自定义 Purview 数据映射来匹配业务环境，但它们现在也包括访问控制。 通过集合，你的访问管理体验将与数据映射相匹配，而不是在数据映射外大致控制访问。

利用集合可对数据源进行精细控制，但也可对可发现性进行精细控制。 用户将只会看到自己有权访问的集合中的资产，因此只会看到其所需的信息。

Purview 帐户升级后，集合也将进行更新。 所有当前资产都将迁移到这些新集合中。 在下面的部分中，将介绍可在哪里找到集合和现有资产。

### <a name="locate-and-manage-collections"></a>查找和管理集合

为了查找新集合，我们将从 [Purview Studio](use-purview-studio.md) 开始。 在 [Azure 门户](https://portal.azure.com)中转到 Purview 资源，然后在概述页面选择“打开 Purview Studio”磁贴，即可找到 Purview Studio。

在左侧窗格中选择“数据映射”>“集合”，打开集合管理页。

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::

在这里，你将看到根集合以及所有现有集合。 根集合是集合列表中的顶级集合，与 Purview 资源同名。 在以下示例中，该集合名为 Contoso Purview。

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了根集合。" border="true":::

所有现有集合都已添加到此根集合，并可在此页面进行管理。
若要创建新集合，请选择“+ 添加集合”。

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="Purview 工作室窗口的屏幕截图，显示了新的集合窗口，其中突出显示了“添加集合”按钮。" border="true":::

若要编辑集合，请在集合详细信息页或集合的下拉菜单中选择“编辑”。

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中打开了集合窗口，所选集合窗口中以及集合名称旁边的省略号按钮下面突出显示了“编辑”按钮。" border="true":::

若要编辑集合中的角色分配，请选择“角色分配”选项卡，查看集合中的所有角色。 只有集合管理员能够管理角色分配。 若要详细了解已升级的帐户中权限，可查看[下述部分](#permissions)。

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中突出显示了“角色分配”选项卡。" border="true":::

若要详细了解已升级的帐户中的集合，请阅读[创建和管理指南](how-to-create-and-manage-collections.md)。

### <a name="what-happens-to-your-collections-during-upgrade"></a>集合在升级期间的变化

1. 会创建一个根集合。 根集合是集合列表中的顶级集合，与 Purview 资源同名。 在以下示例中，该集合名为 Contoso Purview。

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了根集合。" border="true":::

1. 之前存在的集合将连接到该根集合。 你将看到根集合下列出这些集合，并且可在此处与它们交互。

### <a name="what-happens-to-your-sources-during-upgrade"></a>源在升级期间的变化

1. 之前未与集合关联的源都会自动添加到根集合中。

1. 之前与集合关联的源或扫描都将添加到已升级的帐户中的相应集合中。

资产也与已升级的帐户中的集合相关联，但升级后的集合下可能不会立即显示它们。 原因有两个：

* 资产由计划扫描进行扫描，而扫描的下一次运行还没有发生。
* 资产仅由一次性扫描进行了扫描。

> [!NOTE]
> 资产会在扫描期间添加到一个集合中，因此需要对资产运行另一次扫描才能将其添加到这些新集合中。

对于计划扫描，只需等待这些扫描的下一次运行，到时资产会添加到集合中。

对于一次性扫描，需要手动重新运行这些扫描，在集合中填充资产。

1. 在 Purview Studio 中，打开“数据映射”，然后选择“源”。 选择要扫描的源。

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了源。" border="true":::

1. 选择“扫描”选项卡，然后选择要重新运行的扫描。

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开一个源并突出显示了扫描。" border="true":::

1. 选择“立即运行扫描”，来再次运行扫描。

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开一个扫描并突出显示了“立即运行扫描”。" border="true":::

## <a name="permissions"></a>权限

在已升级的 Purview 帐户中，权限通过集合进行管理。

> [!NOTE]
> 已升级帐户中的权限不再通过访问控制 (IAM) 进行管理。 升级旧帐户后，预先存在的 IAM 权限仍将在访问控制 IAM 下显示，但不会再影响 Purview 中的访问。

升级旧帐户后，访问控制 IAM 中分配的所有权限都会分配给根集合的以下角色之一。

* **集合管理员** - 可编辑集合及其详细信息，管理集合中的访问权限以及添加子集合。
* 数据源管理员 - 可以管理数据源和数据扫描。
* **数据管护者** - 可以对目录数据对象执行创建、读取、修改和删除操作。
* 数据读取者 - 可以访问（但不能修改）目录数据对象。

若要向用户授予对 Purview 中源和项目的访问权限，你需要向用户授予对可能需要访问数据的集合和子集合的访问权限。

> [!NOTE]
> 只有集合管理员才有权管理集合中的访问权限。

若要查看或编辑集合中的角色分配，请在集合中选择“角色分配”选项卡。

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Purview Studio 集合窗口的屏幕截图，其中突出显示了“角色分配”选项卡。" border="true":::

若要详细了解如何在已升级的 Purview 帐户中管理权限，请遵循 [Purview 权限指南](catalog-permissions.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解上述概念，或者了解如何保护 Purview 资源，请点击下面的链接。

* [对 Azure Purview 使用专用终结点](catalog-private-link.md)
* [弹性数据映射](concept-elastic-data-map.md)
* [Purview 权限指南](catalog-permissions.md)
