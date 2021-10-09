---
title: 快速入门：为企业应用程序启用单一登录
titleSuffix: Azure AD
description: 在 Azure Active Directory 中为企业应用程序启用单一登录。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: b13c7ac86ec8d21f143f4069cf1d0c777bde4bda
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058322"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中为企业应用程序启用单一登录

本快速入门介绍如何使用 Azure Active Directory 管理中心为添加到 Azure Active Directory (Azure AD) 租户的企业应用程序启用单一登录 (SSO)。 配置 SSO 后，用户可以使用其 Azure AD 凭据进行登录。 

Azure AD 有一个库，其中包含数千个使用 SSO 的预集成应用程序。 本快速入门使用名为 Azure AD SAML Toolkit 的企业应用程序作为示例，但这些概念适用于库中大多数[预配置的企业应用程序](../saas-apps/tutorial-list.md)。

建议使用非生产环境来测试此快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要配置 SSO，需要满足以下条件：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 完成[快速入门：创建并分配用户帐户](add-application-portal-assign-users.md)中的步骤。

## <a name="enable-single-sign-on"></a>启用单一登录

为应用程序启用 SSO：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一进行登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。 搜索并选择要使用的应用程序。 例如“Azure AD SAML Toolkit 1”.
1. 在左侧菜单的“管理”部分中，选择“单一登录”，打开“单一登录”窗格进行编辑  。
1. 选择“SAML”以打开“SSO 配置”页。 配置应用程序后，用户可以使用 Azure AD 租户中的凭据进行登录。
1. 将应用程序配置为使用 Azure AD 进行基于 SAML 的 SSO 的过程因应用程序而异。 对于库中的任何企业应用程序，请使用链接查找有关配置应用程序所需步骤的信息。 本快速入门中列出了用于 Azure AD SAML Toolkit 的步骤。

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="为企业应用程序配置单一登录。":::

1. 在“设置 Azure AD SAML Toolkit 1”部分中，记录“登录 URL”、“Azure AD 标识符”和“注销 URL”属性的值以供稍后使用   。

## <a name="configure-single-sign-on-in-the-tenant"></a>在租户中配置单一登录

添加“登录”和“回复 URL”值，并下载证书以开始在 Azure AD 中配置 SSO。

在 Azure AD 中配置 SSO：

1. 在 Azure 门户中，选择“设置单一登录”窗格上“基本 SAML 配置”部分中的“编辑”  。 
1. 对于“回复 URL(断言使用者服务 URL)”，输入 `https://samltoolkit.azurewebsites.net/SAML/Consume`。
1. 对于登录 URL，输入 `https://samltoolkit.azurewebsites.net/`。
1. 选择“保存”。
1. 在“SAML 签名证书”部分中，选择下载“证书(原始)”，下载 SAML 签名证书，并将其保存以供稍后使用  。

## <a name="configure-single-sign-on-in-the-application"></a>在应用程序中配置单一登录

在应用程序中使用单一登录时，需要向应用程序注册用户帐户并添加之前记录的 SAML 配置值。

### <a name="register-the-user-account"></a>注册用户帐户

向应用程序注册用户帐户：

1. 打开新的浏览器窗口，并浏览到应用程序的登录 URL。 对于 Azure AD SAML Toolkit 应用程序，地址为 `https://samltoolkit.azurewebsites.net`。
1. 在页面右上角，选择“注册”。

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="在 Azure AD SAML Toolkit 应用程序中注册用户帐户。":::

1. 对于“电子邮件”，输入将访问应用程序的用户的电子邮件地址。 例如，前面的快速入门中创建了使用地址 `contosouser1@contoso.com` 的用户帐户。 请确保将 `contoso.com` 更改为租户的域。
1. 输入密码并进行确认。
1. 选择“注册”  。

### <a name="configure-saml-settings"></a>配置 SAML 设置

为应用程序配置 SAML 设置：

1. 使用创建的用户帐户的凭据登录，并选择页面左上角的“SAML 配置”。
1. 在页面中间，选择“创建”。
1. 对于“登录 URL”、“Azure AD 标识符”和“注销 URL”，请输入之前记录的值  。
1. 选择“选择文件”，上传之前下载的证书。
1. 选择“创建”  。
1. 复制“SP 发起的登录 URL”和“断言使用者服务(ACS) URL”以供稍后使用 。

## <a name="update-single-sign-on-values"></a>更新单一登录值

使用为“SP 发起的登录 URL”和“断言使用者服务(ACS) URL”记录的值来更新租户中的单一登录值 。

更新单一登录值：

1. 在 Azure 门户中，选择“设置单一登录”窗格上“基本 SAML 配置”部分中的“编辑”  。 
1. 对于“回复 URL (断言使用者服务 URL)”，请输入之前记录的“断言使用者服务(ACS) URL”值 。
1. 对于“登录 URL”，请输入之前记录的“SP 发起的登录 URL”值 。
1. 选择“保存”。

## <a name="test-single-sign-on"></a>测试单一登录

可以从“设置单一登录”窗格测试单一登录配置。

测试 SSO：

1. 在“测试 Azure AD SAML Toolkit 1 的单一登录”部分的“设置单一登录”窗格上，选择“测试”  。
1. 使用向应用程序分配的用户帐户的 Azure AD 凭据登录应用程序。

## <a name="clean-up-resources"></a>清理资源

如果打算完成下一个快速入门，请保留创建的企业应用程序。 否则，可以考虑将其删除以清理租户。 有关详细信息，请参阅[删除应用程序](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

了解如何配置企业应用程序的属性。
> [!div class="nextstepaction"]
> [配置应用程序](add-application-portal-configure.md)
