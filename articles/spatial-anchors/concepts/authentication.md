---
title: 身份验证和授权
description: 了解应用或服务向 Azure 空间定位点进行身份验证的各种方式，以及在限制访问空间定位点时可以采用的控制级别。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp, subject-rbac-steps
ms.openlocfilehash: ef49c6ebd356c97466b9b5a744cc58584c42bcb7
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126025"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空间定位点身份验证和授权

本文介绍可从应用或 Web 服务向 Azure 空间定位点进行身份验证的各种方式。 此外，还介绍了如何使用 Azure Active Directory (Azure AD) 中的 Azure 基于角色的访问控制 (Azure RBAC) 来控制对空间定位点帐户的访问。

## <a name="overview"></a>概述

![概述如何向 Azure 空间定位点进行身份验证的示意图。](./media/spatial-anchors-authentication-overview.png)

若要访问给定的 Azure 空间定位点帐户，客户端需要先从 Azure 混合现实安全令牌服务 (STS) 获取访问令牌。 从 STS 获取的令牌的生存期为 24 小时。 这些令牌包含空间定位服务用来对帐户做出授权决策的信息，并确保只有已获授权的主体可以访问该帐户。

可以获取访问令牌来交换帐户密钥或者 Azure AD 颁发的令牌。

帐户密钥可让你快速开始使用 Azure 空间定位点服务。 但是，在将应用程序部署到生产环境之前，我们建议你将应用更新为使用 Azure AD 身份验证。

可通过两种方式获取 Azure AD 身份验证令牌：

- 如果你正在生成企业应用程序，并且你的公司使用 Azure AD 作为其标识系统，则你可以在应用中使用基于用户的 Azure AD 身份验证。 然后，使用现有 Azure AD 安全组授予对空间定位点帐户的访问权限。 还可以直接向组织中的用户授予访问权限。
- 否则，我们建议从支持你的应用的 Web 服务获取 Azure AD 令牌。 建议对生产应用程序使用此方法，因为这可以避免在客户端应用程序中嵌入用于访问 Azure 空间定位点的凭据。

## <a name="account-keys"></a>帐户密钥

使用帐户密钥访问 Azure 空间定位点帐户是最简单的入手方法。 可以在 Azure 门户上获取帐户密钥。 转到你的帐户，然后选择“密钥”选项卡：

