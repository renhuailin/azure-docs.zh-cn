---
title: Synapse 基于角色的访问控制
description: 本文介绍 Azure Synapse Analytics 中基于角色的访问控制
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: dda885d55f2102f1fae478c8d214f2bca44f52f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737425"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>什么是 Synapse 基于角色的访问控制 (RBAC)？

Synapse RBAC 扩展了用于 Synapse 工作区及其内容的 [AZURE RBAC](../../role-based-access-control/overview.md) 功能。 

使用 Azure RBAC 可以管理谁可以创建、更新或删除 Synapse 工作区及其 SQL 池、Apache Spark 池和集成运行时。

Synapse RBAC 用于管理可以执行以下操作的人员：
- 发布代码项目并列出或访问已发布的代码项目， 
- 在 Apaches Spark 池和集成运行时上执行代码，
- 访问受凭据保护的链接（数据）服务 
- 监视或取消作业执行、查看作业输出和执行日志。  

>[!Note]
>尽管 Synapse RBAC 用于管理对已发布的 SQL 脚本的访问，但它仅提供对无服务器 SQL 池的有限访问控制，不用于控制对专用 SQL 池的访问。  对 SQL 池的访问主要通过 SQL 安全来控制。

## <a name="what-can-i-do-with-synapse-rbac"></a>Synapse RBAC 有什么用途？

下面是 Synapse RBAC 的用途的一些示例：
  - 让用户可以将对 Apache Spark 笔记本和作业所做的更改发布到实时服务。
  - 让用户可以在特定的 Apache Spark 池上运行和取消笔记本和 Spark 作业。
  - 让用户可以使用特定的凭据，以便能够运行受工作区系统标识保护的管道，并访问使用凭据保护的链接服务中的数据。 
  - 让管理员可以管理、监视和取消特定 Spark 池上的作业执行。    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC 的工作原理
与 Azure RBAC 类似，Synapse RBAC 通过创建角色分配来运作。 角色分配由 3 个元素组成：安全主体、角色定义和范围。  

### <a name="security-principals"></a>安全主体

安全主体指的是用户、组、服务主体或托管标识。

### <a name="roles"></a>角色
 
角色是可对特定资源类型或项目类型执行的权限或操作的集合。

Synapse 提供内置角色来定义满足不同角色需求的操作集合：
- 管理员可以获得创建和配置工作区的完整访问权限 
- 开发人员可以创建、更新和调试 SQL 脚本、笔记本、管道和数据流，但不能在生产计算资源/数据上发布或执行此代码
- 操作员可以监视和管理系统状态、应用程序执行和检查日志，而无需访问代码或执行的输出。
- 安全人员可以管理和配置终结点，而无需访问代码、计算资源或数据。

[详细了解](./synapse-workspace-synapse-rbac-roles.md)内置的 Synapse 角色。 

### <a name="scopes"></a>作用域

“范围”定义将访问权限应用到的资源或项目。  Synapse 支持分层范围。  较低级别的对象会继承在更高级别的范围授予的权限。  在 Synapse RBAC 中，顶级范围是工作区。  在工作区范围分配角色会向工作区中的所有适用对象授予权限。  

工作区中当前受支持的范围包括：Apache Spark 池、集成运行时、链接服务和凭据。 

代码项目的访问权限是在工作区范围授予的。  在更高版本中，将支持对工作区中的项目集合授予访问权限。

## <a name="resolving-role-assignments-to-determine-permissions"></a>解析角色分配以确定权限

角色分配可在指定范围向主体授予由角色定义的权限。

Synapse RBAC 是一种类似于 Azure RBAC 的加法模型。 可以在不同的范围将多个角色分配给一个主体。 在计算安全主体的权限时，系统会考虑向主体以及直接或间接包含该主体的组分配的所有角色。  在确定适用的权限时，它还会考虑每个分配的范围。  

## <a name="enforcing-assigned-permissions"></a>强制执行分配的权限

在 Synapse Studio 中，如果你没有所需的权限，那么在尝试操作时，特定的按钮或选项可能会灰显，或者返回一个权限错误。 

如果按钮或选项处于禁用状态，将鼠标悬停在按钮或选项上时将显示指出所需权限的工具提示。  请与 Synapse 管理员联系，让其分配可授予所需权限的角色。 你可在[此处](./synapse-workspace-synapse-rbac-roles.md)查看提供特定操作的角色。

## <a name="who-can-assign-synapse-rbac-roles"></a>谁可以分配 Synapse RBAC 角色？

只有 Synapse 管理员才能分配 Synapse RBAC 角色。  工作区级别的 Synapse 管理员可以在任何范围授予访问权限。  较低级别范围的 Synapse 管理员只能在对应的范围授予访问权限。 

创建新工作区时，系统会在工作区范围自动为创建者提供 Synapse 管理员角色。   

## <a name="where-do-i-manage-synapse-rbac"></a>在何处管理 Synapse RBAC？

可使用管理中心的访问控制工具在 Synapse Studio 中管理 Synapse RBAC。 

## <a name="next-steps"></a>后续步骤

了解内置的 [Synapse RBAC 角色](./synapse-workspace-synapse-rbac-roles.md)。

了解[如何查看工作区的 Synapse RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md)。

了解[如何分配 Synapse RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)