---
title: 快速入门：从租户中删除应用程序
titleSuffix: Azure AD
description: 本快速入门使用 Azure 门户从 Azure Active Directory (Azure AD) 租户中删除应用程序。
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
ms.openlocfilehash: a29ac9aa0cecaefb3d9ada87b60d2bebb8c5269b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112414"
---
# <a name="quickstart-delete-an-application-from-your-tenant"></a>快速入门：从租户中删除应用程序

本快速入门使用 Azure 门户删除已添加到 Azure Active Directory (Azure AD) 租户的应用程序。

若要详细了解 SSO 和 Azure，请参阅[什么是单一登录 (SSO)](what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要从 Azure AD 租户中删除应用程序，需具备：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。
- 可选：完成[配置应用](add-application-portal-configure.md)。
- 可选：完成[向应用分配用户](add-application-portal-assign-users.md)。
- 可选：完成[设置单一登录](add-application-portal-setup-sso.md)。

>[!IMPORTANT]
>使用非生产环境测试本快速入门中的步骤。

> [!NOTE]
>若要从 Azure AD 中删除应用程序，必须为用户分配以下任一角色：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>从 Azure AD 租户中删除应用程序

从 Azure AD 租户中删除应用程序的方法如下：

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要删除的应用程序。 在本例中，我们想要删除 360 Online。
1. 在左侧窗格的“管理”部分中，选择“属性” 。
1. 选择“删除”，然后选择“是”以确认要从 Azure AD 租户中删除该应用 。

:::image type="content" source="media/add-application-portal/delete-application.png" alt-text="显示如何更改徽标的“属性”屏幕的屏幕截图。":::

> [!TIP]
> 可使用 Graph API 自动管理应用，具体请参阅[使用 Microsoft Graph API 自动管理应用](/graph/application-saml-sso-configure-api)。

## <a name="clean-up-resources"></a>清理资源

完成本快速入门系列后，请考虑删除应用以清理你的测试租户。 本快速入门介绍了如何删除应用。

## <a name="next-steps"></a>后续步骤

你已完成了快速入门系列！ 接下来，了解单一登录 (SSO)，请参阅[什么是 SSO？](what-is-single-sign-on.md) 或者了解应用管理中的最佳做法。
> [!div class="nextstepaction"]
> [应用程序管理最佳做法](application-management-fundamentals.md)