![显示“密钥”选项卡的屏幕截图，其中突出显示了“主密钥”对应的“复制”按钮。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

有两个密钥可供使用。 两者同时可用于访问空间定位点帐户。 建议定期更新用于访问该帐户的密钥。 准备好两个不同的有效密钥可实现这些更新，且不会造成停机。 只需更新主密钥和辅助密钥中的一个。

SDK 为通过帐户密钥进行身份验证提供内置支持。 你只需在 `AccountKey` 对象上设置 `cloudSession` 属性即可：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

设置该属性后，SDK 将处理使用帐户密钥交换访问令牌的过程，并为应用提供必要的令牌缓存。

> [!WARNING]
> 我们建议使用帐户密钥进行快速加入，但仅限在开发/原型设计期间执行此操作。 我们不建议将嵌入了帐户密钥的应用程序交付到生产环境。 而是应该使用下面所述的基于用户或基于服务的 Azure AD 身份验证方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于面向 Azure Active Directory 用户的应用程序，我们建议对用户使用 Azure AD 令牌。 可以使用 [MSAL](../../active-directory/develop/msal-overview.md) 获取此令牌。 遵循[有关注册应用的快速入门](../../active-directory/develop/quickstart-register-app.md)中的步骤，其中包括：

**在 Azure 门户中**
1.    在 Azure AD 中将应用程序注册为本机应用程序。 在注册过程中，需要确定应用程序是否应为多租户应用程序。 此外，还需要提供应用程序允许的重定向 URL。
1.  转到“API 权限”选项卡。
1.  选择“添加权限”。
    1.  在“我的组织使用的 API”选项卡上选择“混合现实资源提供程序”。 
    2.  选择“委托的权限”。
    3.  在“mixedreality”下选择“mixedreality.signin”。 
    4.  选择“添加权限”。
1.  选择“授予管理员同意”。

1. 将 [ASA RBAC 角色](#azure-role-based-access-control)分配给你要授予其资源访问权限的应用程序或用户。 如果希望应用程序的用户对 ASA 帐户具有不同的角色，请在 Azure AD 中注册多个应用程序并为每个应用程序分配一个单独的角色。 然后实现授权逻辑，以便为用户使用适当的角色。 有关详细的角色分配步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

**在代码中**
1.    对于 MSAL 中的“client ID”和“RedirectUri”参数，请确保使用你自己的 Azure AD 应用程序的应用程序 ID 和重定向 URI。 
2.    设置租户信息：
        1.    如果你的应用程序支持“仅我的组织”，请将此值替换为你的 **租户 ID** 或 **租户名称**。 例如 contoso.microsoft.com。
        2.    如果你的应用程序支持“任何组织目录中的帐户”，请将此值替换为 **Organizations**。
        3.    如果你的应用程序支持“所有 Microsoft 帐户用户”，请将此值替换为 **Common**。
3.    在令牌请求中，将 **scope** 设置为“`https://sts.<account-domain>//.default`”，其中，`<account-domain>` 需替换为你的 Azure 空间定位点帐户的 **帐户域**。 美国东部 2 帐户域中的 Azure 空间定位点帐户的示例范围为“`https://sts.mixedreality.azure.com//.default`”。 此范围告知 Azure AD，应用程序正在请求混合现实安全令牌服务 (STS) 的令牌。

完成这些步骤后，你的应用程序应该可以从 MSAL 获取 Azure AD 令牌。 可将该 Azure AD 令牌设置为云会话配置对象上的 `authenticationToken`：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服务身份验证

若要将使用 Azure 空间定位点的应用部署到生产环境，我们建议使用一个用于中转身份验证请求的后端服务。 下面是该过程的概述：

![概述如何向 Azure 空间定位点进行身份验证的示意图。](./media/spatial-anchors-aad-authentication.png)

此处，假设你的应用使用自己的机制向其后端服务进行身份验证。 （例如，Microsoft 帐户、PlayFab、Facebook、Google ID 或自定义用户名和密码。）在后端服务中对用户进行身份验证后，该服务可以检索 Azure AD 令牌，使用该令牌交换 Azure 空间定位点的访问令牌，然后将该访问令牌返回给客户端应用程序。

通过 [MSAL](../../active-directory/develop/msal-overview.md) 检索 Azure AD 访问令牌。 遵循[应用注册快速入门](../../active-directory/develop/quickstart-register-app.md)中的步骤，其中包括：

**在 Azure 门户中**
1.    在 Azure AD 中注册应用程序：
        1.    在 Azure 门户中，依次选择“Azure Active Directory”、“应用注册” 。
        2.    选择“新注册”。
        3.    输入应用程序的名称，选择“Web 应用/API”作为应用程序类型，然后输入服务的身份验证 URL。 选择“创建”  。
1.    在应用程序上选择“设置”，然后选择“证书和机密”选项卡。创建新的客户端密码，选择持续时间，然后选择“添加”。   请务必保存机密值。 稍后需要将其包含在 Web 服务的代码中。
1. 将 [ASA RBAC 角色](#azure-role-based-access-control)分配给你要授予其资源访问权限的应用程序或用户。 如果希望应用程序的用户对 ASA 帐户具有不同的角色，请在 Azure AD 中注册多个应用程序并为每个应用程序分配一个单独的角色。 然后实现授权逻辑，以便为用户使用适当的角色。 有关详细的角色分配步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

**在代码中**

>[!NOTE]
> 可以使用作为[空间定位点示例应用](https://github.com/Azure/azure-spatial-anchors-samples)的一部分提供的[服务示例](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample)。

1.    确保使用自己的 Azure AD 应用程序的应用程序 ID、应用程序机密和重定向 URI 作为 MSAL 中的 **client ID**、**secret** 和 **RedirectUri** 参数。
2.    在 MSAL 的 **authority** 参数中，将 tenant ID 设置为你自己的 Azure AD 租户 ID。
3.    在令牌请求中，将 **scope** 设置为“`https://sts.<account-domain>//.default`”，其中，`<account-domain>` 需替换为你的 Azure 空间定位点帐户的 **帐户域**。 美国东部 2 帐户域中的 Azure 空间定位点帐户的示例范围为“`https://sts.mixedreality.azure.com//.default`”。

完成这些步骤后，后端服务便可以检索 Azure AD 令牌。 然后，该服务会使用该令牌来交换要返回给客户端的 MR 令牌。 使用 Azure AD 令牌检索 MR 令牌的过程是通过 REST 调用完成的。 下面是一个示例调用：

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

授权标头的格式如下：`Bearer <Azure_AD_token>`

响应包含纯文本格式的 MR 令牌。

该 MR 令牌随后会返回给客户端。 然后，客户端应用可在云会话配置中将此令牌设置为其访问令牌：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

为了帮助控制向应用程序、服务或服务的 Azure AD 用户授予的访问权限级别，可以根据需要针对 Azure 空间定位点帐户分配以下预先存在的角色：

- **空间定位点帐户所有者**。 具有此角色的应用程序或用户可以创建、查询和删除空间定位点。 当你使用帐户密钥对帐户进行身份验证时，系统会将“空间定位点帐户所有者”角色分配给经过身份验证的主体。
- **空间定位点帐户参与者**。 具有此角色的应用程序或用户可以创建、查询空间定位点，但不能删除空间定位点。
- **空间定位点帐户读取者**。 具有此角色的应用程序或用户只能查询空间定位点， 而不能创建新的空间定位点、删除现有的空间定位点，也不能更新空间定位点的元数据。 此角色通常用于这样的应用程序：其中有些用户管护了环境，但还有一些用户只能重新调用以前放置在该环境中的定位点。

## <a name="next-steps"></a>后续步骤

使用 Azure 空间定位点创建你的第一个应用：

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
