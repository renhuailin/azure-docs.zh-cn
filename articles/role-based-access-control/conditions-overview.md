---
title: 什么是 Azure 基于属性的访问控制 (Azure ABAC)？ （预览版）
description: 概述 Azure 基于属性的访问控制 (Azure ABAC)。 使用带条件的角色分配控制对 Azure 资源的访问权限。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: overview
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.openlocfilehash: 6919a5b94d9122f16084933574ffc88f8c9f1249
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847411"
---
# <a name="what-is-azure-attribute-based-access-control-azure-abac-preview"></a>什么是 Azure 基于属性的访问控制 (Azure ABAC)？ （预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

基于属性的访问控制 (ABAC) 是一个授权系统，根据与安全主体、资源和环境关联的属性定义访问权限。 通过 ABAC，可根据属性向安全主体授予访问资源的权限。 Azure ABAC 是指为 Azure 实现 ABAC。

## <a name="what-are-role-assignment-conditions"></a>什么是角色分配条件？

Azure 基于角色的访问控制 (Azure RBAC) 是一个授权系统，可帮助管理谁有权访问 Azure 资源、他们可对这些资源执行哪些操作，以及他们有权访问哪些区域。 在大多数情况下，Azure RBAC 将使用角色定义和角色分配提供所需的访问管理。 但在某些情况下，你可能想要提供更精细的访问管理，或者简化对数百个角色分配的管理。

Azure ABAC 构建在 Azure RBAC 之上，它在特定操作上下文中根据属性添加角色分配条件。 角色分配条件是一项额外检查，你可选择将其添加到角色分配中，来提供更精细的访问控制。 条件会筛选找到作为角色定义和角色分配的一部分而授予的权限。 例如，为了读取对象，你可添加要求对象具有特定标记的条件。 你无法使用条件显式拒绝对特定资源的访问。

## <a name="why-use-conditions"></a>为何要使用条件？

使用角色分配条件主要有三个优点：

- **提供更精细的访问控制** - 角色分配使用带有操作和数据操作的角色定义来向安全主体授予权限。 你可编写条件来对这些权限进行筛选，以提供更精细的访问控制。 还可将条件添加到特定操作。 例如，只有在 Blob 被标记为 Project=Blue 时，才能向 John 授予对订阅中的 Blob 的读取访问权限。 
- **帮助减少角色分配的数量** - 目前，每个 Azure 订阅都有 2000 个角色分配这一限制。 某些场景需要数千个角色分配。 必须对所有这些角色分配进行管理。 在这些场景中，你可添加条件来显著减少角色分配数量。 
- **使用具有特定业务含义的属性** - 通过条件，可在访问控制中使用具有特定业务含义的属性。 项目名称、软件开发阶段和分类级别就是这类属性。 这些资源属性的值是动态的，随着用户在团队和项目之间移动而变化。

## <a name="example-scenarios-for-conditions"></a>条件的示例场景

在一些场景中，你可能想要将条件添加到角色分配。 下面是一些示例。

- 读取访问标记为 Project=Cascade 的 Blob
- 新的 Blob 必须包含标记 Project=Cascade
- 现有 Blob 必须至少标记有一个 Project 键或 Program 键
- 现有 Blob 必须标记有一个 Project 键以及 Cascade、Baker 或 Skagit 值
- 在名为 blobs-example-container 的容器中读取、写入或删除 Blob
- 读取访问名为 blobs-example-container 且路径为 readonly 的容器中 Blob
- 写入访问名为 Contosocorp 且路径为 uploads/contoso 的容器中的 Blob
- 读取访问标记为 Program=Alpine 且路径为 logs 的 Blob

若要详细了解如何创建这些示例，请查看 [Azure 角色分配条件示例](../storage/common/storage-auth-abac-examples.md)。

## <a name="where-can-conditions-be-added"></a>可在哪里添加条件？

