---
title: Azure 角色分配条件（预览版）常见问题解答
description: Azure 角色分配条件（预览版）的常见问题解答
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.openlocfilehash: df1b585fb0f84dafe26c954386f487e3ceff2bc7
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847321"
---
# <a name="faq-for-azure-role-assignment-conditions-preview"></a>Azure 角色分配条件（预览版）常见问题解答

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="frequently-asked-questions"></a>常见问题

是否可以在 Azure 门户上的条件生成器中选择存储容器名称或 Blob 路径？

必须在条件中编写存储容器名称、Blob 路径、标记名称或值。 未提供选择属性值的体验。

是否可以将表达式分组？

如果为目标操作添加三个或更多个表达式，则必须在代码编辑器、Azure PowerShell 或 Azure CLI 中定义这些表达式的逻辑分组。 `a AND b OR c` 逻辑分组可以是 `(a AND b) OR c` 或 `a AND (b OR c )`。

是否可以在管理组范围添加角色分配条件？
  
Azure 门户不允许在管理组范围编辑或查看条件。 不会为管理组范围显示“条件”列。 Azure PowerShell 和 Azure CLI 允许在管理组范围添加条件。

预览版中是否通过 Privileged Identity Management (PIM) 支持 Azure 资源的条件？

是。 有关详细信息，请参阅[在 Privileged Identity Management 中分配 Azure 资源角色](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)。

经典管理员是否支持条件？

不是。 

是否可将条件添加到自定义角色分配？

是，前提是自定义角色包括[支持条件的操作](conditions-format.md#actions)。
 
条件是否会增加访问存储 Blob 时出现的延迟？

不会，根据我们的基准测试，条件预期不会增加任何用户可察觉的延迟。

角色分配架构中引入了哪些新属性来支持条件？

下面是新的条件属性：

- `condition`：使用角色定义和属性中的一个或多个操作生成的条件语句。
- `conditionVersion`：条件版本号。 默认值为 2.0，这也是唯一公开支持的版本。

角色分配还有一个新的说明属性：

- `description`：可用于描述条件的角色分配说明。

条件是应用于整个角色分配还是应用于特定的操作？

条件仅应用于特定的目标操作。

条件的限制是什么？

条件的长度最大为 8 KB。

说明的限制是什么？

说明的长度最大为 2 KB。

是否可以创建具有和没有条件的角色分配，但使用相同的安全主体、角色定义和范围元组？

不可以，如果尝试创建此角色分配，则会显示错误。

角色分配中的条件是否提供显式拒绝效果？

不会，角色分配中的条件不会产生显式拒绝效果。 角色分配中的条件向下筛选角色分配中授予的访问权限，这可能导致不允许访问。 显式拒绝效果是拒绝分配的一部分。

## <a name="next-steps"></a>后续步骤

- [Azure 角色分配条件格式和语法（预览版）](conditions-format.md)
- [排查 Azure 角色分配条件的问题（预览版）](conditions-troubleshoot.md)
