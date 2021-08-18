---
title: 如何在 Synapse Studio 中管理 Synapse RBAC 分配
description: 本文介绍了如何分配和撤销 AAD 安全主体的 Synapse RBAC 角色
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 8bbb6185368b438376529d69409d301c0cdd9c97
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732488"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>如何在 Synapse Studio 中管理 Synapse RBAC 角色分配

Synapse RBAC 使用角色向用户、组和其他安全主体分配权限，以允许访问和使用 Synapse 资源与代码项目。  [了解详细信息](./synapse-workspace-synapse-rbac.md)

本文介绍了如何添加和删除 Synapse RBAC 角色分配。

>[!Note]
>- 若要管理 Synapse RBAC 角色分配，你需要在工作区或包含要管理的对象的较低级别作用域中拥有 Synapse 管理员角色。 如果你是工作区的 Synapse 管理员，则可以授予对工作区中所有对象的访问权限。 
>- 来自不同 AD 租户的来宾用户也可以在被分配“Synapse 管理员”角色后查看和管理角色分配。
>- 为了帮助你在未分配或没有 Synapse 管理员角色可用的情况下重新获得对工作区的访问权限，有权管理工作区上的 **AZURE RBAC** 角色分配的用户也可以管理 **Synapse RBAC** 角色分配，从而可以添加 Synapse 管理员或其他 Synapse 角色分配。
>- 对 SQL 池的访问权限是使用 SQL 权限管理的。  除了 Synapse 管理员角色和 Synapse SQL 管理员角色以外，其他 Synapse RBAC 角色不会授予对 SQL 池的访问权限。

>[!important]
>- 对 Synapse RBAC 角色分配所做的更改可能需要 2-5 分钟才能生效。 
>- 如果你通过修改安全组的成员身份来管理 Synapse RBAC 权限，则对成员身份的更改是使用 Azure Active Directory 管理的。  对组成员身份所做的更改可能需要 10-15 分钟或更长时间才能生效。

## <a name="open-synapse-studio"></a>打开 Synapse Studio  

若要将角色分配给用户、组、服务主体或托管标识，请先[打开 Synapse Studio](https://web.azuresynapse.net/)，然后登录到工作区。 

![登录到工作区](./media/common/login-workspace.png) 
 
 打开工作区后，展开左侧的“安全性”部分，然后选择“访问控制”。 

 ![在左侧的“安全性”部分中选择“访问控制”](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

“访问控制”屏幕将列出当前的角色分配。  你可以按主体名称或电子邮件筛选列表，并选择性地筛选所包含的对象类型、角色或作用域。 在此屏幕中，你可以添加或删除角色分配。  

## <a name="add-a-synapse-role-assignment"></a>添加 Synapse 角色分配

在“访问控制”屏幕上，选择“+添加”以创建新的角色分配

![单击“+ 添加”以创建新的角色分配](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

在“添加角色分配”选项卡上，可以在工作区作用域或工作区项作用域内创建角色分配。 

## <a name="add-workspace-scoped-role-assignment"></a>添加以工作区为作用域的角色分配

首先，选择“工作区”作为作用域，然后选择“Synapse RBAC 角色”。  选择要为其分配角色的主体，然后创建角色分配。 

![添加工作区角色分配 - 选择角色](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

分配的角色将应用于工作区中的所有适用对象。

## <a name="add-workspace-item-scoped-role-assignment"></a>添加以工作区项为作用域的角色分配

若要在更细粒度的作用域内分配角色，请选择“工作区项”作为作用域，然后选择充当作用域的项类型。       

![添加工作区项角色分配 - 选择项类型](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

选择要用作作用域的特定“项”，然后从下拉列表中选择要分配的角色。  下拉列表中仅会列出对选定项类型有效的那些角色。 [了解详细信息](./synapse-workspace-synapse-rbac.md)。  

![添加工作区项角色分配 - 选择角色](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
然后，选择要向其分配角色的主体。  你可以采用重复的方式选择多个主体。  选择“应用”以创建角色分配。

## <a name="remove-a-synapse-rbac-role-assignment"></a>删除 Synapse RBAC 角色分配

若要撤销 Synapse RBAC 访问权限，请删除相应的角色分配。  在“访问控制”屏幕上，使用筛选器找到要删除的角色分配。  选中角色分配，然后选择“删除访问权限”。   

![删除角色分配以删除访问权限](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

请记住，对角色分配所做的更改需要 2-5 分钟才能生效。   

## <a name="next-steps"></a>后续步骤

[了解执行常见任务所需的 Synapse RBAC 角色](./synapse-workspace-understand-what-role-you-need.md)