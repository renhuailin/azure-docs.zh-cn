---
title: 身份验证和授权
description: 了解应用程序或服务可以对 Azure 空间锚点进行身份验证的各种方式，以及必须对空间锚点进行访问的控制级别。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 0166a3b6031f9e1d364a37db99be5bc5a65267df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484604"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空间定位点身份验证和授权

在本文中，你将学习从你的应用程序或 web 服务向 Azure 空间定位点进行身份验证的各种方式。 你还将了解如何使用 Azure 中基于角色的访问控制 (Azure RBAC) ，Azure Active Directory (Azure AD) 来控制对空间锚定帐户的访问。

## <a name="overview"></a>概述

![显示 Azure 空间定位点身份验证概述的示意图。](./media/spatial-anchors-authentication-overview.png)

若要访问给定的 Azure 空间定位点帐户，客户端需要先从 Azure 混合现实安全令牌服务 (STS) 获取访问令牌。 从 STS 获取的令牌的生存期为24小时。 它们包含空间定位服务用来对帐户做出授权决定的信息，并确保只有授权的主体可以访问帐户。

对于 Azure AD 颁发的帐户密钥或令牌，可在 exchange 中获取访问令牌。

帐户密钥使你能够快速开始使用 Azure 空间锚定服务。 但是，在将应用程序部署到生产环境之前，我们建议你将应用程序更新为使用 Azure AD 身份验证。

可以通过两种方式获取 Azure AD 身份验证令牌：

- 如果你正在构建企业应用程序，并且你的公司使用 Azure AD 作为其标识系统，则可以在应用程序中使用基于用户的 Azure AD 身份验证。 然后，使用现有 Azure AD 安全组授予对空间锚定帐户的访问权限。 你还可以将访问权限直接授予你组织中的用户。
- 否则，我们建议您从支持您的应用程序的 web 服务获取 Azure AD 令牌。 对于生产应用程序，建议使用此方法，因为这样可以避免在客户端应用程序中嵌入凭据以访问 Azure 空间锚。

## <a name="account-keys"></a>帐户密钥

最简单的入门方法是使用帐户密钥访问 Azure 空间锚定帐户。 您可以在 Azure 门户上获取您的帐户密钥。 中转到你的帐户并选择 " **密钥** " 选项卡：

