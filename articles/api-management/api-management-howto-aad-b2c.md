---
title: 使用 Azure Active Directory B2C 为开发人员帐户授权
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中使用 Azure Active Directory B2C 为开发人员门户的用户授权
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 07/07/2021
ms.author: apimpm
ms.openlocfilehash: 6385f87eec00820d535845df21a44965e1708a55
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568104"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory B2C 为开发人员帐户授权


Azure Active Directory B2C 是一种云标识管理解决方案，适用于面向使用者的 Web 和移动应用程序。 可以使用它来管理对 API 管理开发人员门户的访问。 

本指南说明为了与 Azure Active Directory B2C 集成，需要在 API 管理服务中完成哪些配置。 如果你使用的是被弃用的旧版开发人员门户，则某些步骤会有所不同，如本文所述。

有关使用经典 Azure Active Directory 启用对开发人员门户的访问的信息，请参阅[如何使用 Azure Active Directory 为开发人员帐户授权](api-management-howto-aad.md)。

## <a name="prerequisites"></a>先决条件

* 要在其中创建应用程序的 Azure Active Directory B2C 租户。 有关详细信息，请参阅 [Azure Active Directory B2C 概述](../active-directory-b2c/overview.md)。
* 如果你还没有 API 管理服务，请完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>配置注册和登录用户流

在本部分，请在你的 Azure Active Directory B2C 租户中创建包含注册和登录策略的用户流。 有关详细步骤，请参阅[在 Azure Active Directory B2C 中创建用户流和自定义策略](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us)。

