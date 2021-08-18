---
title: 在 Azure AD 权利管理中将访问权限管理委派给的目录创建者 - Azure Active Directory
description: 了解如何将访问权限管理从 IT 管理员委托给目录创建者和项目经理，使他们能够自行管理访问权限。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afa05b8a529d8a9e9fceeb4a113f0b743acfc05
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286569"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中将访问权限管理委派给的目录创建者

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 默认情况下，全局管理员或 Identity Governance 管理员可以[创建目录](entitlement-management-catalog-create.md)，并且可以将其他用户添加为目录所有者。

若要委派给不是管理员的用户，以便他们可以创建自己的目录，可以将这些用户添加到 Azure AD 权利管理定义的目录创建者角色。 可以添加单个用户，也可以添加一个组，使其成员可以创建目录。  创建目录后，他们就可以将自己拥有的资源添加到其目录。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>以 IT 管理员身份委派给目录创建者

遵循以下步骤将用户分配到目录创建者角色。

**必备角色**：全局管理员、标识治理管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在“权利管理”部分的左侧菜单中，单击“设置”。

1. 单击 **“编辑”** 。

    ![用于添加目录创建者的设置](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. 在“委托权利管理”部分，单击“添加目录创建者”，以选择要向其委托此权利管理角色的用户或组 。

1. 单击“选择”。

1. 单击“保存”。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>允许受委托的角色访问 Azure 门户

若要允许受委托的角色（例如目录创建者和访问包管理者）访问 Azure 门户以管理访问包，应检查管理门户设置。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，单击“Azure Active Directory”，然后单击“用户” 。

1. 在左侧的菜单中，单击“用户设置”。

1. 确保将“限制访问 Azure AD 管理门户”设置为“否” 。

    ![Azure AD 用户设置 - 管理门户](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="manage-role-assignments-programmatically-preview"></a>以编程方式管理角色分配（预览版）

还可使用 Microsoft Graph 查看和更新目录创建者以及权利管理目录特定的角色分配。  对于具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，如果用户在其中具有相应的角色，则可调用图形 API 来列出权利管理的[角色定义](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true)和这些角色定义的[角色分配](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true)。

要检索分配给目录创建者角色（具有定义 ID `ba92d953-d8e0-4e39-a797-0cbedb0a89e8` 的角色）的用户和组列表，请使用图形查询

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=roleDefinitionId eq 'ba92d953-d8e0-4e39-a797-0cbedb0a89e8'&$expand=principal
```


## <a name="next-steps"></a>后续步骤

- [创建和管理资源的目录](entitlement-management-catalog-create.md)
- [将访问权限管理委托给访问包管理者](entitlement-management-delegate-managers.md)
- [将访问治理委托给资源所有者](entitlement-management-delegate.md)

