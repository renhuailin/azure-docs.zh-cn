---
title: 如何在 Synapse Studio 中查看 Synapse RBAC 角色分配
description: 本文介绍如何使用 Synapse Studio 查看 Synapse RBAC 角色分配
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 11e5ed746215df4518e9bd6af2c0c92539067e47
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747003"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>如何查看 Synapse RBAC 角色分配

Synapse RBAC 角色用于向用户、组和其他安全主体分配权限，以允许访问和使用 Synapse 资源。  [了解详细信息](./synapse-workspace-synapse-rbac.md)

本文介绍如何查看工作区的当前角色分配。

对于任何 Synapse RBAC 角色，你可以列出所有作用域的 Synapse RBAC 角色分配，包括你无权访问的对象的分配。 只有 Synapse 管理员才能授予 Synapse RBAC 访问权限。  

>[!Note]
> 来宾用户（来自不同 AD 租户的用户）在被分配了 Synapse 管理员角色后，也可以查看和管理角色分配。    

## <a name="open-synapse-studio"></a>打开 Synapse Studio  

若要查看角色分配，请先[打开 Synapse Studio](https://web.azuresynapse.net/)，并选择工作区。 若要登录到工作区，可以使用两种帐户选择方法。 一种是从 Azure 订阅进行选择，另一种是通过手动输入选择帐户 。 如果你有 Synapse Azure 角色或更高级别的 Azure 角色，可以使用这两种方法登录到工作区。 如果没有相关的 Azure 角色，而被授予了 Synapse RBAC 角色，则只有手动输入才能登录到工作区。

![登录到工作区](./media/common/login-workspace.png) 
 
 打开工作区后，选择左侧的“管理”中心，然后展开“安全性”部分，并选择“访问控制”  。 

 ![在左侧的“安全性”部分中选择“访问控制”](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>查看工作区角色分配

“访问控制”屏幕列出了工作区的所有当前角色分配（按角色分组）。 每个分配都包含主体名称、主体类型、角色及其作用域。

![“访问控制”屏幕](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

如果在不同作用域为主体分配了相同的角色，你将看到主体的多个分配（每个作用域一个）。  

如果将角色分配给安全组，你将看到显式分配给该组的角色，而不是从父组继承的角色。  

你可以按主体名称或电子邮件筛选列表，并选择性地筛选对象类型、角色和作用域。 在“名称”筛选器中输入你的姓名或电子邮件别名，以查看分配给你的角色。 只有 Synapse 管理员才能更改你的角色。

>[!Important] 
>如果你是分配有角色的组的直接或间接成员，则可能具有未显示的权限。

>[!tip]
>可以在 Azure 门户中使用 Azure Active Directory 查找你的组成员身份。  

如果创建新的工作区，则系统会在工作区范围内自动为你和工作区 MSI 服务主体提供 Synapse 管理员角色。

## <a name="next-steps"></a>后续步骤

了解[如何管理 Synapse RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md)。

了解[执行特定任务所需的角色](./synapse-workspace-understand-what-role-you-need.md)