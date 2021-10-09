---
title: 快速入门：创建和分配用户帐户
titleSuffix: Azure AD
description: 在 Azure Active Directory 租户中创建用户帐户并将其分配给应用程序。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 28f5cfe158c13928d9443df22551bcfd6e7d8e85
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058377"
---
# <a name="quickstart-create-and-assign-a-user-account-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中创建和分配用户帐户

本快速入门介绍如何使用 Azure Active Directory 管理中心在 Azure Active Directory (Azure AD) 租户中创建用户帐户。 创建帐户后，可以将其分配给已添加到租户中的企业应用程序。

建议使用非生产环境来测试本快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要创建用户帐户并将其分配给企业应用程序，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 完成[快速入门：添加企业应用程序](add-application-portal.md)中的步骤。

## <a name="create-a-user-account"></a>创建用户帐户

若要在 Azure AD 租户中创建用户帐户，请执行以下操作：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“用户”。
1. 选择窗格顶部的“新建用户”。 

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="向 Azure AD 租户添加新用户帐户。":::
    
1. 在“用户名”字段中，输入用户帐户的用户名。 例如 `contosouser1@contoso.com`。 请确保将 `contoso.com` 更改为租户域的名称。
1. 在“姓名”字段中，输入帐户用户的姓名。 例如 `contosouser1`。
1. 保持选中“自动生成密码”，然后选择“显示密码”。 记下“密码”框中显示的值  。
1. 选择“创建”  。

## <a name="assign-a-user-account-to-an-enterprise-application"></a>将用户帐户分配给企业应用程序

若要将用户帐户分配给企业应用程序，请执行以下操作：

1. 在 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，选择“企业应用程序”，然后搜索并选择要向其分配用户帐户的应用程序。 例如，你在上一个快速入门中创建的名为“Azure AD SAML Toolkit 1”的应用程序。
1. 在左侧窗格中，选择“用户和组”，然后选择“添加用户/组”。

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="将用户帐户分配给 Azure AD 租户中的 zn 应用程序。":::

1. 在“添加分配”窗格中，选择“用户和组”下的“未选择任何内容”。
1. 搜索并选择要分配给应用程序的用户。 例如 `contosouser1@contoso.com`。
1. 选择“选择”  。
1. 在“添加分配”窗格中，选择窗格底部的“分配” 。

## <a name="clean-up-resources"></a>清理资源

如果你打算完成下一个快速入门，请保留你创建的应用程序。 否则，可以考虑将其删除以清理租户。 有关详细信息，请参阅[删除应用程序](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

了解如何为企业应用程序设置单一登录。
> [!div class="nextstepaction"]
> [启用单一登录](add-application-portal-setup-sso.md)