1. 在 [Azure 门户](https://portal.azure.com)中，访问你的 Azure Active Directory B2C 租户。
1. 在“策略”下，选择“用户流” > “+ 新建用户流”。 
1. 在“创建用户流”页面上，选择“注册和登录”用户流 。
1. 提供以下信息：
    1. 输入用户流的唯一名称。
    1. 在“标识提供者”中，选择“电子邮件注册”。 
    1. 在“用户属性和令牌声明”中，选择 API 管理开发人员门户所需的属性和声明（旧版开发人员门户不需要）。
         ![应用程序声明](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * 属性：名字、姓氏
        * 声明：电子邮件地址、名字、姓氏、用户的 ObjectId
1. 选择“创建”。

## <a name="configure-identity-provider-for-developer-portal"></a>为开发人员门户配置标识提供者

1. 在一个单独的 [Azure 门户](https://portal.azure.com)标签页中，导航到你的 API 管理实例。
1. 在“开发人员门户”下，选择“标识” > “+ 添加”。  
1. 在“添加标识提供者”页面中，选择“Azure Active Directory B2C”。 
1. 在“添加标识提供者”窗口中，复制“重定向 URL”。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="复制重定向 URL":::    

1. 返回到 Azure 门户中你的 Azure Active Directory B2C 租户的浏览器选项卡。 选择“应用注册” >  “+ 新建注册”。 
1. 在“注册应用程序”页面中，输入你的应用程序的注册信息。
    * 在“名称”部分，输入你选择的应用程序名称。
    * 在“支持的帐户类型”部分，选择适用于你的方案的帐户类型。 若要以广泛的客户为目标，请选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)”。 有关详细信息，请参阅[注册应用程序](../active-directory/develop/quickstart-register-app.md#register-an-application)。
    * 在“重定向 URI”中，输入你从 API 管理实例复制的重定向 URL。
    * 在“权限”中，选中“授予对 OpenID 和脱机访问权限的管理员同意”。
    * 选择“注册”以创建应用程序。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="注册新应用程序":::

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”，将该值复制到剪贴板。 

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="应用程序 ID":::
1. 切换回 API 管理的“添加标识提供者”页面，将 ID 粘贴到“客户端 ID”文本框中。
1. 切换回 B2C 应用注册。 选择“证书和机密” > “+ 新建客户端机密” 。 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="创建客户端机密"::: 
   * 在“添加客户端机密”页中，输入说明并选择“添加”。
   * 将密钥记录在一个安全的位置。 退出此页面后，此机密值永不再显示。
1. 切换回 API 管理的“添加标识提供者”页面，将密钥粘贴到“客户端机密”文本框中。
1. 切换回 B2C 应用注册。 在左侧菜单中的“管理”下，选择“身份验证” 。
    * 在“隐式授权”下，选中“访问令牌”复选框。
    * 选择“保存”。
1. 切换回 API 管理的“添加标识提供者”页。
    * 在“登录租户”中，指定 Azure Active Directory B2C 租户的域名。
    * 可以使用“颁发机构”字段控制要使用的 Azure AD B2C 登录 URL。 将值设置为“<your_b2c_tenant_name>.b2clogin.com”。
    * 在 B2C 租户策略中指定“注册策略”和“登录策略”。
    * 还可以提供“个人资料编辑策略”和“密码重置策略”。 

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Active Directory B2C 标识提供者配置":::
1. 指定所需的配置后，选择“添加”。

保存更改后，开发人员可以使用 Azure Active Directory B2C 创建新帐户并登录到开发人员门户。

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>开发人员门户 - 添加 Azure AD B2C 帐户身份验证

> [!IMPORTANT]
> 在创建或更新 Azure Active Directory B2C 配置设置时，需要[重新发布开发人员门户](api-management-howto-developer-portal-customize.md#publish)，以使更改生效。

在开发人员门户中，可以使用“登录按钮: OAuth”小组件通过 Azure AD B2C 进行登录。 此小组件已包括在默认开发人员门户内容的登录页上。

1. 若要使用 Azure Active Directory B2C 进行登录，请打开一个新的浏览器窗口并转到开发人员门户。 选择“登录”。

1. 在“登录”页面上，选择“Azure Active Directory B2C” 。

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="登录到开发人员门户":::
1. 将重定向到在上一部分中配置的注册策略。 选择在 Active Directory B2C 租户中使用你的电子邮件地址进行注册

注册完成后，将重定向回到开发人员门户。 现已登录到 API 管理服务实例的开发人员门户。

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="登录到开发人员门户的操作完成":::

尽管每当新用户使用 Azure AD B2C 登录时都会自动创建一个新帐户，但你可以考虑向注册页添加同一个小组件。

“注册表单：OAuth”小组件表示用于注册 OAuth 的表单。

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>旧开发人员门户 - 如何使用 Azure AD B2C 注册

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. 若要使用 Azure AD B2C 注册开发人员帐户，请打开新的浏览器窗口并转到旧版开发人员门户。 单击“注册”按钮。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="在旧版开发人员门户中注册":::
1. 选择使用 **Azure Active Directory B2C** 注册。

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="使用 Azure Active Directory B2C 进行注册":::

3. 将重定向到在上一部分中配置的注册策略。 选择使用电子邮件地址或现有的某个社交帐户注册。

   > [!NOTE]
   > 如果“Azure Active Directory B2C”是发布者门户上“标识”选项卡中启用的唯一选项，将直接重定向到注册策略。

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="旧版开发人员门户中的注册选项":::

   注册完成后，将重定向回到开发人员门户。 现已登录到 API 管理服务实例的开发人员门户。

## <a name="next-steps"></a>后续步骤

*  [Azure Active Directory B2C 概述]
*  [Azure Active Directory B2C：可扩展的策略框架]
*  [将 Microsoft 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 Google 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 LinkedIn 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 Facebook 帐户用作 Azure Active Directory B2C 中的标识提供者]


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C 概述]: ../active-directory-b2c/overview.md
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C：可扩展的策略框架]: ../active-directory-b2c/user-flow-overview.md
[将 Microsoft 帐户用作 Azure Active Directory B2C 中的标识提供者]: ../active-directory-b2c/identity-provider-microsoft-account.md
[将 Google 帐户用作 Azure Active Directory B2C 中的标识提供者]: ../active-directory-b2c/identity-provider-google.md
[将 Facebook 帐户用作 Azure Active Directory B2C 中的标识提供者]: ../active-directory-b2c/identity-provider-facebook.md
[将 LinkedIn 帐户用作 Azure Active Directory B2C 中的标识提供者]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
