---
title: 列出 Azure AD 角色定义 - Azure AD
description: 了解如何列出 Azure 内置角色和自定义角色。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00fc768357d80fa22305b3b85e084dc636bd5a7c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796403"
---
# <a name="list-azure-ad-role-definitions"></a>列出 Azure AD 角色定义

角色定义是可执行特权的集合，例如读取、写入和删除。 它通常直接称为“角色”。 Azure Active Directory 有 60 多种内置角色，你也可以创建自己的自定义角色。 如果你曾经有过“这些角色究竟有什么用？”这样的疑问，可查看每个角色的详细权限列表。

本文介绍如何列出 Azure AD 内置角色和自定义角色及其权限。

## <a name="list-all-roles"></a>列出所有角色

1. 登录 [Azure AD 管理中心](https://aad.portal.azure.com)并选择“Azure Active Directory”。

1. 选择“角色和管理员”以查看所有可用角色列表。

    ![Azure 门户中的角色列表](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 在右侧选择省略号，然后选择“说明”来查看角色权限的完整列表。

    该页包含相关文档的链接，引导你对角色进行管理。

    ![显示“全局管理员 - 说明”页面的屏幕截图。](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色权限的详细信息，请参阅 [Azure AD 内置角色](permissions-reference.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
