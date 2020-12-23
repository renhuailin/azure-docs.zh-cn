---
title: Synapse 基于角色的访问控制
description: 本文介绍 Azure Synapse Analytics 中基于角色的访问控制
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7972f34bf0d2b93828899903e013c2e35bc997c0
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523410"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>什么是基于角色的访问控制 (RBAC) ？

Synapse RBAC 扩展了 [AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 的功能，以用于 Synapse 工作区及其内容。 

使用 Azure RBAC 可以管理谁可以创建、更新或删除 Synapse 工作区及其 SQL 池、Apache Spark 池和集成运行时。

Synapse RBAC 用于管理可以执行以下操作的人员：
- 发布代码项目并列出或访问已发布的代码项目， 
- 在 Apaches Spark 池和集成运行时上执行代码。
- 访问受凭据保护的链接 (数据) 服务 
- 监视或取消作业执行、查看作业输出和执行日志。  

>[!Note]
>尽管 Synapse RBAC 用于管理对发布的 SQL 脚本的访问，但它仅提供对无服务器 SQL 池的有限访问控制， _不_ 用于控制对专用 sql 池的访问。  对 SQL 池的访问主要使用 SQL 安全来控制。

## <a name="what-can-i-do-with-synapse-rbac"></a>我可以对 Synapse RBAC 做些什么？

下面是可以通过 Synapse RBAC 执行的操作的一些示例：
  - 允许用户将对 Apache Spark 笔记本和作业所做的更改发布到实时服务。
  - 允许用户在特定 Apache Spark 池上运行和取消笔记本和 spark 作业。
  - 允许用户使用特定的凭据，以便他们能够运行受工作区系统标识保护的管道，并访问使用凭据保护的链接服务中的数据。 
  - 允许管理员管理、监视和取消特定 Spark 池上的作业执行。    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC 的工作原理
与 Azure RBAC 类似，Synapse RBAC 通过创建角色分配来工作。 角色分配由 3 个元素组成：安全主体、角色定义和范围。  

### <a name="security-principals"></a>安全主体

_安全主体_ 是用户、组、服务主体或托管标识。

### <a name="roles"></a>角色
 
_角色_ 是可对特定资源类型或项目类型执行的权限或操作的集合。

Synapse 提供内置角色，用于定义满足不同角色需求的操作集合：
- 管理员可以获得创建和配置工作区的完全访问权限 
- 开发人员可以创建、更新和调试 SQL 脚本、笔记本、管道和数据流，但不能在生产计算资源/数据上发布或执行此代码
- 操作员可以监视和管理系统状态、应用程序执行和检查日志，而无需访问代码或执行中的输出。
- 安全人员可以管理和配置终结点，而无需访问代码、计算资源或数据。

[了解](./synapse-workspace-synapse-rbac-roles.md) 有关内置 Synapse 角色的详细信息。 

### <a name="scopes"></a>作用域

_作用域_ 定义访问应用到的资源或项目。  Synapse 支持分层作用域。  在更高级别范围中授予的权限由较低级别的对象继承。  在 Synapse RBAC 中，顶级作用域是一个工作区。  向工作区范围分配角色会向工作区中的所有适用对象授予权限。  

工作区中受支持的当前范围包括： Apache Spark 池、集成运行时、链接服务和凭据。 

向工作区范围授予对代码项目的访问权限。  在更高版本中，将支持授予对工作区中项目集合的访问权限。

## <a name="resolving-role-assignments-to-determine-permissions"></a>解析角色分配以确定权限

角色分配授予主体在指定范围内由角色定义的权限。

Synapse RBAC 是一种附加模型，如 Azure RBAC。 可以将多个角色分配给单个主体和不同范围。 在计算安全主体的权限时，系统会考虑分配给主体的所有角色，以及直接或间接包含主体的组。  它还在确定应用的权限时，考虑每个分配的范围。  

## <a name="enforcing-assigned-permissions"></a>强制分配权限

在 Synapse Studio 中，特定的按钮或选项可能灰显，或者如果没有所需权限，则在尝试操作时可能返回权限错误。 

如果按钮或选项处于禁用状态，则将鼠标悬停在按钮或选项上会显示具有所需权限的工具提示。  请与 Synapse 管理员联系，以分配授予所需权限的角色。 你可以在 [此处](./synapse-workspace-synapse-rbac-roles.md)查看提供特定操作的角色。

## <a name="who-can-assign-synapse-rbac-roles"></a>谁可以分配 Synapse RBAC 角色？

只有 Synapse 管理员才能分配 Synapse RBAC 角色。  工作区级别的 Synapse 管理员可以在任何范围内授予访问权限。  较低级别的 Synapse 管理员只能在该范围内授予访问权限。 

创建新的工作区时，会自动在工作区范围为创建者提供 Synapse 管理员角色。   

## <a name="where-do-i-manage-synapse-rbac"></a>在哪里管理 Synapse RBAC？

使用管理中心中的访问控制工具在 Synapse Studio 内管理 Synapse RBAC。 

## <a name="next-steps"></a>后续步骤

了解内置的 [SYNAPSE RBAC 角色](./synapse-workspace-synapse-rbac-roles.md)。

了解如何为工作区 [查看 SYNAPSE RBAC 角色分配](./how-to-review-synapse-rbac-role-assignments.md) 。

了解 [如何分配 SYNAPSE RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)