---
title: 快速入门：为应用程序配置基于 OIDC 的单一登录
titleSuffix: Azure AD
description: 本快速入门逐步介绍在 Azure Active Directory (Azure AD) 租户中为应用程序设置基于 OIDC 的单一登录 (SSO) 的过程。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2020
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 0e1c253211cfef2d9bf55b4d0a615ccb9563036b
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669432"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-for-an-application"></a>快速入门：为应用程序配置基于 OIDC 的单一登录

通过为添加到 Azure Active Directory (Azure AD) 租户的应用程序设置单一登录 (SSO)，开始简化用户登录。 设置 SSO 后，用户可以使用其 Azure AD 凭据登录到应用程序。 SSO 在 Azure AD 免费版中提供。

若要详细了解基于 OIDC 的 SSO，请参阅[了解基于 OIDC 的单一登录](configure-oidc-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要为添加到 Azure AD 租户的应用程序设置 SSO，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 支持 SSO 且已预配置并添加到 Azure AD 库的应用程序。 大多数应用都可以使用 Azure AD 进行 SSO。 Azure AD 库中的应用是预配置的。 如果应用未列出或是自定义开发的应用，则仍可以将其与 Azure AD 一起使用。 查看目录中的教程和其他文档。 本快速入门重点介绍已针对 SSO 进行预配置并已由应用开发人员添加到 Azure AD 库的应用。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。
- 可选：完成[配置应用](add-application-portal-configure.md)。
- 可选：完成[将用户分配到应用](add-application-portal-assign-users.md)。

>[!IMPORTANT]
>使用非生产环境测试本快速入门中的步骤。

## <a name="enable-single-sign-on-for-an-app"></a>为应用启用单一登录

添加使用 OIDC 标准来实现 SSO 的应用时，你有一个设置按钮。 选择该按钮后，你将转到应用程序站点并完成应用的注册过程。 本系列前面的“添加应用”快速入门介绍了添加应用的过程。 如果要配置已添加的应用程序，请查看第一个快速入门。 它将引导你查看租户中已有的应用程序。

为应用程序配设置单一登录：

1. 本系列前面的快速入门介绍了如何添加使用 Azure AD 租户进行标识管理的应用。 如果应用开发人员使用 OIDC 标准来实现 SSO，则在添加应用时会显示一个注册按钮。

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="屏幕截图显示单一登录选项和注册按钮。" lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::

2. 选择“注册”，你将进入应用开发人员登录页面。 使用 Azure Active Directory 登录凭据进行登录。

   > [!IMPORTANT]
    > 如果已经订阅了该应用程序，则将验证用户详细信息以及租户/目录信息。 如果该应用程序无法验证用户，它会将你重定向到应用程序服务注册页面或错误页面。

3. 成功通过身份验证后，将出现一个要求管理员同意的对话框。 依次选择“代表组织同意”和“接受” 。 若要详细了解用户和管理员同意，请参阅[了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="屏幕截图显示应用的同意屏幕。" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. 系统将应用程序添加到租户中，并显示应用程序主页。

> [!TIP]
> 可使用 Graph API 自动管理应用，具体请参阅[使用 Microsoft Graph API 自动管理应用](/graph/application-saml-sso-configure-api)。

下面是一个视频，大致介绍了关于将基于 OIDC 的应用添加到 Azure AD 的其他详细信息。

在 Azure Active Directory 中添加基于 OIDC 的应用

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门系列后，请考虑删除应用以清理测试租户。 本系列中的最后一个快速入门介绍如何删除应用，请参阅[删除应用](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章了解如何删除应用。
> [!div class="nextstepaction"]
> [删除应用](delete-application-portal.md)