![显示 "密钥" 选项卡的屏幕截图，其中突出显示了主键的 "复制" 按钮。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

提供两个密钥。 两者同时对空间锚定帐户的访问权限都有效。 建议你定期更新用于访问该帐户的密钥。 具有两个不同的有效密钥可实现这些更新而无需停机。 只需更新主密钥和辅助密钥即可。

SDK 提供通过帐户密钥进行身份验证的内置支持。 只需 `AccountKey` 在对象上设置属性 `cloudSession` ：

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

设置该属性后，SDK 将处理访问令牌的帐户密钥的交换，并为应用提供必要的令牌缓存。

> [!WARNING]
> 建议你使用帐户密钥进行快速载入，但仅限开发/原型开发期间使用。 我们不建议将应用程序交付到生产中，并在该应用程序中使用嵌入的帐户密钥。 相反，请使用下面所述的基于用户或基于服务的 Azure AD 身份验证方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于以 Azure Active Directory 用户为目标的应用程序，我们建议你对用户使用 Azure AD 令牌。 可以通过使用 [MSAL](../../active-directory/develop/msal-overview.md)来获取此令牌。 按照快速入门中的 [注册应用程序](../../active-directory/develop/quickstart-register-app.md)中的步骤操作，其中包括：

**在 Azure 门户中**
1.    在 Azure AD 中将应用程序注册为本机应用程序。 注册过程中，需要确定应用程序是否应为多租户应用程序。 还需要为应用程序提供允许的重定向 Url。
1.  请访问 **API 权限** 选项卡。
2.  选择“添加权限”。
    1.  在 "**我的组织使用的 api** " 选项卡上选择 **混合现实资源提供程序**。
    2.  选择“委托的权限”。
    3.  在 **mixedreality** 下选择 " **mixedreality** "。
    4.  选择“添加权限”。
3.  选择 " **授予管理员许可**"。

2. 向应用程序或用户授予对你的资源的访问权限：
   1.    中转到 Azure 门户中的空间锚资源。
   2.    请 **访问 (IAM)** "选项卡上的" 访问控制 "。
   3.    选择“添加角色分配”。
   1.    [选择一个角色](#azure-role-based-access-control)。
   2.    在 " **选择** " 框中，输入要为其分配访问权限的用户、组和/或应用程序的名称。
   3.    选择“保存”。

**在代码中**
1.    请确保为 MSAL 中的 **客户端 id** 和 **RedirectUri** 参数使用你自己的 Azure AD 应用程序的应用程序 ID 和重定向 URI。
2.    设置租户信息：
        1.    如果你的应用程序 **仅支持我的组织**，请将此值替换为你的 **租户 ID** 或 **租户名称**。 例如，contoso.microsoft.com。
        2.    如果你的应用程序支持 **任何组织目录中的帐户**，请将此值替换为 **组织**。
        3.    如果你的应用程序支持 **所有 Microsoft 帐户用户**，请将此值替换为 **Common**。
3.    在令牌请求中，将 **范围** 设置为 **" `https://sts.<account-domain>//.default` "**，其中，将 `<account-domain>` 替换为 Azure 空间锚定帐户的 **帐户域** 。 美国东部2帐户域中的 Azure 空间标记帐户的示例范围为 **" `https://sts.mixedreality.azure.com//.default` "**。 此范围告知 Azure AD，应用程序正在请求混合现实安全令牌服务 (STS) 的令牌。

完成这些步骤后，应用程序应能够从 MSAL 获取 Azure AD 令牌。 可以 `authenticationToken` 在云会话配置对象上将该 Azure AD 标记设置为：

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

若要将使用 Azure 空间锚点的应用部署到生产环境，建议使用将代理身份验证请求的后端服务。 下面是此过程的概述：

![提供 Azure 空间定位点身份验证概述的示意图。](./media/spatial-anchors-aad-authentication.png)

此处，假设你的应用程序使用自己的机制来向后端服务进行身份验证。  (例如，Microsoft 帐户、PlayFab、Facebook、Google ID 或自定义用户名和密码。在用户通过后端服务进行身份验证后，) ，该服务可检索 Azure AD 令牌，为 Azure 空间锚定交换令牌，并将其返回给客户端应用程序。

Azure AD 访问令牌通过 [MSAL](../../active-directory/develop/msal-overview.md)进行检索。 按照 [注册应用快速入门](../../active-directory/develop/quickstart-register-app.md)中的步骤操作，其中包括：

**在 Azure 门户中**
1.    在 Azure AD 中注册应用程序：
        1.    在 Azure 门户中，选择 " **Azure Active Directory**"，然后选择 " **应用注册**"。
        2.    选择“新注册”。
        3.    输入应用程序的名称，选择“Web 应用/API”作为应用程序类型，然后输入服务的身份验证 URL。 选择“创建”  。
2.    在应用程序中，选择 " **设置**"，然后选择 " **证书和密钥** " 选项卡。创建新的客户端密钥，选择持续时间，然后选择 " **添加**"。 务必保存机密值。 需要将其包含在 web 服务的代码中。
3.    向应用程序和/或用户授予对你的资源的访问权限：
        1.    中转到 Azure 门户中的空间锚资源。
        2.    请 **访问 (IAM)** "选项卡上的" 访问控制 "。
        3.    选择“添加角色分配”。
        4.    [选择一个角色](#azure-role-based-access-control)。
        5.    在 " **选择** " 框中，输入要向其分配访问权限的应用程序的名称或名称。 如果希望应用的用户具有不同于空间锚点帐户的角色，请在 Azure AD 中注册多个应用程序，并为每个应用程序分配一个单独的角色。 然后实现授权逻辑，以便为用户使用适当的角色。

              > [!NOTE]
              > 在 " **添加角色分配** " 窗格中的 " **分配访问权限**" 中，选择 **Azure AD 用户、组或服务主体**。

        6.    选择“保存”。

**在代码中**

>[!NOTE]
> 您可以使用 GitHub 上提供的服务示例。

1.    请确保使用你自己的 Azure AD 应用程序的应用程序 ID、应用程序密钥和重定向 URI 作为 MSAL 中的 **客户端 ID**、 **secret** 和 **RedirectUri** 参数。
2.    在 MSAL 中，将租户 ID 设置为你 **自己的 Azure AD** 租户 id。
3.    在令牌请求中，将 **范围** 设置为 **" `https://sts.<account-domain>//.default` "**，其中，将 `<account-domain>` 替换为 Azure 空间锚定帐户的 **帐户域** 。 美国东部2帐户域中的 Azure 空间标记帐户的示例范围为 **" `https://sts.mixedreality.azure.com//.default` "**。

完成这些步骤后，后端服务可以检索 Azure AD 令牌。 然后，该服务会使用该令牌来交换要返回给客户端的 MR 令牌。 使用 Azure AD 令牌检索 MR 令牌的过程是通过 REST 调用完成的。 下面是一个示例调用：

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

授权标头的格式如下所示： `Bearer <Azure_AD_token>`

响应包含以纯文本形式的 MR 标记。

该 MR 令牌随后会返回给客户端。 然后，客户端应用可以在云会话配置中将其设置为其访问令牌：

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

为了帮助你控制授予应用程序、服务或 Azure AD 服务用户的访问级别，你可以根据需要为 Azure 空间锚定帐户分配这些预先存在的角色：

- **空间锚定帐户所有者**。 具有此角色的应用程序或用户可以创建空间锚，对其进行查询，并将其删除。 使用帐户密钥对帐户进行身份验证时，会将空间锚定帐户所有者角色分配给经过身份验证的主体。
- **空间锚定帐户参与者**。 具有此角色的应用程序或用户可以创建空间定位点并对其进行查询，但不能删除它们。
- **空间锚定帐户读取** 者。 具有此角色的应用程序或用户只能查询空间锚。 它们无法创建新的文件、删除现有的更新或更新元数据。 此角色通常用于某些用户组织环境的应用程序，但其他用户只能撤回之前放置在环境中的定位点。

## <a name="next-steps"></a>后续步骤

创建具有 Azure 空间锚点的第一个应用：

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
