---
title: 通过应用程序进行身份验证 - Azure 中继（预览版）
description: 本文介绍如何使用 Azure Active Directory 对应用程序进行身份验证以访问 Azure 中继资源。
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654152"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>使用 Azure Active Directory 对应用程序进行身份验证和授权，使之能够访问 Azure 中继实体（预览版）
Azure 中继支持使用 Azure Active Directory (Azure AD) 对 Azure 中继实体（混合链接、WCF 中继）请求授权。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (Azure RBAC) 授予对安全主体的访问权限，该安全主体可能是用户、组或应用程序服务主体。 若要详细了解角色和角色分配，请参阅[了解不同的角色](../role-based-access-control/overview.md)。   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>通过应用进行身份验证
将 Azure AD 与 Azure 中继配合使用的主要优势之一在于，不再需要在代码中存储凭据。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 中继请求授权。

以下部分介绍如何配置控制台应用程序，以便在 Microsoft 标识平台 2.0 中进行身份验证。 有关详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 授权 Azure 中继实体的第一步是，通过 Azure 门户在 Azure AD 租户中注册客户端应用程序。 注册客户端应用程序时，需要向 AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为应用程序 ID）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 

有关将应用程序注册到 Azure AD 的分步说明，请参阅[快速入门：将应用程序注册到 Azure AD](../active-directory/develop/quickstart-register-app.md#register-an-application)。

> [!IMPORTANT]
> 记下“目录(租户) ID”和“应用程序(客户端) ID”的值 。 将需要这些值来运行示例应用程序。

### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 在上面链接的同一篇文章中，请参阅[添加客户端机密](../active-directory/develop/quickstart-register-app.md#add-a-client-secret)部分来创建客户端机密。 

> [!IMPORTANT]
> 记下“客户端机密”的值。 将需要该值来运行示例应用程序。

## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色
将其中一个 Azure 中继角色分配给所需范围（中继实体、命名空间、资源组、订阅）中应用程序的服务主体。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

## <a name="run-the-sample"></a>运行示例

1. 从 [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) 下载控制台应用程序示例。
1. 按照 [README 文章](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)中的说明，在计算机本地运行应用程序。

    > [!NOTE]
    > 按照上述相同步骤运行 [WCF 中继的示例控制台应用程序](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)。 

#### <a name="highlighted-code-from-the-sample"></a>示例中突出显示的代码
下面是示例中的代码，演示如何使用 Azure AD 身份验证连接到 Azure 中继服务。  

1. 使用 `TokenProvider.CreateAzureActiveDirectoryTokenProvider` 方法创建 [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) 对象。 

    如果尚未创建应用注册，请参阅[将应用程序注册到 Azure AD](#register-your-application-with-an-azure-ad-tenant) 部分创建应用注册，然后创建客户端机密，如[创建客户端机密](#create-a-client-secret)部分所述。

    如果要使用现有的应用注册，请按照以下说明获取“应用程序(客户端) ID”和“目录(租户) ID” 。 

    1. 登录 [Azure 门户](https://portal.azure.com)。
    1. 使用顶部的搜索栏，搜索并选择“Azure Active Directory”。
    1. 在“Active Directory”页左侧菜单的“管理”部分，选择“应用注册”  。 
    1. 选择应用注册。 
    1. 在应用注册页上，你将看到“应用程序(客户端) ID”和“目录(租户) ID”的值 。 
    
    若要获取客户端机密，请执行下列步骤：
    1. 单击应用注册页左侧菜单中的“证书和机密”。 
    1. 在“客户端机密”部分，使用机密的“值”列中的“复制”按钮 。 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. 通过传递混合连接 URI 和在前面步骤中创建的令牌提供程序来创建 [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) 或 [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) 对象。

    **侦听器：**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **发送方**：
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure RBAC，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-template.md)

若要了解有关 Azure 中继的详细信息，请参阅以下主题。
- [什么是中继？](relay-what-is-it.md)
- [开始使用 Azure 中继混合连接 WebSocket](relay-hybrid-connections-dotnet-get-started.md)
- [开始使用 Azure 中继混合连接 HTTP 请求](relay-hybrid-connections-http-requests-dotnet-get-started.md)








