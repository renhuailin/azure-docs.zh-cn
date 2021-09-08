---
title: 目录权限（预览版）
description: 本文概述了如何在 Azure Purview 中配置基于角色的访问控制 (RBAC)
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: 06ed84bf63f79087efef33b1061e21b61315e78e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969445"
---
# <a name="access-control-in-azure-purview"></a>Azure Purview 中的访问控制

Azure Purview 使用集合来组织和管理对其源、资产和其他项目的访问。 本文介绍 Azure Purview 帐户中的集合与访问管理。

> [!NOTE]
> 目前，本文中的信息仅适用于在 2021 年 8 月 18 日当天或之后创建的 Purview 帐户。 在 8 月 18 日之前创建的实例可以创建集合，但不会通过这些集合管理权限。 有关对 8 月 18 日之前创建的 Purview 实例进行访问控制的信息，请参阅页面底部的[旧版权限指南](#legacy-permission-guide)。
>
> 所有旧帐户都将在几周内自动升级。 升级 Purview 帐户时，你将收到电子邮件通知。 升级帐户时，所有分配的权限将自动重新部署到根集合。 此时，应通过集合而不是访问控制 (IAM) 来管理权限。 IAM 权限将不再适用于 Purview 项目。

## <a name="collections"></a>集合

集合是 Azure Purview 用来将资产、源和其他项目分组到层次结构（以使其可供发现）以及用来管理访问控制的一种工具。 对 Purview 资源的所有访问都是从 Purview 帐户本身内部的集合进行管理的。

## <a name="roles"></a>角色

Azure Purview 使用一组预定义的角色来控制谁可以访问帐户中的哪些内容。 这些角色目前是：

- 集合管理员 - 可以编辑 Purview 集合及其详细信息，并可以添加子集合。 他们还可以将用户添加到他们充当管理员的集合中的其他 Purview 角色。
- 数据源管理员 - 可以管理数据源和数据扫描。
- 数据管护者 - 可以创建、读取、修改和删除目录数据资产，以及在资产之间建立关系。
- 数据读取者 - 可以访问（但不能修改）目录数据资产。

## <a name="who-should-be-assigned-to-what-role"></a>应将用户分配到哪个角色？

|使用方案|合适的角色|
|-------------|-----------------|
|我只需要查找资产，不需要编辑任何内容|数据读取者|
|我需要编辑有关资产的信息、分配分类、将其与术语表条目相关联，等等。|数据管护者|
|我需要编辑术语表或设置新的分类定义|数据管护者|
|我的应用程序服务主体需要将数据推送到 Azure Purview|数据管护者|
|我需要通过 Purview Studio 设置扫描|数据源管理员，以及至少是源已注册到的集合的数据读取者或数据管护者。|
|我需要启用服务主体或组，以便在 Azure Purview 中设置和监视扫描，而无需允许它们访问目录信息 |数据源管理员|
|我需要将用户分配到 Azure Purview 角色中 | 集合管理员 |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="显示 Purview 角色的图表" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>了解如何使用 Azure Purview 的角色和集合

所有访问控制将在 Purview 的集合中进行管理。 可以在 [Purview Studio](use-purview-studio.md) 中找到 Purview 的集合。 在 [Azure 门户](https://portal.azure.com)中打开你的 Purview 帐户，然后在“概述”页上选择“Purview Studio”磁贴。 从该位置导航到左侧菜单中的“数据映射”，然后选择“集合”选项卡。

创建 Azure Purview 帐户后，它最初包含一个与 Purview 帐户本身同名的根集合。 Purview 帐户的创建者将自动添加为此根集合的集合管理员、数据源管理员、数据管护者和数据读取者，可以编辑和管理此集合。

源、资产和对象可以直接添加到此根集合，但其他集合也可以添加到此根集合。 添加集合可以更好地控制谁有权访问 Purview 帐户中的数据。

对于所有其他用户，只有在为他们或者他们所在的组分配了上述角色之一后，他们才能访问 Azure Purview 帐户中的信息。 这意味着，如果 Azure Purview 帐户是由你创建的，则在将其他人[添加到集合中上述一个或多个角色](how-to-create-and-manage-collections.md#add-role-assignments)之前，只有创建者（你）才能访问或使用该帐户的 API。

只能由集合管理员或通过权限继承将用户添加到集合。 父集合的权限由其子集合自动继承。 但是，你可以选择在任一集合上[限制权限继承](how-to-create-and-manage-collections.md#restrict-inheritance)。 如果执行此操作，该集合的子集合将不再从父级继承权限，并且将需要直接添加，不过，无法删除从父集合自动继承的集合管理员。

## <a name="assign-permissions-to-your-users"></a>为用户分配权限

创建 Azure Purview 帐户后，首先要做的事是创建集合，并将用户分配到这些集合中的角色。

### <a name="create-collections"></a>创建集合

可以根据 Purview 帐户中的源结构自定义集合，集合还可为这些资源充当组织有序的存储箱。 考虑可能需要哪些集合时，请考虑用户访问或发现信息的方式。 源是否按部门细分？ 在这些部门中，是否存在只需发现某些资产的专业组？ 是否有某些源必须可供所有用户发现？

可以根据这些提示来确定需要使用哪些集合和子集合来最有效地组织数据映射。

新集合可以直接添加到数据映射（在其中可从下拉列表选择其父集合），也可以作为子集合从父集合中进行添加。 在数据映射视图中，可以看到按集合排序的所有源和资产；在列表中，列出了源的集合。

有关详细说明和信息，请参阅[有关创建和管理集合的指南](how-to-create-and-manage-collections.md)。

#### <a name="a-collections-example"></a>集合示例

对集合、权限及其工作原理有了基本的了解后，接下来让我们看一个示例。

:::image type="content" source="./media/catalog-permissions/collection-example.png" alt-text="该图表显示按区域和部门细分的示例集合层次结构。" border="true":::

这是组织构建其数据的一种方式：从根集合（在此示例中为 Contoso）开始，集合组织到区域，然后组织到部门和分部。 可将数据源和资产添加到其中的任一集合，以便按这些区域和部门组织数据资源，并沿着这些线路管理访问控制。 有一个分部（“营收”）实施严格的访问准则，因此需要严格管理权限。

[数据读取者角色](#roles)可以访问目录中的信息，但不能对其进行管理或编辑。 因此对于上面的示例，将“数据读取者”权限添加到根集合上的某个组并允许继承，将为该组中的所有用户授予对 Purview 源和资产的组读取者权限。 这样，该组中的每个人都可以发现这些资源，但不能对其进行编辑。 在“营收”组上[限制继承](how-to-create-and-manage-collections.md#restrict-inheritance)可以控制对这些资产的访问。 需要访问营收信息的用户可以单独添加到“营收”集合。
同样，对于“数据管护者”和“数据源管理员”角色，这些组的权限最初是在他们所分配到的集合级别授予的，并传播到未限制继承的子集合。 下面在“美洲”子集合中的集合级别为多个组分配了权限。

:::image type="content" source="./media/catalog-permissions/collection-permissions-example.png" alt-text="该图表显示按区域和部门细分的示例集合层次结构，并显示权限分布。" border="true":::

### <a name="add-users-to-roles"></a>将用户添加到角色

角色分配是通过集合管理的。 只有拥有[集合管理员角色](#roles)的用户能够向该集合上的其他用户授予权限。 需要添加新权限时，集合管理员需访问 Purview Studio，依次导航到“数据映射”、“集合”选项卡，然后选择需要将用户添加到的集合。 在“角色分配”选项卡中，他们可以添加和管理需要权限的用户。

有关完整说明，请参阅[有关添加角色分配的操作指南](how-to-create-and-manage-collections.md#add-role-assignments)。

## <a name="legacy-permission-guide"></a>旧版权限指南

> [!NOTE]
> 本旧版集合指南仅适用于在 2021 年 8 月 18 日之前创建的 Purview 帐户。 在该时间之后创建的实例应遵循前面的指南。

本文介绍如何在 Azure Purview 的[数据平面](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)中为 8 月 18 日之前创建的 Purview 资源实现基于角色的访问控制 (RBAC)。

> [!IMPORTANT]
> 创建 Purview 帐户的主体会自动获得所有数据平面权限，无论他们可能属于或不属于哪些数据平面角色。 如果任何其他用户要在 Azure Purview 中执行任何操作，他们必须至少具有一个预定义的数据平面角色。

### <a name="azure-purviews-pre-defined-legacy-data-plane-roles"></a>Azure Purview 的预定义旧版数据平面角色

Azure Purview 定义了一组预定义的数据平面角色，可用于控制 Azure Purview 中的访问权限。 这些角色包括：

* **Purview 数据读取者角色** - 有权访问 Purview 门户，并可读取 Azure Purview 中除扫描绑定以外的所有内容
* **Purview 数据策展人角色** - 有权访问 Purview 门户，并可读取 Azure Purview 中除扫描绑定以外的所有内容，可编辑有关资产的信息，可编辑分类定义和术语表术语，并且可以将分类和术语表术语应用于资产。
* **Purview 数据源管理员角色** - 无权访问 Purview 门户（用户还需要具有数据读取者或数据策展人角色），可以管理将数据扫描到 Azure Purview 中的所有方面，但不具有对 Azure Purview 中的内容的读写权限（除了与扫描相关的内容）。

### <a name="understand-how-to-use-azure-purviews-legacy-data-plane-roles"></a>了解如何使用 Azure Purview 的旧版数据平面角色

创建 Azure Purview 帐户后，创建者具有 Azure Purview 数据策展人和数据源管理员角色。 但在角色存储中，未将这些角色分配给帐户创建者。 Azure Purview 认为帐户创建者是主体，并基于其标识向其赋予了这些功能。

其他所有用户只有在至少具有其中一个角色时才能使用 Azure Purview 帐户。 这意味着，创建 Azure Purview 帐户后，在将其他所有用户分配到一个或多个先前定义的角色之前，仅创建者可访问帐户或使用其 API。

请注意，Purview 数据源管理员角色有两种受支持的方案。 第一种方案适用于已经是 Purview 数据读取者或 Purview 数据策展人的用户，这些用户也需要能够创建扫描。 这些用户需要具有两个角色：至少是 Purview 数据读取者或 Purview 数据策展人之一，以及 Purview 数据源管理员角色。

Purview 数据源管理员的另一种方案适用于编程进程（如服务主体），该进程需要能够设置和监视扫描但不应具有访问目录任何数据的权限。

通过使服务主体具有 Purview 数据源管理员角色而不具有任何其他两个角色，可以实现此方案。 该主体将无法访问 Purview 门户，但这无妨。 因为它是编程主体，并且仅通过 API 进行通信。

### <a name="putting-users-into-legacy-roles"></a>为用户分配旧版角色

因此，在创建 Azure Purview 帐户之后，首先要做的就是为用户分配这些角色。

可通过 [Azure 的 RBAC](../role-based-access-control/overview.md) 管理角色分配。

Azure 中只有两个内置控制平面角色可以分配用户角色 - 所有者或用户访问管理员。 因此，若要为用户分配 Azure Purview 的这些角色，必须找到作为所有者或用户访问管理员的人员或自己成为他们。

#### <a name="an-example-of-assigning-someone-to-a-legacy-role"></a>为用户分配旧版角色的示例

1. 转到 https://portal.azure.com 并导航到 Azure Purview 帐户
1. 在左侧单击“访问控制(IAM)”
1. 然后，按照[此处](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)提供的一般说明进行操作

### <a name="legacy-role-definitions-and-actions"></a>旧版角色定义和操作

角色定义为操作的集合。 有关如何定义角色的详细信息，请参阅[此处](../role-based-access-control/role-definitions.md)。 若要了解 Azure Purview 角色的角色定义，请参阅[此处](../role-based-access-control/built-in-roles.md)。

### <a name="getting-added-to-a-legacy-data-plane-role-in-an-azure-purview-account"></a>在 Azure Purview 帐户中添加到旧版数据平面角色

如果你希望获得对 Azure Purview 帐户的访问权限，以便可以使用其工作室或调用其 API，则你需要添加到 Azure Purview 数据平面角色中。 只有 Azure Purview 帐户的所有者或用户访问管理员才能执行此操作。 对于大多数用户来说，下一步是找到一名本地管理员，该管理员可以帮助你找到可以为你提供访问权限的合适人员。

对于有权访问其公司 [Azure 门户](https://portal.azure.com)的用户，可以查看他们要加入的特定 Azure Purview 帐户，单击其“访问控制(IAM)”选项卡，然后查看作为所有者或用户访问管理员 (UAA) 的用户。 但请注意，在某些情况下，Azure Active Directory 组或服务主体可能用作所有者或 UAA，在这种情况下，可能无法直接联系这些组或服务主体。 而是需要寻求管理员的帮助。

### <a name="legacy---who-should-be-assigned-to-what-role"></a>旧版 - 应将谁分配到哪个角色？

|使用方案|合适的角色|
|-------------|-----------------|
|我只需要查找资产，不需要编辑任何内容|Purview 数据读取者角色|
|我需要编辑有关资产的信息，对其进行分类，将它们与术语表条目等相关联。|Purview 数据策展人角色|
|我需要编辑术语表或设置新的分类定义|Purview 数据策展人角色|
|我的应用程序服务主体需要将数据推送到 Azure Purview|Purview 数据策展人角色|
|我需要通过 Purview Studio 设置扫描|Purview 数据源管理员角色以及至少 Purview 数据读取者角色或 Purview 数据策展者角色之一|
|我需要启用服务主体或其他编程标识，以在不允许编程标识访问目录信息的情况下在 Azure Purview 中设置和监视扫描 |Purview 数据源管理员角色|
|我需要将用户分配到 Azure Purview 角色中 | 所有者或用户管访问理员 |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles-legacy.png" alt-text="显示 Purview 旧版角色的图表" lightbox="./media/catalog-permissions/collection-permissions-roles-legacy.png":::

## <a name="next-steps"></a>后续步骤

对集合和访问控制有了基本的了解后，接下来请遵循以下指南创建和管理这些集合，或者开始将源注册到 Purview 资源中。

- [如何创建和管理集合](how-to-create-and-manage-collections.md)
- [Purview 支持的数据源](purview-connector-overview.md)
