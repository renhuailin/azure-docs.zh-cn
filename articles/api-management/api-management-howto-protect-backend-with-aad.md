---
title: 在 API 管理中使用 OAuth 2.0 和 Azure Active Directory 保护 API 后端
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理和开发人员门户中使用 OAuth 2.0 用户授权和 Azure Active Directory 保护用户对 Web API 后端的访问。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6a6a83ef0e810880ff14ddc8125235229db75787
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676158"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>在 Azure API 管理中将 OAuth 2.0 授权和 Azure Active Directory 配合使用来保护 Web API 后端 

本文介绍如何将 [OAuth 2.0 协议与 Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md) 配合使用来配置 [Azure API 管理](api-management-key-concepts.md)实例，以保护 API。 

> [!NOTE]
> 此功能在 API 管理的“开发人员”、“基本”、“标准”和“高级”层中可用   。  
> 
> 可以在“消耗”层中遵循以下每个步骤，但从开发人员门户调用 API 的步骤除外。

## <a name="prerequisites"></a>必备条件

在执行本文中的步骤之前，必须提供：

- API 管理实例
- 一个使用 API 管理实例的已发布 API
- Azure AD 租户

## <a name="overview"></a>概述

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="该概况图直观地将下面的流程概念化。":::

1. 在 Azure AD 中注册一个应用程序（后端应用）用于表示 API。

1. 在 Azure AD 中注册另一个应用程序（客户端应用），用于表示需要调用 API 的客户端应用程序。

1. 在 Azure AD 中授予权限，使客户端应用能够调用后端应用。

1. 在开发人员门户中配置开发人员控制台，以使用 OAuth 2.0 用户授权调用 API。

1. 添加 **validate-jwt** 策略以验证每个传入请求的 OAuth 令牌。

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. 在 Azure AD 中注册一个应用程序，用于表示 API

使用 Azure 门户在 Azure AD 中注册一个表示某个 API 的应用程序，以通过 Azure AD 保护该 API。 

