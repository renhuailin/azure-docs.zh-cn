---
title: 使用 Microsoft Graph API 管理用户
titleSuffix: Azure AD B2C
description: 如何通过调用 Microsoft Graph API 和使用应用程序标识自动化流程来管理 Azure AD B2C 租户中的用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178868"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C 用户帐户

Microsoft Graph 允许通过在 Microsoft Graph API 中提供创建、读取、更新和删除方法来管理 Azure AD B2C 目录中的用户帐户。 可以调用 Microsoft Graph API 将现有用户存储迁移到 Azure AD B2C 租户并执行其他用户帐户管理操作。

后面的部分将演示使用 Microsoft Graph API 管理 Azure AD B2C 用户时的重要方面。 本文演示的 Microsoft Graph API 操作、类型和属性是 Microsoft Graph API 参考文档中显示的相应元素的一部分。

## <a name="register-a-management-application"></a>注册管理应用程序

在编写的任何用户管理应用程序或脚本可与 Azure AD B2C 租户中的资源交互之前，需要创建一个应用程序注册，以授权执行此类操作。

遵循本操作指南文章中的步骤创建管理应用程序可以使用的应用程序注册：

[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>用户管理 Microsoft Graph 操作

在 [Microsoft Graph API](/graph/api/resources/user) 中可以使用以下用户管理操作：

- [获取用户列表](/graph/api/user-list)
- [创建用户](/graph/api/user-post-users)
- [获取用户](/graph/api/user-get)
- [更新用户](/graph/api/user-update)
- [删除用户](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>代码示例：如何以编程方式管理用户帐户

此代码示例是一个 .NET Core 控制台应用程序，它使用 [Microsoft Graph SDK](/graph/sdks/sdks-overview) 来与 Microsoft Graph API 交互。 其中的代码演示了如何调用 API 来以编程方式管理 Azure AD B2C 租户中的用户。
可以[下载示例存档](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip)，在 GitHub 中[浏览存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)，或克隆存储库：

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

获取代码示例后，根据环境对其进行配置，然后生成项目：

1. 在 [Visual Studio](https://visualstudio.microsoft.com) 或 [Visual Studio Code](https://code.visualstudio.com) 中打开项目。
1. 打开 `src/appsettings.json`。
1. 在 `appSettings` 节中，将 `your-b2c-tenant` 替换为租户的名称，将 `Application (client) ID` 和 `Client secret` 替换为管理应用程序注册的值（请参阅本文中的[注册管理应用程序](#register-a-management-application)部分）。
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

## <a name="next-steps"></a>后续步骤

有关 Azure AD B2C 资源支持的 Microsoft Graph API 操作的完整索引，请参阅[可用于 Azure AD B2C 的 Microsoft Graph 操作](microsoft-graph-operations.md)。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
