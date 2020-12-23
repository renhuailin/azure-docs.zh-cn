---
title: 如何在 Synapse Studio 中查看 Synapse RBAC 角色分配
description: 本文介绍如何使用 Synapse Studio 查看 Synapse RBAC 角色分配
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 9ebbe54dd41cd7d7b83ef12f465326db3ef2b9ab
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572178"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>如何查看 Synapse RBAC 角色分配

Synapse RBAC 角色用于将权限分配给用户、组和其他安全主体，以允许访问和使用 Synapse 资源。  [了解详细信息](https://go.microsoft.com/fwlink/?linkid=2148306)

本文介绍如何查看工作区的当前角色分配。

对于任何 Synapse RBAC 角色，你可以列出所有作用域的 Synapse RBAC 角色分配，包括你无权访问的对象的分配。 只有 Synapse 管理员才能授予 Synapse RBAC 访问权限。  

>[!Note]
>来宾用户 (不同 AD 租户中的用户) 无法查看或管理角色分配，即使分配了 Synapse 管理员角色也是如此。    

## <a name="open-synapse-studio"></a>打开 Synapse Studio  

若要查看角色分配，请先 [打开 Synapse Studio](https://web.azuresynapse.net/) ，然后选择工作区。 

![登录到工作区](./media/common/login-workspace.png) 
 
 打开工作区后，在左侧选择 " **管理** 中心"，展开 " **安全性** " 部分，然后选择 " **访问控制**"。 

 ![选择左侧的 "安全" 部分中的访问控制](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>查看工作区角色分配

"访问控制" 屏幕按角色列出了工作区的所有当前角色分配。 每个分配都包含主体名称、主体类型、角色及其作用域。

![访问控制屏幕](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

如果在不同的作用域内为主体分配了相同的角色，你将看到主体的多个分配，每个作用域一个。  

如果将角色分配给安全组，你将看到显式分配给组的角色，而不是从父组继承的角色。  

你可以按主体名称或电子邮件筛选列表，并有选择地筛选对象类型、角色和作用域。 在名称筛选器中输入你的姓名或电子邮件别名，查看分配给你的角色。 只有 Synapse 管理员才能更改你的角色。

>[!Important] 
>如果直接或间接分配了角色的组的成员，则可能具有未显示的权限。

>[!tip]
>你可以使用 Azure 门户中的 Azure Active Directory 找到你的组成员身份。  

如果创建新的工作区，则会在工作区范围内自动为你和工作区 MSI 服务主体提供 Synapse 管理员角色。

## <a name="next-steps"></a>后续步骤

了解 [如何管理 SYNAPSE RBAC 角色分配](./how-to-manage-synapse-rbac-role-assignments.md)。

了解 [执行特定任务所需的角色](./synapse-workspace-understand-what-role-you-need.md)