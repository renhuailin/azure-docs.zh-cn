---
title: 快速入门：向应用程序分配用户
titleSuffix: Azure AD
description: 此快速入门介绍支持用户使用已设置的应用将 Azure AD 用作标识提供者的过程。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 5c8b4ec030f60b940d8d6c36bd31e10932151dd5
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667162"
---
# <a name="quickstart-assign-users-to-an-application"></a>快速入门：向应用程序分配用户

在上一个快速入门中，你配置了应用的属性。 设置属性时，会为已分配和未分配的用户配置体验。 此快速入门介绍将用户分配到应用的过程。

## <a name="prerequisites"></a>先决条件

若要向已添加到 Azure AD 租户的应用分配用户，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。
- 可选：完成[配置应用](add-application-portal-configure.md)。

>[!IMPORTANT]
>使用非生产环境测试本快速入门中的步骤。

## <a name="assign-users-to-an-app"></a>向应用分配用户

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
2. 在左侧导航菜单中，选择“用户和组”。
   > [!NOTE]
   > 某些 Microsoft 365 应用需要使用 PowerShell。
3. 选择“添加用户/组”按钮。
4. 在“添加分配”窗格中，选择“用户和组”。 
5. 选择要分配到该应用程序的用户或组。 也可以开始在搜索框中输入用户或组的名称。 可以选择多个用户和组，所选内容会显示在“选定项”下。
    > [!IMPORTANT]
    > 如果你将某组分配到应用程序，只有属于此组的用户才有访问权限。 此分配不会级联到嵌套组。

    > [!NOTE]
    > 基于组的分配需要 Azure Active Directory Premium P1 或 P2 版本。 基于组的分配仅支持安全组。 目前不支持嵌套的组成员身份和 Microsoft 365 组。 有关本文中讨论的功能的其他许可要求，请参阅 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。
6. 完成后，请选择“选择”。
   :::image type="content" source="./media/assign-user-or-group-access-portal/assign-users.png" alt-text="将用户或组分配给应用":::
7. 在“用户和组”窗格上的列表中选择一个或多个用户或组，然后选择窗格底部的“选择”按钮。 
8. 可将角色分配给用户或组（如果应用程序支持此操作）。 在“添加分配”窗格中，选择“选择角色”。  在“选择角色”窗格中，选择要应用到所选用户或组的角色，然后选择窗格底部的“确定”。 
    > [!NOTE]
    > 如果应用程序不支持角色选择，则会分配默认的访问角色。 在这种情况下，应用程序会管理用户拥有的访问权限级别。
9. 在“添加分配”窗格中，选择窗格底部的“分配”按钮。 

可以使用相同的过程取消分配用户或组。 选择要取消分配的用户或组，然后选择“删除”。 某些 Microsoft 365 和 Office 365 应用需要使用 PowerShell。

## <a name="clean-up-resources"></a>清理资源

完成快速入门后，请考虑删除该应用。 这样就可以保持测试租户的整洁。 本系列中的最后一个快速入门介绍如何删除应用，请参阅[删除应用](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何为应用设置单一登录。
> [!div class="nextstepaction"]
> [设置基于 SAML 的单一登录](add-application-portal-setup-sso.md)

OR

> [!div class="nextstepaction"]
> [设置基于 OIDC 的单一登录](add-application-portal-setup-oidc-sso.md)