有关应用注册的详细信息，请参阅[快速入门：配置应用程序以公开 Web API](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用注册”。

1. 选择“新注册”。 

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：

   - 在“名称”部分中，输入一个将显示给应用用户的有意义的应用程序名称，例如 *backend-app*。 
   - 在“支持的帐户类型”部分，选择适合你的方案的选项。 

1. 将“[重定向 URI](../active-directory/develop/reply-url.md)”部分保留空白。

1. 选择“注册”以创建应用程序。 

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用 。

1. 在边侧菜单的“管理”部分下，选择“公开 API”，并将“应用程序 ID URI”设置为默认值  。 记下此值以备将来使用。

1. 选择“添加范围”按钮以显示“添加范围”页 ：
    1. 在“范围名称”、“管理员同意显示名称”和“管理员同意说明”中输入新值  。
    1. 确保选择了“已启用”范围状态。

1. 选择“添加作用域”按钮以创建作用域。 

1. 重复步骤 8 和 9，以添加 API 支持的所有范围。

1. 创建范围后，请记下它们，以便在后续步骤中使用。 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. 在 Azure AD 中注册另一个应用程序，用于表示客户端应用程序

将每个调用 API 的客户端应用程序注册为 Azure AD 中的应用程序。 在本示例中，客户端应用程序是 API 管理开发人员门户中的“开发人员控制台”。 

在 Azure AD 中注册另一个应用程序，用于表示开发人员控制台：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用注册”。

1. 选择“新注册”。

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：

   - 在“名称”部分中，输入一个将显示给应用用户的有意义的应用程序名称，例如 *client-app*。 
   - 在“支持的帐户类型”部分中，选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户”。  

1. 在“重定向 URI”部分中，选择 `Web` 并将 URL 字段暂时保留为空。

1. 选择“注册”以创建应用程序。 

1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用 。

1. 为此应用程序创建客户端密码，以在后续步骤中使用。

   1. 在边侧菜单的“管理”部分下，选择“证书和机密” 。
   1. 在“客户端机密”下，选择“新建客户端密钥” 。
   1. 在“添加客户端机密”下，提供说明并选择密钥过期时间 。
   1. 选择 **添加** 。

创建机密后，请记下密钥值，以便在后续步骤中使用。 

## <a name="3-grant-permissions-in-azure-ad"></a>3. 在 Azure AD 中授予权限

注册了用于表示 API 和开发人员控制台的两个应用程序之后，请授予权限以允许客户端应用调用后端应用。  

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用注册”。

1. 选择你的客户端应用程序。 然后，在应用的页面列表中，选择“API 权限”。

1. 选择“添加权限”。

1. 在“选择 API”下，选择“我的 API”，然后找到并选择你的 backend-app。

1. 选择“委托的权限”，然后选择 backend-app 的适当权限。

1. 选择“添加权限”。

可选：
1. 导航到客户端应用的“API 权限”页。

1. 选择“为 \<your-tenant-name> 授予管理员同意”，以代表此目录中的所有用户授予同意。 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. 在开发人员控制台中启用 OAuth 2.0 用户授权

此时，我们已在 Azure AD 中创建应用程序，并已授予适当的权限来让客户端应用调用后端应用。 

此示例将在开发人员控制台（客户端应用）中启用 OAuth 2.0 用户授权。

1. 在 Azure 门户中，找到“授权终结点 URL”和“令牌终结点 URL”，并将其保存供稍后使用 。 
    1. 打开“应用注册”页。 
    1. 选择“终结点”。
    1. 复制“OAuth 2.0 授权终结点”和“OAuth 2.0 令牌终结点” 。 

1. 浏览到 API 管理实例。

1. 在边侧菜单中的“开发人员门户”部分下，选择“OAuth 2.0 + OpenID Connect” 。 

1. 在“OAuth 2.0”选项卡下，选择“添加” 。

1. 提供“显示名称”和“说明”。 

1. 对于“客户端注册页 URL”，请输入占位符值，如 `http://localhost`。 
    * “客户端注册页 URL”指向一个页面，用户可在其中创建和配置自己的、受 OAuth 2.0 提供程序支持的帐户。 
    * 我们将使用一个占位符，因为在此示例中，用户不会创建和配置自己的帐户。

1. 选择“授权代码”作为“授权类型”。 

1. 指定前面保存的“授权终结点 URL”和“令牌终结点 URL” ： 
    1. 将“OAuth 2.0 授权终结点”复制并粘贴到“授权终结点 URL”文本框中 。 
    1. 选择“授权”请求方法下的“POST”。
    1. 输入“OAuth 2.0 令牌终结点”，并将其粘贴到“令牌终结点 URL”文本框中 。 
        * 如果使用 v1 终结点：
          * 添加名为 resource 的正文参数。
          * 输入后端应用的“应用程序 ID”作为值。
        * 如果使用 v2 终结点：
          * 使用在“默认范围”字段中创建的后端应用范围。
          * 在[应用程序清单](../active-directory/develop/reference-app-manifest.md)中将 [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) 属性的值设置为 `2`。
          

   >[!IMPORTANT]
   > 虽然你可以使用 v1 或 v2 终结点，但我们建议使用 v2 终结点 。 

1. 指定客户端应用凭据：
    * 对于“客户端 ID”，请使用客户端应用的“应用程序 ID”。 
    * 对于“客户端机密”，请使用前面为 client-app 创建的密钥。 

1. 记下授权代码授权类型的“重定向 URI”。

1. 选择“创建”  。

1. 返回到客户端应用注册。 
 
1. 在“管理”下，选择“身份验证”。 

1. 在“平台配置”下：
    * 单击“添加平台”。
    * 选择“Web”作为类型。 
    * 将前面保存的重定向 URI 粘贴到“重定向 URI”下。
    * 单击“配置”按钮保存设置。

   现在，开发人员控制台可以通过 OAuth 2.0 授权服务器从 Azure AD 获取访问令牌了，接下来请为 API 启用 OAuth 2.0 用户授权。 这使得开发人员控制台知道在调用 API 之前，需要代表用户获取访问令牌。

15. 浏览到 API 管理实例，并转到“API”。

1. 选择要保护的 API。 例如，`Echo API`。

1. 转到“设置”。

1. 在“安全性”下：
    1. 选择“OAuth 2.0”。
    1. 选择前面配置的 OAuth 2.0 服务器。 

1. 选择“保存”。

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. 从开发人员门户成功调用 API

> [!NOTE]
> 本部分不适用于“消耗”层，因为该层不支持开发人员门户。

在 API 中启用 OAuth 2.0 用户授权后，开发人员控制台在调用 API 之前，将会代表用户获取访问令牌。

1. 在开发人员门户中浏览到 API 下的任一操作。 
1. 选择“试用”，随即你会转到开发人员控制台。

1. 可以看到，“授权”部分出现了一个与刚刚添加的授权服务器对应的新项。

1. 从授权下拉列表中选择“授权代码”。 
1. 系统将提示你登录到 Azure AD 租户。 
    * 如果你已登录到帐户，则可能不会出现提示。

1. 成功登录后，会将一个 `Authorization` 标头添加到请求，该标头包含从 Azure AD 获取的访问令牌。 下面是一个采用 Base64 编码的示例令牌：

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. 选择“发送”以成功调用 API。

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. 配置 JWT 验证策略以便对请求进行预授权

到目前为止：
* 你已尝试从开发人员控制台发出调用。
* 你已看到提示并已登录到 Azure AD 租户。 
* 开发人员控制台将代表你获取访问令牌，并在对 API 发出的请求中包含该令牌。

但是，如果有人调用我们的 API 但未提供令牌或者提供无效的令牌，会发生什么情况？ 例如，如果在不使用 `Authorization` 头的情况下调用 API，则调用仍会继续进行，因为 API 管理不验证访问令牌。 它只是将 `Authorization` 标头传递给后端 API。

使用[验证 JWT](./api-management-access-restriction-policies.md#ValidateJWT) 策略通过验证每个传入请求的访问令牌，对 API 管理中的请求进行预授权。 如果某个请求没有有效的令牌，API 管理会阻止该请求。 

将以下示例策略添加到 `<inbound>` 策略部分后，该策略将检查从 Azure AD 获取的访问令牌中的受众声明值，如果该令牌无效，则返回错误消息。 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> 上述 `openid-config` URL 对应于 v2 终结点。 对于 v1 `openid-config` 终结点，请使用 `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration`。

> [!TIP] 
> 在 Azure 门户中的以下位置之一查找用作 Azure AD 租户 ID 的 {aad-tenant} 值：
> * Azure AD 资源的概述页，或
> * Azure AD 资源的“管理”>“属性”页。

有关如何配置策略的信息，请参阅[设置或编辑策略](./set-edit-policies.md)。

## <a name="build-an-application-to-call-the-api"></a>生成应用程序来调用 API

在本指南中，我们使用了 API 管理中的开发者控制台作为示例客户端应用程序来调用由 OAuth 2.0 保护的 `Echo API`。 若要详细了解如何构建应用程序并实现 OAuth 2.0，请参阅 [Azure AD 代码示例](../active-directory/develop/sample-v2-code.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure AD 和 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)。
- 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
- 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](./api-management-howto-mutual-certificates.md)。
- [创建 API 管理服务实例](./get-started-create-service-instance.md)。
- [管理第一个 API](./import-and-publish.md)。
