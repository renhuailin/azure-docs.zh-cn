---
title: 使用 Azure Active Directory 为开发人员帐户授权
titleSuffix: Azure API Management
description: 了解如何在 API 管理中使用 Azure Active Directory 授权用户。
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: 03ac79a70a1725fd6d1ceca6d79d4cdb325a8c51
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428457"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户

本文将指导如何进行以下操作：
> [!div class="checklist"]
> * 为 Azure Active Directory (Azure AD) 中的用户启用对开发人员门户的访问。
> * 通过添加包含用户的外部组管理 Azure AD 用户组。

## <a name="prerequisites"></a>先决条件

- 完成[创建 Azure API 管理实例](get-started-create-service-instance.md)快速入门。
- [导入并发布](import-and-publish.md) Azure API 管理实例。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 为开发人员帐户授权

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 选择 ![箭头图标。](./media/api-management-howto-aad/arrow.png).
1. 搜索并选择“API 管理服务”。
1. 选择自己的 API 管理服务实例。
1. 在“开发人员门户”下，选择“标识” 。
1. 选择顶部的“+添加”，在右侧打开“添加标识提供者”窗格 。
1. 在“类型”下，从下拉菜单中选择“Azure Active Directory” 。
    * 选择后，可以输入其他所需信息。 
    * 信息包括“客户端 ID”和“客户端机密” 。 
    * 请参阅本文稍后介绍的有关这些控件的详细信息。
1. 保存“重定向 URL”供稍后使用。
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="在 Azure 门户中添加标识提供者":::

    > [!NOTE]
    > 有两个重定向 URL：<br/>
    > * 重定向 URL 指向最新的 API 管理开发人员门户。
    > * 重定向 URL（已弃用的门户）指向已弃用的 API 管理开发人员门户。
    >
    > 建议使用最新的开发人员门户重定向 URL。
   
1. 在浏览器上的新标签页中打开 Azure 门户。 
1. 导航到“[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)”以在 Active Directory 中注册应用。
1. 选择“新注册”。 在“注册应用程序”页上，将值设置如下：
    
    * 将“名称”设置为一个有意义的名称。 例如，*developer-portal*
    * 将“支持的帐户类型”设置为“仅限此组织目录中的帐户”。  
    * 将“重定向 URI”设置为在步骤 9 中保存的值。 
    * 选择“注册”  。 

1.  注册应用程序之后，从“概述”页复制“应用程序(客户端) ID” 。 
1. 切换到包含你的 API 管理实例的浏览器标签页。 
1. 在“添加标识提供者”窗口中，将“应用程序(客户端) ID”值粘贴到“客户端 ID”框中。
1. 切换到包含“应用注册”的浏览器标签页。
1. 选择适当的应用注册。
1. 在边侧菜单的“管理”部分下，选择“证书和机密” 。 
1. 在“证书和机密”页中，选择“客户端机密”下的“新建客户端机密”按钮  。 
    * 输入“说明”。
    * 为“过期”选择任一选项。
    * 选择“添加”  。 
1. 在离开页面之前复制客户端的“机密 ID”。 稍后需要用到此值。 
1. 在边侧菜单中的“管理”下，选择“身份验证” 。
1. 在“隐式授权和混合流”部分下，选中“ID 令牌”复选框 。
1. 切换到包含你的 API 管理实例的浏览器标签页。 
1. 将机密粘贴到“添加标识提供者”窗格的“客户端机密”字段中 。

    > [!IMPORTANT]
    > 在密钥过期之前更新“客户端机密”。 

1. 在“添加标识提供者”窗格的“允许的租户”字段中，指定要向其授予 API 管理服务实例 API 访问权限的 Azure AD 实例域 。 
    * 可使用换行符、空格或逗号分隔多个域。

    > [!NOTE]
    > 可在“允许的租户”部分中指定多个域。 全局管理员必须先向应用程序授予对目录数据的访问权限，然后用户才能从非原始应用注册域登录。 若要授予权限，全局管理员应：
    > 1. 转到 `https://<URL of your developer portal>/aadadminconsent`（例如 `https://contoso.portal.azure-api.net/aadadminconsent`）。
    > 1. 输入全局管理员要向其授予访问权限的 Azure AD 租户的域名。
    > 1. 选择“提交”。 

1.  指定所需配置后，选择“添加”。

