---
title: 配置 Azure AD 身份验证
description: 了解如何将 Azure Active Directory 身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 61253ab7360253e1c6902b6a7b38c9fb53faa3eb
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611741"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>将应用服务或 Azure Functions 应用配置为使用 Azure AD 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何为 Azure 应用服务或 Azure Functions 配置身份验证，使应用使用 [Microsoft 标识平台](../active-directory/develop/v2-overview.md) (Azure AD) 作为身份验证提供程序为用户登录。

应用服务身份验证功能可以通过 Microsoft 标识平台自动创建应用注册。 你还可以使用你或目录管理员单独创建的注册。

- [自动创建新的应用注册](#express)
- [使用单独创建的现有注册](#advanced)

> [!NOTE]
> 用于创建新注册的选项不适用于政府云。 可以[单独定义注册](#advanced)。

## <a name="option-1-create-a-new-app-registration-automatically"></a><a name="express"> </a>选项 1：自动创建新的应用注册

此选项旨在使身份验证的启用变得简单，只需单击几下鼠标即可。

1. 登录 [Azure 门户]并导航到你的应用程序。
1. 在左侧菜单中选择“身份验证”。 单击“添加标识提供者”。
1. 在“标识提供者”下拉列表中选择“Microsoft”。 默认会选中用于创建新注册的选项。 你可以更改注册名称或支持的帐户类型。

    将创建一个客户端密码，并将其存储为一个名为 `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` 的槽粘滞[应用程序设置](./configure-common.md#configure-app-settings)。 如果你想要在 Azure Key Vault 中管理机密，稍后可以将该设置更新为使用 [Key Vault 引用](./app-service-key-vault-references.md)。

1. 如果这是为应用程序配置的第一个标识提供程序，你还将看到“应用服务身份验证设置”部分的提示。 否则你可转到下一步。
    
    这些选项可确定应用程序如何响应未经身份验证的请求，默认选择会将所有请求重定向到用此新提供程序登录。 你现在可以更改自定义此行为，也可以稍后通过选择“身份验证”设置旁边的“编辑”，在主要“身份验证”屏幕上调整这些设置。   若要详细了解这些选项，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. （可选）单击“下一步: 权限”并添加应用程序所需的任何作用域。 这些作用域将被添加到应用注册中，但你也可以稍后更改它们。
1. 单击“添加”。

现在，你可以在应用中使用 Microsoft 标识平台进行身份验证了。 该提供程序将在“身份验证”屏幕上列出。 在该屏幕上，你可以编辑或删除此提供程序配置。

有关为访问 Azure 存储和 Microsoft Graph 的 Web 应用配置 Azure AD 登录的示例，请参阅[此教程](scenario-secure-app-authentication-app-service.md)。

## <a name="option-2-use-an-existing-registration-created-separately"></a><a name="advanced"> </a>选项 2：使用单独创建的现有注册

你也可以为 Microsoft 标识平台手动注册应用程序，并通过注册详细信息自定义注册和配置应用服务身份验证。 例如，如果你要使用的应用注册来自其他 Azure AD 租户，而不是你的应用程序所在的租户，则此选项很有用。

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>在 Azure AD 中为应用服务应用创建应用注册

首先创建应用注册。 执行此操作时，请收集以下信息，稍后在应用服务应用中配置身份验证时需要用到这些信息：

- 客户端 ID
- 租户 ID
- 客户端机密（可选）
- 应用程序 ID URI

若要注册应用，请执行以下步骤：

1. 登录到 [Azure 门户]，搜索并选择“应用服务”，然后选择应用。 记下应用的 URL。 稍后要使用此 URL 来配置 Azure Active Directory 应用注册。
1. 在门户菜单中选择“Azure Active Directory”，然后转到“应用注册”选项卡并选择“新建注册”。  
1. 在“注册应用”页上的“名称”中，输入应用注册的名称。
1. 在“重定向 URI”中，选择“Web”并键入 `<app-url>/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback`。
1. 选择“注册”。
1. 在应用注册创建后，复制“应用(客户端) ID”和“目录(租户) ID”，以供稍后使用。
1. 选择“身份验证”。 在“隐式授权”下，启用“ID 令牌”以允许 OpenID Connect 用户从应用服务登录。
1. （可选）选择“品牌”。 在“主页 URL”中，输入应用服务应用的 URL，然后选择“保存”。
1. 依次选择“公开 API” > “设置”。 对于单租户应用，请粘贴应用服务应用的 URL 并选择“保存”；对于多租户应用，请粘贴基于某个租户验证域的 URL，然后选择“保存”。

   > [!NOTE]
   > 此值是应用注册的应用 ID URI。 如果 Web 应用需要访问云中的 API，则在配置云应用服务资源时，需要提供该 Web 应用的“应用程序 ID URI”。 例如，如果你希望云服务显式向该 Web 应用授予访问权限，则可以使用此 URI。

1. 选择“添加范围”。
   1. 在“范围名称”中输入 *user_impersonation*。
   1. 在文本框中，输入许可范围名称，以及希望在许可页上向用户显示的说明。 例如，输入“访问我的应用”。
   1. 选择“添加作用域”。
1. （可选）若要创建客户端机密，请选择“证书和机密” > “新建客户端机密” > “添加”。   复制页面中显示的客户端机密值。 它不会再次显示。
1. （可选）若要添加多个回复 URL，请选择“身份验证”。

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>在应用服务应用中启用 Azure Active Directory

1. 登录 [Azure 门户]并导航到你的应用程序。
1. 在左侧菜单中选择“身份验证”。 单击“添加标识提供者”。
1. 在“标识提供者”下拉列表中选择“Microsoft”。
1. 对于“应用注册类型”，可以选择“选取此目录中的现有应用注册”，这将自动收集必要的应用信息。 如果你的注册来自其他租户，或者你没有查看注册对象的权限，请选择“提供现有应用注册的详细信息”。 对于此选项，你将需要填写以下配置详细信息：

    |字段|说明|
    |-|-|
    |应用程序（客户端）ID| 使用应用注册的应用（客户端）ID。 |
    |客户端密码（可选）| 使用在应用注册中生成的客户端机密。 使用客户端密码，将使用混合流，并且应用服务将返回访问令牌和刷新令牌。 如果未设置客户端密码，则使用隐式流并仅返回 ID 令牌。 这些令牌由提供程序发送并存储在 EasyAuth 令牌存储中。|
    |颁发者 URL| 使用 `<authentication-endpoint>/<tenant-id>/v2.0`。请将其中的 *\<authentication-endpoint>* 替换为 [云环境的身份验证终结点](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints)（例如，“https://login.microsoftonline.com”表示全球 Azure），并将 *\<tenant-id>* 替换为在其中创建了应用注册的 **目录（租户）ID**。 例如，此值用于将用户重定向到相应的 Azure AD 租户，以及下载适当的元数据，以便确定相应的令牌签名密钥和令牌颁发者声明值。 对于使用 Azure AD v1 的应用程序以及对于 Azure Functions 应用，请在 URL 中省略 `/v2.0`。|
    |允许的令牌受众| 如果这是云应用或服务器应用，并且你希望允许使用 Web 应用中的身份验证令牌，请在此处添加 Web 应用的应用 ID URI。 系统始终会将配置的“客户端 ID”隐式视为允许的受众。|

    该客户端密码将存储为一个名为 `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` 的槽粘滞[应用程序设置](./configure-common.md#configure-app-settings)。 如果你想要在 Azure Key Vault 中管理机密，稍后可以将该设置更新为使用 [Key Vault 引用](./app-service-key-vault-references.md)。

1. 如果这是为应用程序配置的第一个标识提供程序，你还将看到“应用服务身份验证设置”部分的提示。 否则你可转到下一步。
    
    这些选项可确定应用程序如何响应未经身份验证的请求，默认选择会将所有请求重定向到用此新提供程序登录。 你现在可以更改自定义此行为，也可以稍后通过选择“身份验证”设置旁边的“编辑”，在主要“身份验证”屏幕上调整这些设置。   若要详细了解这些选项，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 单击“添加”。

现在，你可以在应用中使用 Microsoft 标识平台进行身份验证了。 该提供程序将在“身份验证”屏幕上列出。 在该屏幕上，你可以编辑或删除此提供程序配置。

## <a name="configure-client-apps-to-access-your-app-service"></a>配置客户端应用以访问应用服务

在上一部分，你已注册应用服务或 Azure Function 以便对用户进行身份验证。 本部分介绍如何注册本机客户端或守护程序应用，以便它们能够代表用户或自身来请求访问应用服务公开的 API。 如果你只想要对用户进行身份验证，则不需要完成本部分所述的步骤。

### <a name="native-client-application"></a>本机客户端应用程序

可以注册本机客户端，以代表已登录用户请求访问应用服务应用的 API。

1. 在 [Azure 门户]中，选择“Active Directory” > “应用注册” > “新建注册”。
1. 在“注册应用”页上的“名称”中，输入应用注册的名称。
1. 在“重定向 URI”中选择“公共客户端(移动和桌面)”，然后键入 URL `<app-url>/.auth/login/aad/callback`。  例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback`。

    > [!NOTE]
    > 对于Microsoft Store 应用程序，请改用[包 SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) 作为 URI。
1. 选择“创建”。
1. 创建应用注册后，复制“应用程序(客户端) ID”的值。
1. 选择“API 权限” > “添加权限” > “我的 API”。  
1. 选择前面为应用服务应用创建的应用注册。 如果未看到该应用注册，请确保在 [在 Azure AD 中为应用服务应用创建应用注册](#register)部分已添加 **user_impersonation** 范围。
1. 在“委托的权限”下，依次选择“user_impersonation”和“添加权限”。

现已配置一个可以代表用户请求访问应用服务应用的本机客户端应用程序。

### <a name="daemon-client-application-service-to-service-calls"></a>守护程序客户端应用程序（服务到服务的调用）

应用程序可以获取令牌，代表自身（不代表用户）调用应用服务或 Functions 应用中托管的 Web API。 此方案适用于在没有登录用户的情况下执行任务的非交互式后台程序应用程序。 它使用标准 OAuth 2.0 [客户端凭据](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)授权。

1. 在 [Azure 门户]中，选择“Active Directory” > “应用注册” > “新建注册”。
1. 在“注册应用程序”页上的“名称”中，输入后台程序应用注册的名称。
1. 对于后台应用程序，不需要“重定向 URI”，因此可将其保留为空。
1. 选择“创建”。
1. 创建应用注册后，复制“应用程序(客户端) ID”的值。
1. 选择“证书和机密” > “新建客户端机密” > “添加”。 复制页面中显示的客户端机密值。 它不会再次显示。

现在可以通过将 `resource` 参数设置为目标应用的“应用程序 ID URI”，[使用客户端 ID 和客户端机密请求访问令牌](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)。 然后，可以使用标准 [OAuth 2.0 授权标头](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)将生成的访问令牌提供给目标应用，应用服务身份验证/授权将像平常一样验证和使用该令牌，以指示调用方（在本例中是应用程序，不是用户）已进行身份验证。

目前，这允许 Azure AD 租户中的 _任何_ 客户端应用程序请求访问令牌，并向目标应用进行身份验证。 如果还想要强制 _授权_ 以只允许某些客户端应用程序，则必须执行一些附加配置。

1. 在表示要保护的应用服务或 Functions 应用的应用注册清单中[定义应用角色](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)。
1. 在表示需要获得授权的客户端的应用注册上，选择“API 权限” > “添加权限” > “我的 API”。
1. 选择之前创建的应用注册。 如果看不到应用注册，请确保已[添加应用角色](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)。
1. 在“应用程序权限”下，选择之前创建的应用角色，然后选择“添加权限”。
1. 确保单击“授予管理员同意”以授权客户端应用程序请求权限。
1. 与前面的方案（添加任何角色之前）类似，现在可以为同一目标 `resource` [请求访问令牌](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)，而访问令牌将包括一个 `roles` 声明，其中包含授权给客户端应用程序的应用角色。
1. 在目标应用服务或 Functions 应用代码中，现在可以验证令牌中是否存在预期的角色（这不是由应用服务身份验证/授权执行的）。 有关详细信息，请参阅[访问用户声明](configure-authentication-user-identities.md#access-user-claims-in-app-code)。

现已配置可以使用自己的标识访问应用服务应用的后台程序客户端应用程序。

## <a name="best-practices"></a>最佳做法

无论使用哪种配置来设置身份验证，以下最佳做法都能使租户和应用程序保持更高的安全性：

- 为每个应用服务应用提供其自身的权限和许可。
- 为每个应用服务应用配置其自身的注册。
- 避免通过对不同的部署槽使用不同的应用注册，在环境之间共享权限。 测试新代码时，这种做法有助于防止问题影响到生产应用。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [教程：在访问 Azure 存储和 Microsoft Graph 的 Web 应用中对用户进行身份验证和授权](scenario-secure-app-authentication-app-service.md)
* [教程：在 Azure 应用服务中对用户进行端到端身份验证和授权](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
