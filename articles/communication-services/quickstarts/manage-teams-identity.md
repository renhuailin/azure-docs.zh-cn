---
title: 设置和创建 Teams 访问令牌
titleSuffix: An Azure Communication Services quickstart
description: 生成提供 Teams 访问令牌的服务
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9e58659cfaa5b459a28278362aac002a1a87db5
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223853"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>快速入门：设置和管理 Teams 访问令牌

> [!IMPORTANT]
> 若要启用/禁用自定义 Teams 终结点体验，请完成[此表单](https://forms.office.com/r/B8p5KqCH19)。

在本快速入门中，我们将生成一个 .NET 控制台应用程序，以使用 MSAL 库对 AAD 用户令牌进行身份验证。 然后，我们将使用 Azure 通信服务标识 SDK 将该令牌交换为 Teams 访问令牌。 然后 Azure 通信服务调用 SDK 可以使用 Teams 访问令牌来生成自定义 Teams 终结点。

> [!NOTE]
> 在生产环境中，我们建议在后端服务中实现此交换机制，因为交换请求是使用机密签名的。


## <a name="prerequisites"></a>先决条件
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](./create-communication-resource.md)。
- 通过[此表单](https://forms.office.com/r/B8p5KqCH19)启用自定义 Teams 终结点体验
- Azure Active Directory 中的用户拥有 Teams 许可证

## <a name="introduction"></a>简介

Teams 标识已绑定到 Azure Active Directory 中的租户。 来自同一租户或任何租户的用户可以使用你的应用程序。 在本快速入门中，我们将使用多个参与者（来自虚构公司 Contoso 和 Fabrikam 的用户、开发人员和管理员）完成多租户用例。 在此用例中，Contoso 是一家为 Fabrikam 构建 SaaS 解决方案的公司。 

以下部分将指导你完成管理员、开发人员和用户的步骤。 这些关系图演示了多租户用例。 如果使用单个租户，请从单个租户中的 Contoso 和 Fabrikam 执行所有步骤。

## <a name="admin-actions"></a>管理员操作

管理员角色在 AAD 中具有扩展权限。 此角色的成员可以预配资源，以及从 Azure 门户读取信息。 在下图中，可以看到必须由管理员执行的所有操作。

![用于启用自定义 Teams 终结点体验的管理员操作](./media/teams-identities/teams-identity-admin-overview.png)

1. Contoso 的管理员在 Azure Active Directory 中创建或选择现有应用程序。 属性“支持的帐户类型”定义不同租户中的用户是否可以向应用程序进行身份验证。 属性“重定向 URI”将成功的身份验证请求重定向到 Contoso 的服务器。
1. Contoso 的管理员使用 Azure 通信服务的 VoIP 权限扩展应用程序的清单。 
1. Contoso 的管理员允许应用程序使用公共客户端流
1. Contoso 的管理员可以选择性更新
1. Contoso 的管理员通过[此表单](https://forms.office.com/r/B8p5KqCH19)启用体验
1. Contoso 的管理员创建或选择用于对交换请求进行身份验证的现有通信服务。 将 AAD 用户令牌交换为 Teams 访问令牌。 可在此处详细了解如何创建[新的 Azure 通信服务资源](./create-communication-resource.md)。
1. Fabrikam 的管理员为 Fabrikam 租户中的 Azure 通信服务预配新的服务主体
1. Fabrikam 的管理员为 Contoso 的应用程序授予 Azure 通信服务 VoIP 权限。 只有在 Contoso 的应用程序未经验证时，才需要执行此步骤。

### <a name="1-create-aad-application-registration-or-select-aad-application"></a>1. 创建 AAD 应用程序注册或选择 AAD 应用程序 

用户必须使用 Azure 通信服务的 `VoIP` 权限针对 AAD 应用程序进行身份验证。 如果没有要用于本快速入门的现有应用程序，可以创建新的应用程序注册。 

以下应用程序设置会影响体验：
- 属性“支持的帐户类型”定义应用程序是单租户（“仅此组织目录中的帐户”）还是多租户（“任何组织目录中的帐户”）。 对于这种情况，你可以使用此替代方法。
- 重定向 URI 定义身份验证请求在身份验证后重定向的 URI。 对于此方案，可以使用“公共客户端/本机(移动和桌面)”并填写“http://localhost”作为 URI。

[可在此处找到详细的文档](/azure/active-directory/develop/quickstart-register-app#register-an-application)。 

注册应用程序后，你将在概述中看到标识符。 以下步骤中将使用此标识符：应用程序(客户端)ID。

### <a name="2-allow-public-client-flows"></a>2. 允许公共客户端流

在应用程序的“身份验证”窗格中，可以看到为“公共客户端/本机(移动和桌面)”配置的平台，其中重定向 URI 指向 localhost。 在屏幕底部，可以找到开关“允许公共客户端流”，在本快速入门中其将设置为“是”。

### <a name="3-update-publisher-domain-optional"></a>3. 更新发布者域（可选）
在“品牌打造”窗格中，可以更新应用程序的发布者域。 这适用于多租户应用程序，其中应用程序将被标记为已由 Azure 验证。 可在[此处](/azure/active-directory/develop/howto-configure-publisher-domain)找到有关如何验证发布者以及如何更新应用程序域的详细信息。

### <a name="4-define-azure-communication-services-voip-permission-in-application"></a>4. 在应用程序中定义 Azure 通信服务的 VoIP 权限

转到应用程序的详细信息，然后选择“清单”窗格。 在清单中查找名为“requiredResourceAccess”的属性。 它是对象数组，用于定义应用程序的权限。 使用第一方应用程序 Azure 通信服务的 VoIP 权限扩展清单。 将以下对象添加到数组。

> [!NOTE] 
> 请勿更改代码片段中的 GUID，因为它们唯一标识应用程序和权限。

```json
{
   "resourceAppId": "1fd5118e-2576-4263-8130-9503064c837a",
   "resourceAccess": [
      {
         "id": "31f1efa3-6f54-4008-ac59-1bf1f0ff9958",
         "type": "Scope"
      }
   ]
}
```

然后选择“保存”按钮以保留更改。 现在可以在“API 权限”窗格中看到“Azure 通信服务 - VoIP”权限。

### <a name="5-enable-custom-teams-endpoint-experience-for-application"></a>5. 为应用程序启用自定义 Teams 终结点体验

AAD 管理员填写以下[表单](https://forms.office.com/r/B8p5KqCH19)，为应用程序启用自定义 Teams 终结点体验。

### <a name="6-create-or-select-communication-services-resource"></a>6. 创建或选择通信服务资源

你的 Azure 通信服务资源用于对将 AAD 用户令牌交换为 Teams 访问令牌的所有请求进行身份验证。 可以通过使用访问密钥或 Azure RBAC 进行身份验证的 Azure 通信服务标识 SDK 触发此交换。 可以在 Azure 门户中获取访问密钥，或者通过“访问控制(IAM)”窗格配置 Azure RBAC。

如果要[创建新的通信服务资源，请按照本指南操作](./create-communication-resource.md)。

### <a name="7-provision-azure-communication-services-service-principal"></a>7. 预配 Azure 通信服务的服务主体

若要在 Fabrikam 的租户中启用自定义 Teams 终结点体验，Fabrikam 的 AAD 管理员必须使用应用程序 ID 1fd5118e-2576-4263-8130-9503064c837a 预配名为 Azure 通信服务的服务主体。 如果在 Azure Active Directory 的“企业应用程序”窗格中看不到此应用程序，则必须手动添加该应用程序。

Fabrikam 的 AAD 管理员通过 PowerShell 连接到 Azure 的租户。 

> [!NOTE]
> 将 [Tenant_ID] 替换为租户的 ID，此 ID 可在 Azure 门户的 AAD 概述页上找到。

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

如果找不到命令，则 PowerShell 中未安装 AzureAD 模块。 关闭 PowerShell，然后使用“管理”权限运行它。 然后，可以使用以下命令安装 Azure-AD 包：

```azurepowershell
Install-Module AzureAD
```

连接到 Azure 并进行身份验证后，运行以下命令来预配通信服务的服务主体。 

> [!NOTE]
> 参数 AppId 是指第一方应用程序 Azure 通信服务。 请勿更改此值。

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="8-provide-admin-consent"></a>8. 提供管理员同意

如果 Contoso 的应用程序未经验证，AAD 管理员必须向 Contoso 应用程序授予 Azure 通信服务 VoIP 权限。 Fabrikam 的 AAD 管理员通过唯一链接提供同意。 若要构造管理员同意链接，请按照说明操作：

1. 单击以下链接 *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}*
1. 将 {Tenant_ID} 替换为 Fabrikam 的租户 ID
1. 将 {Application_ID} 替换为 Contoso 的应用程序 ID
1. Fabrikam 的 AAD 管理员在浏览器中导航到此链接。 
1. Fabrikam 的 AAD 管理员登录并代表组织授予权限

如果授予了同意，则在 Fabrikam 租户中创建 Contoso 应用程序的服务主体。 Fabrikam 的管理员可以在 AAD 中查看同意：

1. 以管理员身份登录到 Azure 门户
1. 转到 Azure Active Directory
1. 转到“企业应用程序”窗格
1. 将筛选器“应用程序类型”设置为“所有应用程序”
1. 在用于筛选应用程序的字段中，插入 Contoso 应用程序的名称
1. 选择“应用”以筛选结果
1. 选择具有所需名称的服务主体 
1. 转到“权限”窗格

可以看到，Azure 通信服务的 VoIP 权限现在状态为“已为 {Directory_name} 授予”。

## <a name="developer-actions"></a>开发人员操作

Contoso 的开发人员需要设置客户端应用程序以对用户进行身份验证。 然后，开发人员需要在后端服务器上创建终结点，以在重定向后处理 AAD 用户令牌。 收到 AAD 用户令牌时，它会交换为 Teams 访问令牌，并返回到客户端应用程序。 下图显示了需要开发人员执行的操作：

![用于启用自定义 Teams 终结点体验的开发人员操作](./media/teams-identities/teams-identity-developer-overview.png)

1. Contoso 的开发人员将 MSAL 库配置为对具有 Azure 通信服务 VoIP 权限的管理员在之前步骤中创建的应用程序的用户进行身份验证
1. Contoso 的开发人员将初始化 ACS 标识 SDK，并通过 SDK 将传入的 AAD 用户令牌交换为 Teams 访问令牌。 Teams 访问令牌随后将返回到客户端应用程序。

借助 Microsoft 身份验证库 (MSAL)，开发人员能够从 Microsoft 标识平台终结点获取 AAD 用户令牌，以便对用户进行身份验证并访问安全的 Web API。 它可用于提供对 Azure 通信服务的安全访问。 MSAL 支持许多不同的应用程序体系结构和平台，包括 .NET、JavaScript、Java、Python、Android 和 iOS。

可以在公开文档中找到有关如何设置不同环境的详细信息。 [Microsoft 身份验证库 (MSAL) 概述](/azure/active-directory/develop/msal-overview)。

> [!NOTE]
> 以下部分介绍如何为 .NET 中的控制台应用程序将 AAD 访问令牌交换为 Teams 访问令牌。

### <a name="create-new-application"></a>新建应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 dotnet new 命令创建名为 TeamsAccessTokensQuickstart 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

将目录更改为新创建的应用文件夹，并使用 dotnet build 命令编译应用程序。

```console
cd TeamsAccessTokensQuickstart
dotnet build
```
#### <a name="install-the-package"></a>安装包
同样，在应用程序目录中，使用 dotnet add package 命令安装适用于 .NET 包的 Azure 通信服务标识库。

```console
dotnet add package Azure.Communication.Identity
dotnet add package Microsoft.Identity.Client
```

#### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

- 在文本编辑器中打开 Program.cs 文件
- 添加 using 指令以包含以下命名空间： 
    - Azure.Communication
    - Azure.Communication.Identity
    - Microsoft.Identity.Client
- 更新 Main 方法声明以支持异步代码

使用以下代码以开始执行以下操作：

```csharp
using System;
using System.Text;
using Azure.Communication;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services – Teams access tokens quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="1-receive-aad-user-token-via-msal-library"></a>1. 通过 MSAL 库接收 AAD 用户令牌

使用 MSAL 库对具有 Azure 通信服务 VoIP 权限的 Contoso 应用程序 AAD 进行身份验证。 在公有云（参数 authority）中为 Contoso 的应用程序（参数 applicationId）配置客户端。 AAD 用户令牌将返回到重定向 URI（参数 redirectUri）。 凭据将取自交互式弹出窗口，该弹出窗口将在默认浏览器中打开。

> [!NOTE] 
> 重定向 URI 必须与应用程序中定义的值匹配。 请查看管理员指南中的第一步，了解如何配置重定向 URI。

```csharp
const string applicationId = "Contoso's_Application_ID";
const string authority = "https://login.microsoftonline.com/common";
const string redirectUri = "http://localhost";

var client = PublicClientApplicationBuilder
                .Create(applicationId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

const string scope = "https://auth.msft.communication.azure.com/VoIP";

var aadUserToken = await client.AcquireTokenInteractive(new[] { scope }).ExecuteAsync();

Console.WriteLine("\nAuthenticated user: " + aadUserToken.Account.Username);
Console.WriteLine("AAD user token expires on: " + aadUserToken.ExpiresOn);
```

变量 aadUserToken 现在带有有效的 Azure Active Directory 用户令牌，用于交换。

### <a name="2-exchange-aad-user-token-for-teams-access-token"></a>2. 将 AAD 用户令牌交换为 Teams 访问令牌

有效的 AAD 用户令牌针对具有 Azure 通信服务 VoIP 权限的第三方 AAD 对用户进行身份验证。 以下代码使用 ACS 标识 SDK，以便将 AAD 用户令牌交换为 Teams 访问令牌。

> [!NOTE]
> 将值“&lt;Connection-String&gt;”替换为有效的连接字符串或使用 Azure RBAC 进行身份验证。 可在[本快速入门](./access-tokens.md)中找到更多详细信息。

```csharp
var identityClient = new CommunicationIdentityClient("<Connection-String>");
var teamsAccessToken = identityClient.ExchangeTeamsToken(aadUserToken.AccessToken);

Console.WriteLine("\nTeams access token expires on: " + teamsAccessToken.Value.ExpiresOn);
```

如果满足要求中定义的所有条件，则你将获得 24 小时内有效的 Teams 访问令牌。

#### <a name="run-the-code"></a>运行代码
使用 dotnet run 命令从应用程序目录中运行应用程序。

```console
dotnet run
```

应用的输出描述每个已完成的操作：

```console
Azure Communication Services - Teams access tokens quickstart

Authenticated user: john.smith@contoso.com
AAD user token expires on: 6/10/2021 10:13:17 AM +00:00

Teams access token expires on: 6/11/2021 9:13:18 AM +00:00
```

## <a name="user-actions"></a>用户操作

用户表示 Contoso 应用程序的 Fabrikam 用户。 下图显示了用户体验。

![用于启用自定义 Teams 终结点体验的用户操作](./media/teams-identities/teams-identity-user-overview.png)

1. Fabrikam 的用户使用 Contoso 的客户端应用程序，并提示进行身份验证。
1. 针对具有 Azure 通信服务 VoIP 权限的 Contoso 应用程序的 Fabrikam Azure Active Directory 租户，Contoso 的客户端应用程序使用 MSAL 库对用户进行身份验证。 
1. 身份验证将重定向到服务器，如 MSAL 和 Contoso 的应用程序中的“重定向 URI”属性所定义
1. Contoso 的服务器使用 ACS 标识 SDK 将 AAD 用户令牌交换为 Teams 访问令牌，并将 Teams 访问令牌返回到客户端应用程序。

使用客户端应用程序中的有效 Teams 访问令牌，开发人员可以集成 ACS 调用 SDK 并生成自定义Teams 终结点。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 在 Azure Active Directory 中创建和配置应用程序
> * 使用 MSAL 库颁发 Azure Active Directory 用户令牌
> * 使用 ACS 标识 SDK 将 Azure Active Directory 用户令牌交换为 Teams 访问令牌

你可能会对下列文档感兴趣：

- 了解[自定义 Teams 终结点](../concepts/teams-endpoint.md)
- 了解 [Teams 互操作性](../concepts/teams-interop.md)
