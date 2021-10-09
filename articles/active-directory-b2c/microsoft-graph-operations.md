---
title: 使用 Microsoft Graph 管理资源
titleSuffix: Azure AD B2C
description: 如何通过调用 Microsoft Graph API 和使用应用程序标识来自动化流程以管理 Azure AD B2C 租户中的资源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 216123a60a69fc696423ac9d129201201cac8fbc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739917"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C

利用 Microsoft Graph，可以管理 Azure AD B2C 目录中的资源。 支持用于管理 Azure AD B2C 资源（包括用户、标识提供者、用户流、自定义策略和策略密钥）的下列 Microsoft Graph API 操作。 以下各部分中的每个链接对应于 Microsoft Graph API 参考中该操作的相应页面。 

> [!NOTE]
> 还可以以编程方式创建 Azure AD B2C 目录本身，以及链接到 Azure 订阅的相应 Azure 资源。 此功能不是通过 Microsoft Graph API 公开的，而是通过 Azure REST API 公开的。 有关详细信息，请参阅 [B2C 租户 - 创建](/rest/api/activedirectory/b2ctenants/create)。

观看此视频，了解如何使用 Microsoft Graph API 迁移 Azure AD B2C 用户。

>[!Video https://www.youtube.com/embed/9BRXBtkBzL4]

## <a name="prerequisites"></a>先决条件

若要使用 MS Graph API 并与 Azure AD B2C 租户中的资源交互，需要进行应用程序注册来获取这样做的权限。 请执行[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md) 一文中的步骤，以便创建供管理应用程序使用的应用程序注册。 

## <a name="user-management"></a>用户管理

- [列出用户](/graph/api/user-list)
- [创建使用者用户](/graph/api/user-post-users)
- [获取用户](/graph/api/user-get)
- [更新用户](/graph/api/user-update)
- [删除用户](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>用户电话号码管理（beta 版本）

一种电话号码，用户可以使用它来通过[短信或语音呼叫](sign-in-options.md#phone-sign-in)或[多重身份验证](multi-factor-authentication.md)进行登录。 有关详细信息，请参阅 [Azure AD 身份验证方法 API](/graph/api/resources/phoneauthenticationmethod)。

- [添加](/graph/api/authentication-post-phonemethods)
- [列表](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [更新](/graph/api/phoneauthenticationmethod-update)
- [删除](/graph/api/phoneauthenticationmethod-delete)

请注意，[列出](/graph/api/authentication-list-phonemethods)操作仅返回已启用的电话号码。 应启用以下电话号码来与列表操作一起使用。 

![启用电话登录](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> 在当前 beta 版本中，此 API 仅在以下情况下运行：在国家/地区代码和电话号码之间加入空格的形式存储了电话号码。 默认情况下，Azure AD B2C 服务目前不添加此空间。

## <a name="self-service-password-reset-email-address-beta"></a>自助服务密码重置电子邮件地址（beta 版本）

一种电子邮件地址，[用户名登录帐户](sign-in-options.md#username-sign-in)可以使用它来重置密码。 有关详细信息，请参阅 [Azure AD 身份验证方法 API](/graph/api/resources/emailauthenticationmethod)。

- [添加](/graph/api/emailauthenticationmethod-post)
- [列表](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [更新](/graph/api/emailauthenticationmethod-update)
- [删除](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>标识提供者

管理 Azure AD B2C 租户中的用户流可用的[标识提供者](add-identity-provider.md)。

- [列出 Azure AD B2C 租户中注册的标识提供者](/graph/api/identityprovider-list)
- [创建标识提供者](/graph/api/identityprovider-post-identityproviders)
- [获取标识提供者](/graph/api/identityprovider-get)
- [更新标识提供者](/graph/api/identityprovider-update)
- [删除标识提供者](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>用户流

配置用于注册、登录、组合式注册和登录、密码重置和配置文件更新的预先生成的策略。

- [列出用户流](/graph/api/identitycontainer-list-b2cuserflows)
- [创建用户流](/graph/api/identitycontainer-post-b2cuserflows)
- [获取用户流](/graph/api/b2cidentityuserflow-get)
- [删除用户流](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>用户流身份验证方法（beta 版本）

选择允许用户通过本地帐户进行注册的机制。 本地帐户是 Azure AD 对其进行标识断言的帐户。 有关详细信息，请参阅 [b2cAuthenticationMethodsPolicy 资源类型](/graph/api/resources/b2cauthenticationmethodspolicy)。

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [更新](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>自定义策略

下列操作可用于管理你的 Azure AD B2C 信任框架策略（称为[自定义策略](custom-policy-overview.md)）。

- [列出在租户中配置的所有信任框架策略](/graph/api/trustframework-list-trustframeworkpolicies)
- [创建信任框架策略](/graph/api/trustframework-post-trustframeworkpolicy)
- [读取现有信任框架策略的属性](/graph/api/trustframeworkpolicy-get)
- [更新或创建信任框架策略](/graph/api/trustframework-put-trustframeworkpolicy)
- [删除现有信任框架策略](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>策略密钥

Identity Experience Framework 存储着自定义策略中引用的机密，以在组件之间建立信任关系。 这些机密可以是对称或非对称密钥/值。 在 Azure 门户中，这些实体显示为 **策略密钥**。

Microsoft Graph API 中策略密钥的顶层资源是[信任的框架密钥集](/graph/api/resources/trustframeworkkeyset)。 每个 **密钥集** 都包含至少一个 **密钥**。 若要创建密钥，请先创建一个空的密钥集，然后在密钥集中生成一个密钥。 你可以创建手动机密、上传证书或 PKCS12 密钥。 密钥可以是某个生成的机密、某个字符串（例如 Facebook 应用程序机密），或者是你上传的某个证书。 如果密钥集具有多个密钥，则只有其中一个密钥处于活动状态。

### <a name="trust-framework-policy-keyset"></a>信任框架策略密钥集

- [列出信任框架密钥集](/graph/api/trustframework-list-keysets)
- [创建信任框架密钥集](/graph/api/trustframework-post-keysets)
- [获取密钥集](/graph/api/trustframeworkkeyset-get)
- [更新信任框架密钥集](/graph/api/trustframeworkkeyset-update)
- [删除信任框架密钥集](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任框架策略密钥

- [获取密钥集中当前处于活动状态的密钥](/graph/api/trustframeworkkeyset-getactivekey)
- [在密钥集中生成密钥](/graph/api/trustframeworkkeyset-generatekey)
- [上传基于字符串的机密](/graph/api/trustframeworkkeyset-uploadsecret)
- [上传 X.509 证书](/graph/api/trustframeworkkeyset-uploadcertificate)
- [上传 PKCS12 格式的证书](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>应用程序

- [列出应用程序](/graph/api/application-list)
- [创建应用程序](/graph/api/resources/application)
- [更新应用程序](/graph/api/application-update)
- [创建 ServicePrincipal](/graph/api/resources/serviceprincipal)
- [创建 oauth2Permission 授权](/graph/api/resources/oauth2permissiongrant)
- [删除应用程序](/graph/api/application-delete)

## <a name="application-extension-properties"></a>应用程序扩展属性

- [列出扩展属性](/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一个目录，其中可以保存每个用户的 100 个自定义属性。 对于用户流，[可使用 Azure 门户管理](user-flow-custom-attributes.md)这些扩展属性。 对于自定义策略，Azure AD B2C 在策略首次向扩展属性中写入值时替你创建属性。

## <a name="audit-logs"></a>审核日志

- [列出审核日志](/graph/api/directoryaudit-list)

若要详细了解如何访问 Azure AD B2C 审核日志，请参阅[访问 Azure AD B2C 审核日志](view-audit-logs.md)。

## <a name="conditional-access"></a>条件性访问

- [列出所有条件访问策略](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [读取条件访问策略的属性和关系](/graph/api/conditionalaccesspolicy-get)
- [创建新的条件访问策略](/graph/api/resources/application)
- [更新条件访问策略](/graph/api/conditionalaccesspolicy-update)
- [删除条件访问策略](/graph/api/conditionalaccesspolicy-delete)

## <a name="how-to-programmatically-manage-microsoft-graph"></a>如何以编程方式管理 Microsoft Graph

需要管理 Microsoft Graph 时，可以使用应用程序权限将其作为应用程序进行管理，也可以使用委托的权限。 对于委托的权限，需要用户或管理员同意应用请求的权限。 当应用调用目标资源时，它被委托了充当已登录用户的权限。 应用程序权限由不需要登录用户在场的应用使用，由于这些应用不需要登录用户在场，因此需要应用程序权限。 因此，只有管理员才能同意应用程序权限。

> [!NOTE]
> 通过用户流或自定义策略登录的用户的委托权限不能针对 Microsoft Graph 的委托权限使用。
## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>代码示例：如何以编程方式管理用户帐户

此代码示例是一个 .NET Core 控制台应用程序，它使用 [Microsoft Graph SDK](/graph/sdks/sdks-overview) 来与 Microsoft Graph API 交互。 其中的代码演示了如何调用 API 来以编程方式管理 Azure AD B2C 租户中的用户。
可以[下载示例存档](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip)，在 GitHub 中[浏览存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)，或克隆存储库：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

获取代码示例后，根据环境对其进行配置，然后生成项目：

1. 在 [Visual Studio](https://visualstudio.microsoft.com) 或 [Visual Studio Code](https://code.visualstudio.com) 中打开项目。
1. 打开 `src/appsettings.json`。
1. 在 `appSettings` 部分，将 `your-b2c-tenant` 替换为租户的名称，将 `Application (client) ID` 和 `Client secret` 替换为管理应用程序注册的值。 有关详细信息，请参阅[注册 Microsoft Graph 应用程序](microsoft-graph-get-started.md)。
1. 在存储库的本地克隆中打开控制台窗口，切换到 `src` 目录，然后生成项目：

    ```console
    cd src
    dotnet build
    ```
    
1. 使用 `dotnet` 命令运行应用程序：

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

应用程序将显示可执行的命令列表。 例如，获取所有用户、获取单个用户、删除用户、更新用户的密码和批量导入。

> [!NOTE]
> 要使应用程序能够更新用户帐户密码，需要向应用程序[授予用户管理员角色](microsoft-graph-get-started.md#optional-grant-user-administrator-role)。
 
### <a name="code-discussion"></a>代码探讨

示例代码使用 [Microsoft Graph SDK](/graph/sdks/sdks-overview)，旨在简化可访问 Microsoft Graph 的优质、高效且可复原的应用程序的生成。

对 Microsoft Graph API 发出的任何请求都需要使用访问令牌进行身份验证。 该解决方案利用 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 包，该包提供 Microsoft 身份验证库 (MSAL) 的基于身份验证方案的包装器，以便与 Microsoft Graph SDK 配合使用。

_Program.cs_ 文件中的 `RunAsync` 方法：

1. 从 _appsettings.json_ 文件读取应用程序设置
1. 使用 [OAuth 2.0 客户端凭据授予](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)流初始化身份验证提供程序。 应用可以使用客户端凭据授予流获取用于调用 Microsoft Graph API 的访问令牌。
1. 使用身份验证提供程序设置 Microsoft Graph 服务客户端：

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

然后，在 _UserService.cs_ 中使用初始化的 *GraphServiceClient* 来执行用户管理操作。 例如，获取租户中的用户帐户列表：

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[使用 Microsoft Graph SDK 发出 API 调用](/graph/sdks/create-requests)中介绍了如何在 Microsoft Graph 中读取和写入信息，使用 `$select` 控制返回的属性，提供自定义查询参数，以及使用 `$filter` 和 `$orderBy` 查询参数。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