保存更改后，指定的 Azure AD 实例中的用户可以[使用 Azure AD 帐户登录到开发人员门户](#log_in_to_dev_portal)。

## <a name="add-an-external-azure-ad-group"></a>添加外部 Azure AD 组

为 Azure AD 租户中的用户启用访问权限后，可以：
* 将 Azure AD 组添加到 API 管理中。 
* 使用 Azure AD 组控制产品可见性。

默认情况下，在[上一部分](#authorize-developer-accounts-by-using-azure-ad)中注册的应用程序可以使用所需的 `User.Read` 委托权限访问 Microsoft Graph API。 执行以下步骤，向应用程序授予使用 `Directory.Read.All` 应用程序权限访问 Microsoft Graph API 和 Azure AD Graph API 的权限： 

1. 导航到在上一部分中创建的应用注册。
2. 在边侧菜单中的“管理”下，选择“API 权限” 。
1. 选择“添加权限”。 
1. 在“请求 API 权限”窗格中：
    1. 选择“Microsoft API”选项卡。
    1. 选择“Microsoft Graph”磁贴。 
    1. 选择“应用程序权限”并搜索“目录”。 
    1. 选择“Directory.Read.All”权限。 
    1. 选择窗格底部的“添加权限”。
1. 选择“添加权限”以添加另一个权限。 
1. 在“请求 API 权限”窗格中：
    1. 选择“Microsoft API”选项卡。
    1. 向下滚动到“支持的旧版 API”部分。
    1. 选择“Azure Active Directory Graph”磁贴。 
    1. 选择“应用程序权限”并搜索“目录”。 
    1. 选择“Directory.Read.All”权限。
    1. 选择“添加权限”。 
1. 选择“为 {tenantname} 授予管理员同意”，以便为此目录中的所有用户授予访问权限。 

现在可以从 API 管理实例的“组”选项卡添加外部 Azure AD 组。

1. 在边侧菜单中的“开发人员门户”下，选择“组” 。
1. 选择“添加 Azure AD 组”按钮。

   ![“添加 AAD 组”按钮](./media/api-management-howto-aad/api-management-with-aad008.png)
1. 从下拉菜单中选择“租户”。 
1. 搜索并选择要添加的组。
1. 按“选择”按钮。

添加外部 Azure AD 组后，可以查看和配置其属性： 
1. 从“组”选项卡中选择该组的名称。 
1. 编辑该组的“名称”和“说明”信息 。
 
配置的 Azure AD 实例中的用户现在可以：
* 登录到开发人员门户。 
* 查看和订阅他们可以看到的任何组。

> [!NOTE]
> 在 [Microsoft 标识平台中的权限和同意](../active-directory/develop/v2-permissions-and-consent.md#permission-types)一文中详细了解“委托”权限类型和“应用程序”权限类型之间的差异 。

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> 开发人员门户：添加 Azure AD 帐户身份验证

在开发人员门户中，可以使用默认开发人员门户内容的登录页上包含的“登录按钮: OAuth”小组件登录到 Azure AD 中。

尽管当新用户在 Azure AD 中登录时会自动创建一个新帐户，还是请考虑向注册页添加同一个小组件。 “注册表单：OAuth”小组件表示用于注册 OAuth 的表单。

> [!IMPORTANT]
> 需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使 Azure AD 更改生效。

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>旧版开发人员门户：如何在 Azure AD 中登录

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

若要使用在前面部分中配置的 Azure AD 帐户登录到开发人员门户，请执行以下操作：

1. 使用 Active Directory 应用程序配置中的登录 URL 打开新的浏览器窗口。 
1. 选择“Azure Active Directory” 。

   ![登录页][api-management-dev-portal-signin]

1. 输入 Azure AD 中某个用户的凭据。
1. 选择“登录”。

   ![使用用户名和密码登录][api-management-aad-signin]

1. 如果系统提示你向注册表单输入信息，请在其中填写任何附加信息。 
1. 选择“注册”  。

   ![注册窗体中的“注册”按钮][api-management-complete-registration]

用户现已登录到 API 管理服务实例的开发人员门户。

![完成注册后的开发人员门户][api-management-registration-complete]

## <a name="next-steps"></a>后续步骤

- 了解如何[在 API 管理中使用 OAuth 2.0 授权和 Azure AD 保护 Web API 后端](./api-management-howto-protect-backend-with-aad.md)
- 详细了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)。
- 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
- 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](./api-management-howto-mutual-certificates.md)。
- [创建 API 管理服务实例](./get-started-create-service-instance.md)。
- [管理第一个 API](./import-and-publish.md)。

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