目前，可将条件添加到具有[存储 blob 数据操作](conditions-format.md#actions)的内置或自定义角色分配。 其中包括以下内置角色：

- [存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)
- [存储 Blob 数据所有者](built-in-roles.md#storage-blob-data-owner)
- [存储 Blob 数据读者](built-in-roles.md#storage-blob-data-reader)

在与角色分配相同的范围内添加条件。 与角色分配一样，你必须具有添加条件的 `Microsoft.Authorization/roleAssignments/write` 权限。

以下是可在条件中使用的存储属性。

- 容器名称
- Blob 路径
- Blob 索引标记键
- Blob 索引标记

> [!TIP]
> Blob 还能够存储任意用户定义的键值元数据。 尽管元数据与 Blob 索引标记类似，但必须使用带条件的 Blob 索引标记。 有关详细信息，请查看[通过 Blob 索引标记管理和查找 Azure Blob 数据（预览）](../storage/blobs/storage-manage-find-blobs.md)。

## <a name="what-does-a-condition-look-like"></a>条件是什么样的？

可将条件添加到新的或现有的角色分配。 下面是已在资源组范围分配给名为 Chandra 的用户的[存储 Blob 数据读取者](built-in-roles.md#storage-blob-data-reader)。 还添加了一个条件，它只允许对标记为 Project=Cascade 的 Blob 进行读取访问。

![带条件的角色分配的关系图。](./media/conditions-overview/condition-role-assignment-rg.png)

如果 Chandra 尝试读取没有 Project=Cascade 标记的 Blob，则访问将被阻止。

![在使用条件的情况下阻止访问的关系图。](./media/conditions-overview/condition-access-multiple.png)

下面是条件在 Azure 门户中的呈现效果：

![包含 Blob 索引标记的值的生成表达式部分。](./media/shared/condition-expressions.png)

下面是条件在代码中的呈现效果：

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

若要详细了解条件的格式，请查看 [Azure 角色分配条件格式和语法](conditions-format.md)。

## <a name="conditions-and-privileged-identity-management-pim"></a>条件和 Privileged Identity Management (PIM)

你还可使用 Privileged Identity Management (PIM) 将条件添加到符合资格的角色分配。 使用 PIM 时，最终用户必须激活符合资格的角色分配，才能获得执行特定操作的权限。 通过使用 PIM 中的条件，不仅可使用精细的条件限制用户对资源的访问，还可在 PIM 中使用限时设置、审批工作流和审核核线索等对资源进行保护。 有关详细信息，请查看[在 Privileged Identity Management 中分配 Azure 资源角色](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)。

## <a name="terminology"></a>术语

为了更好地理解 Azure RBAC 和 Azure ABAC，可参考以下术语列表。

| 术语 | 定义 |
| --- | --- |
| 基于属性的访问控制 (ABAC) | 根据与安全主体、资源和环境关联的属性定义访问权限的授权系统。 通过 ABAC，可根据属性向安全主体授予访问资源的权限。 |
| Azure ABAC | 是指为 Azure 实现 ABAC。 |
| 角色分配条件 | 可选择性地添加到角色分配中的一项额外检查，可提供更精细的访问控制。 |
| attribute | 在此上下文中，键值对 Project=Blue 中的 Project 是属性键，Blue 是属性值。 在访问控制方面，属性和标记是同义词。 |
| expression | 在条件中计算得到 true 或 false 的语句。 表达式的格式为&lt;属性&gt; &lt;运算符&gt; &lt;值&gt;。 |

## <a name="next-steps"></a>后续步骤

- [有关 Azure 角色分配条件的常见问题解答（预览）](conditions-faq.md)
- [示例 Azure 角色分配条件（预览）](../storage/common/storage-auth-abac-examples.md)
- [教程：使用 Azure 门户添加角色分配条件来限制对 Blob 的访问（预览）](../storage/common/storage-auth-abac-portal.md)
