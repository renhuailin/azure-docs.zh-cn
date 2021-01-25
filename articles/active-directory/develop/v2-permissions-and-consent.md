---
title: Microsoft 标识平台范围、权限 & 许可
description: 了解 Microsoft 标识平台终结点中的授权，包括范围、权限和许可。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: aa8c00d1ee2a0dc3d019cc75b4e411ede984e74a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756046"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft 标识平台中的权限和许可

与 Microsoft 标识平台集成的应用程序遵循授权模型，该模型可让用户和管理员控制数据的访问方式。 已在 Microsoft 标识平台上更新授权模型的实现。 它会更改应用必须与 Microsoft 标识平台交互的方式。 本文介绍此授权模型的基本概念，包括范围、权限和许可。

## <a name="scopes-and-permissions"></a>范围和权限

Microsoft 标识平台实现 [OAuth 2.0](active-directory-v2-protocols.md) 授权协议。 OAuth 2.0 是可让第三方应用代表用户访问 Web 托管资源的方法。 任何与 Microsoft 标识平台集成的 web 托管资源都具有资源标识符或 *应用程序 ID URI*。 

下面是 Microsoft 托管资源的一些示例：

* Microsoft Graph： `https://graph.microsoft.com`
* Microsoft 365 邮件 API：`https://outlook.office.com`
* Azure Key Vault：`https://vault.azure.net`

这同样适用于已与 Microsoft 标识平台集成的任何第三方资源。 以上任意资源还可以定义一组可用于将该资源的功能划分成较小区块的权限。 例如， [Microsoft Graph](https://graph.microsoft.com) 已定义执行以下任务及其他任务所需的权限：

* 读取用户的日历
* 写入用户的日历
* 以用户身份发送邮件

由于这些类型的权限定义，资源对其数据进行了精细的控制，并显示了 API 功能的公开方式。 第三方应用可以从用户和管理员请求这些权限，只有在用户或管理员批准该请求之后，应用才能代表用户访问或处理数据。 

将资源的功能拆分为小权限集时，可以生成第三方应用来仅请求执行其功能所需的权限。 用户和管理员可以知道应用可以访问哪些数据。 而且，它们可以更有把握地确保应用程序不会受到恶意攻击。 开发人员应始终遵守最低权限原则，要求仅要求其应用程序正常运行所需的权限。

在 OAuth 2.0 中，这些类型的权限集称为 " *作用域*"。 它们也经常称为 " *权限*"。 在 Microsoft 标识平台中，权限表示为字符串值。 对于 Microsoft Graph 示例，以下是每个权限的字符串值：

* 使用 `Calendars.Read`
* 使用 `Calendars.ReadWrite`
* 使用 `Mail.Send`

应用往往是通过在发往 Microsoft 标识平台授权终结点的请求中指定范围来请求这些权限。 但是，某些高权限权限只能通过管理员同意获得。 可以使用 [管理员同意终结点](#admin-restricted-permissions)请求或授予这些帐户。 请继续阅读以了解详细信息。

## <a name="permission-types"></a>权限类型

Microsoft 标识平台支持两种类型的权限： *委托的权限* 和 *应用程序权限*。

* **委托的权限** 由包含登录用户的应用使用。 对于这些应用程序，用户或管理员同意应用程序所请求的权限。 当应用程序调用目标资源时，该应用程序将委派权限作为已登录用户。 

    有些委托的权限可以由非管理员同意。 但某些高权限权限需要 [管理员同意](#admin-restricted-permissions)。 若要了解哪些管理员角色可以同意委派权限，请参阅 [Azure Active Directory (Azure AD) 中的管理员角色权限 ](../roles/permissions-reference.md)。

* **应用程序权限** 由在没有登录用户的情况下运行的应用程序使用，例如，作为后台服务或后台程序运行的应用程序。 只有 [管理员才能同意](#requesting-consent-for-an-entire-tenant) 应用程序权限。

_有效权限_ 是指应用程序在向目标资源发出请求时所具有的权限。 务必要了解委托权限与应用程序授予的应用程序权限之间的差异，以及在调用目标资源时授予应用的有效权限。

- 对于委托权限，你的应用程序的 _有效权限_ 是委托权限的最小特权交集，此应用已通过同意) 和当前已登录用户的权限授予 (。 应用的特权永远不会超过登录用户的特权。 

   在组织内，已登录用户的权限可以由策略确定，也可以由一个或多个管理员角色中的成员身份决定。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure AD 中的管理员角色权限](../roles/permissions-reference.md)。

   例如，假设为应用授予了 Microsoft Graph 中的 _User.ReadWrite.All_ 委托权限。 此权限在名义上会授予应用读取和更新组织中每个用户的个人资料的权限。 如果登录用户是全局管理员，则你的应用程序可以更新组织中每个用户的配置文件。 但是，如果登录用户没有管理员角色，则你的应用只能更新已登录用户的配置文件。 它无法更新组织中其他用户的配置文件，因为它有权代表其执行操作的用户不具有这些权限。

- 对于应用程序权限，你的应用程序的 _有效权限_ 是权限隐含的全部特权。 例如，拥有 _User.ReadWrite.All_ 应用程序权限的应用可以更新组织中每个用户的个人资料。

## <a name="openid-connect-scopes"></a>OpenID Connect 范围

OpenID connect 的 Microsoft 标识平台实现具有一些定义明确的作用域，这些范围还在 Microsoft Graph 上托管： `openid` 、 `email` 、 `profile` 和 `offline_access` 。 `address`和 `phone` OpenID 连接范围不受支持。

如果请求 OpenID Connect 范围和令牌，则将获取一个令牌以调用 [用户信息终结点](userinfo.md)。

### <a name="openid"></a>openid

如果应用使用 [OpenID connect](active-directory-v2-protocols.md)登录，则必须请求 `openid` 范围。 该 `openid` 作用域将显示在工作帐户同意页上作为 " **登录** " 权限。 在 "个人 Microsoft 帐户同意" 页上，它显示为 " **查看配置文件并使用您的 Microsoft 帐户权限连接到应用和服务** "。 

使用此权限，应用可以以声明的形式接收用户的唯一标识符 `sub` 。 此权限还允许应用访问用户信息终结点。 `openid`范围可用于 Microsoft 标识平台令牌终结点，以获取 ID 令牌。 应用可以使用这些令牌进行身份验证。

### <a name="email"></a>电子邮件

`email`范围可与 `openid` 范围和任何其他范围一起使用。 它以 `email` 声明的形式向应用提供对用户主要电子邮件地址的访问权限。 

仅当某个电子邮件地址与用户帐户相关联（并非总是如此）时， `email` 声明才包含在令牌中。 如果你的应用程序使用 `email` 范围，则应用程序需要能够处理令牌中不 `email` 存在声明的情况。

### <a name="profile"></a>个人资料

`profile`范围可与 `openid` 范围和任何其他范围一起使用。 它使应用程序能够访问有关用户的大量信息。 可访问的信息包括但不限于用户的名字、姓氏、首选用户名和对象 ID。 

有关 `profile` 特定用户的参数中可用声明的完整列表 `id_tokens` ，请参阅[ `id_tokens` 引用](id-tokens.md)。

### <a name="offline_access"></a>offline_access

[`offline_access` 范围](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) 可让应用长时间代表用户访问资源。 在 "同意" 页上，此范围显示为 " **维护对你向其授予** 访问权限的数据的访问权限"。 

用户批准 `offline_access` 范围后，应用可接收来自 Microsoft 标识平台令牌终结点的刷新令牌。 刷新令牌的生存期较长。 旧的访问令牌过期时，应用可以获取新的访问令牌。

> [!NOTE]
> 此权限当前显示在所有同意页上，甚至对于不提供刷新 (令牌的流（如 [隐式流](v2-oauth2-implicit-grant-flow.md)) ），也是如此。 此设置可解决这样一种情况：客户端可以在隐式流中开始，然后移到需要刷新令牌的代码流中。

在 Microsoft 标识平台上（向 v2.0 终结点发出的请求），应用程序必须显式请求 `offline_access` 范围才能接收刷新令牌。 因此，当你在 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)中兑换授权代码时，你将只收到终结点的访问令牌 `/token` 。 

访问令牌在短期内有效。 它通常会在一小时后过期。 到时，应用需要将用户重定向回到 `/authorize` 终结点以获取新的授权代码。 此重定向期间，用户可能需要再次输入其凭据或重新同意权限，具体取决于应用类型。

有关如何获取及使用刷新令牌的详细信息，请参阅 [Microsoft 标识平台协议参考](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>请求单个用户的许可

在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授权请求中，应用可以使用 `scope` 查询参数来请求所需的权限。 例如，当用户登录到应用时，该应用将发送一个类似于以下示例的请求。 添加 (分行符是为了提高可读性。 ) 

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 参数是应用程序所请求的委托权限列表（以空格分隔）。 每个权限都是通过将权限值追加到 (应用程序 ID URI) 的资源标识符来表示的。 在该请求示例中，应用需要相应的权限来读取用户的日历，以及以用户身分发送邮件。

用户输入其凭据后，Microsoft 标识平台会检查 *用户同意* 的匹配记录。 如果用户过去没有同意任何请求的权限，并且管理员没有代表整个组织同意这些权限，则 Microsoft 标识平台会要求用户授予所请求的权限。

目前，在首次 `offline_access` 同意应用程序时， ( "维持对你向其授予访问权限的数据的访问权限" ) 权限和 `user.read` ( "登录并读取你的配置文件" ) 权限。  这些权限通常是适当的应用程序功能所必需的。 `offline_access`权限允许应用访问，以便刷新对本机应用和 web 应用至关重要的令牌。 `user.read`权限提供对声明的访问 `sub` 。 它允许客户端或应用程序在一段时间内正确地确定用户身份并访问基本的用户信息。

![显示工作帐户同意的示例屏幕截图。](./media/v2-permissions-and-consent/work_account_consent.png)

当用户批准权限请求时，将记录同意。 用户以后登录该应用程序时，无需再次同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>请求整个租户的许可

组织购买应用程序的许可证或订阅时，组织通常希望预先设置应用程序以供组织的所有成员使用。 在此过程中，管理员可以代表租户中的任何用户授予对该应用程序的许可。 如果管理员授予了对整个租户的许可，则组织的用户将不会看到该应用程序的同意页面。

若要为租户中的所有用户请求许可委托的权限，应用可以使用管理员许可终结点。

此外，应用程序必须使用管理员许可终结点请求应用程序权限。

## <a name="admin-restricted-permissions"></a>管理员限制的权限

Microsoft 资源中的某些高特权权限可设置为 *受管理员限制*。 下面是这些类型权限的一些示例：

* 使用 `User.Read.All` 读取所有用户的完整个人资料
* 使用 `Directory.ReadWrite.All`
* 使用 `Groups.Read.All` 读取组织目录中的所有组

尽管使用者用户可以授予应用程序对此类数据的访问权限，但组织用户无法授予对同一组敏感公司数据的访问权限。 如果应用程序向组织用户请求访问以下权限之一，用户会收到错误消息，指出他们未经授权，无法许可应用的权限。

如果你的应用程序需要管理员限制权限的作用域，则组织的管理员必须代表组织的用户同意这些作用域。 若要避免向请求许可的用户显示对其无法授予的权限的提示，你的应用可以使用管理员许可终结点。 下一节将介绍管理员同意终结点。

如果应用程序请求高权限委托权限，并且管理员通过管理员同意终结点授予这些权限，则会为租户中的所有用户授予许可。

如果应用程序请求应用程序权限，并且管理员通过管理员同意终结点授予这些权限，则不会代表任何特定用户完成此授予。 而是直接为客户端应用程序授予权限。 这些类型的权限只能由守护程序服务和在后台运行的其他非交互式应用程序使用。

## <a name="using-the-admin-consent-endpoint"></a>使用管理员许可终结点

在您使用管理员同意终结点授予管理员同意后，您就已完成。 用户无需执行任何其他操作。 授予管理员许可后，用户可以通过典型的身份验证流程获取访问令牌。 生成的访问令牌具有许可的权限。

当公司管理员使用你的应用程序并将其定向到授权终结点时，Microsoft 标识平台会检测用户的角色。 它会询问公司管理员是否要代表整个租户代表您请求的权限。 相反，可以使用专用管理员许可终结点主动请求管理员代表整个租户授予权限。 此终结点对于请求应用程序权限也是必需的。 不能使用授权终结点请求应用程序权限。

如果你遵循了这些步骤，则应用就能为租户中的所有用户请求权限，包括受管理员限制的范围。 此操作是高权限操作。 仅在需要时才使用此操作。

若要查看实现这些步骤的代码示例，请参阅 GitHub 中的 [管理员限制的范围示例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) 。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在应用注册门户中请求权限

在应用注册门户中，应用程序可以列出他们所需的权限，包括委托权限和应用程序权限。 此设置允许使用 `/.default` 范围和 Azure 门户的 " **授予管理员许可** " 选项。  

通常，应以静态方式为给定的应用程序定义权限。 它们应是应用程序将以动态方式或增量方式请求的权限的超集。

> [!NOTE]
>只能通过使用来请求应用程序权限 [`/.default`](#the-default-scope) 。 因此，如果你的应用程序需要应用程序权限，请确保它们已列在应用注册门户中。

配置应用程序的静态请求权限列表：

1. 请在<a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure 门户应用注册 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>快速入门教程中转到你的应用程序。
1. 选择一个应用程序，或 [创建一个应用](quickstart-register-app.md) （如果尚未这样做）。
1. 在应用程序的 "**概述**" 页的 "**管理**" 下，选择 " **API 权限**" "  >  **添加权限**"。
1. 从可用 Api 列表中选择 " **Microsoft Graph** "。 然后添加应用所需的权限。
1. 选择 " **添加权限**"。

### <a name="recommended-sign-the-user-in-to-your-app"></a>建议：让用户登录到应用

在构建使用管理员许可终结点的应用程序时，应用通常需要一个页面或视图，使管理员能够批准应用的权限。 此页可以是：

* 应用程序注册流的一部分。
* 应用的设置部分。
* 专用的 "连接" 流。 

在许多情况下，只有在用户使用工作 Microsoft 帐户或学校 Microsoft 帐户登录后，应用才会显示此 "连接" 视图。

当你将用户登录到应用时，你可以确定管理员所属的组织，然后要求他们批准所需的权限。 尽管此步骤不是必需的，但它可以帮助你为组织用户创建更直观的体验。 

若要将用户登录，请遵循 [Microsoft 标识平台协议教程](active-directory-v2-protocols.md)。

### <a name="request-the-permissions-from-a-directory-admin"></a>向目录管理员请求权限

准备好向组织管理员请求权限时，可将用户重定向到 Microsoft 标识平台管理员许可终结点。

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 参数        | 条件        | 说明                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | 必需 | 要向其请求权限的目录租户。 它可以采用 GUID 或友好名称格式提供。 或者，它可以通过组织大致引用，如示例中所示。 不要使用 "common"，因为个人帐户不能提供管理员同意，但在租户的上下文中除外。 若要确保与管理租户的个人帐户的兼容性最佳，请尽可能使用租户 ID。 |
| `client_id` | 必需 | 应用程序 (客户端) ID 分配给应用的 [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908) 体验。 |
| `redirect_uri` | 必需 |要向其发送响应，供应用处理的重定向 URI。 必须与在应用注册门户中注册的重定向 URI 之一完全匹配。 |
| `state` | 建议 | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 使用该状态可在身份验证请求出现之前，在应用中编码用户的状态信息，例如用户过去所在的页面或视图。 |
|`scope`        | 必需        | 定义应用程序请求的权限集。 范围可以是使用 [`/.default`](#the-default-scope)) 或动态的静态 (。  此集可以包括 OpenID Connect 范围 (`openid` 、 `profile` `email`) 。 如果需要应用程序权限，必须使用 `/.default` 来请求静态配置的权限列表。  |


此时，Azure AD 要求租户管理员登录，以完成请求。 系统会要求管理员批准在参数中请求的所有权限 `scope` 。  如果使用了静态 (`/.default`) 值，则它的作用类似于1.0 版管理员同意终结点，并请求在应用所需的权限中找到的所有范围的同意。

#### <a name="successful-response"></a>成功的响应

如果管理员批准了应用的权限，成功响应如下所示：

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 参数 | 说明 |
| --- | --- |
| `tenant` | 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。 |
| `state` | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 该 state 用于在身份验证请求出现之前，于应用中编码用户的状态信息，例如之前所在的页面或视图。 |
| `admin_consent` | 将设置为 `True`。 |

#### <a name="error-response"></a>错误响应

如果管理员未批准应用的权限，失败响应如下所示：

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 参数 | 说明 |
| --- | --- |
| `error` | 错误代码字符串，可用于对发生的错误类型进行分类。 还可用于对错误做出反应。 |
| `error_description` | 可帮助开发人员识别错误根本原因的具体错误消息。 |

从管理员许可终结点收到成功响应后，应用便已获得所请求的权限。 接下来，可以请求所需的资源令牌。

## <a name="using-permissions"></a>使用权限

用户同意应用的权限后，你的应用可以获取访问令牌，这些令牌表示应用访问某些容量资源的权限。 访问令牌只能用于单个资源。 但在访问令牌中编码的是应用已被授予该资源的每个权限。 若要获取访问令牌，应用可以对 Microsoft 标识平台令牌终结点发出类似于以下请求：

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

可在资源的 HTTP 请求中使用生成的访问令牌。 它可可靠地向资源表明，你的应用程序具有执行特定任务的适当权限。

有关 OAuth 2.0 协议以及如何获取访问令牌的详细信息，请参阅 [Microsoft 标识平台终结点协议参考](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 范围

可以使用 `/.default` 范围，帮助将应用从 v1.0 终结点迁移到 Microsoft 标识平台终结点。 `/.default`范围是为每个应用程序生成的，该应用程序引用在应用程序注册中配置的权限的静态列表。 

的 `scope` 值在 `https://graph.microsoft.com/.default` 功能上与 v1.0 `resource=https://graph.microsoft.com` 终结点上的相同。 通过 `https://graph.microsoft.com/.default` 在其请求中指定范围，你的应用程序将请求一个访问令牌，该令牌包含你在应用注册门户中为应用选择的每个 Microsoft Graph 权限的范围。 范围是使用资源 URI 和构造的 `/.default` 。 因此，如果资源 URI 为 `https://contosoApp.com` ，则请求的范围为 `https://contosoApp.com/.default` 。  如果必须包含第二个斜杠才能正确请求标记，请参阅 [有关尾随斜杠的部分](#trailing-slash-and-default)。

`/.default`范围可用于任何 OAuth 2.0 流中。 但在 [代表流](v2-oauth2-on-behalf-of-flow.md) 和 [客户端凭据流](v2-oauth2-client-creds-grant-flow.md)中是必需的。 当你使用 v2 管理员同意终结点请求应用程序权限时，还需要使用此方法。

客户端不能 `/.default` 在单个请求中合并静态 () 许可和动态许可。 因此 `scope=https://graph.microsoft.com/.default+mail.read` 会导致错误，因为它结合了范围类型。

### <a name="default-and-consent"></a>/.default 和 consent

`/.default` 范围也可触发 `prompt=consent` 的 v1.0 终结点行为。 无论资源如何，它都会请求注册应用程序的所有权限。 如果它包含在请求中，则该范围将 `/.default` 返回一个令牌，其中包含所请求资源的作用域。

### <a name="default-when-the-user-has-already-given-consent"></a>用户已授予许可时为 /.default

`/.default`作用域在功能上与以-中心的 v2.0 终结点的行为相同 `resource` 。 它还附带了 v1.0 终结点的许可行为。 也就是说， `/.default` 仅当用户在客户端和资源之间未授予任何权限时，才会触发同意提示。 

如果存在任何此类许可，则返回的令牌将包含用户为该资源授予的所有作用域。 但是，如果未授予任何权限，或者如果 `prompt=consent` 提供了参数，则会为客户端应用程序注册的所有范围显示许可提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>示例 1：用户或租户管理员已授予权限

在此示例中，用户或租户管理员已向 `mail.read` 客户端授予了和 `user.read` Microsoft Graph 权限。 

如果客户端请求 `scope=https://graph.microsoft.com/.default` ，则不会显示任何许可提示，无论客户端应用程序的 Microsoft Graph 的注册权限的内容如何。 返回的标记包含范围 `mail.read` 和 `user.read` 。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>示例 2：用户在客户端和资源之间未授予权限

在此示例中，用户未在客户端和 Microsoft Graph 之间授予同意。 客户端已经注册了权限 `user.read` 和 `contacts.read` 。 它还针对 Azure Key Vault 范围注册 `https://vault.azure.net/user_impersonation` 。 

当客户端请求令牌时 `scope=https://graph.microsoft.com/.default` ，用户将看到 `user.read` 范围、 `contacts.read` 作用域和 Key Vault 范围的同意页面 `user_impersonation` 。 返回的令牌仅包含 `user.read` 和 `contacts.read` 作用域。 它只能用于 Microsoft Graph。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>示例3：用户已同意，并且客户端请求更多范围

在此示例中，用户已为客户端许可 `mail.read`。 客户端已为范围注册 `contacts.read` 。 

当客户端通过使用请求令牌 `scope=https://graph.microsoft.com/.default` 并请求同意时 `prompt=consent` ，用户将看到所有 (的许可页面，并且仅) 应用程序注册的权限。 `contacts.read`范围在同意页上，但 `mail.read` 不是。 返回的令牌用于 Microsoft Graph。 它包含 `mail.read` 和 `contacts.read` 。

### <a name="using-the-default-scope-with-the-client"></a>对客户端使用 /.default 范围

在某些情况下，客户端可以请求其自己的 `/.default` 作用域。 以下示例演示了这种情况。

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

此代码示例将为所有已注册的权限（如果前面的同意说明并适用于方案）生成许可页 `/.default` 。 然后，代码将返回 `id_token` ，而不是访问令牌。  

此行为适用于从 Azure AD 身份验证库 (ADAL) 迁移到 Microsoft 身份验证库 (MSAL) 的一些旧客户端。 面向 Microsoft 标识平台的新客户 *端不应* 使用此设置。

### <a name="client-credentials-grant-flow-and-default"></a>客户端凭据授权流和“/.default”  

的另一个用途 `/.default` 是在非交互式应用程序（如使用 [客户端凭据](v2-oauth2-client-creds-grant-flow.md)授予流来调用 web API）的非交互式应用程序中 (或) *角色* 请求应用程序权限。

若要为 web API 创建 (角色) 应用程序权限，请参阅 [在应用程序中添加应用程序角色](howto-add-app-roles-in-azure-ad-apps.md)。

客户端应用中的客户端凭据请求 *必须* 包含 `scope={resource}/.default` 。 此处， `{resource}` 是应用程序打算调用的 WEB API。 *不* 支持使用单独的应用程序权限 (角色发出客户端凭据请求) 。 为该 web API 授予的 (角色) 的所有应用程序权限都包含在返回的访问令牌中。

若要授予对所定义的应用程序权限的访问权限，包括授予对应用程序的管理员许可，请参阅 [配置客户端应用程序以访问 WEB API](quickstart-configure-app-access-web-apis.md)。

### <a name="trailing-slash-and-default"></a>尾部斜杠和 /.default

某些资源 Uri 有尾随正斜杠，例如， `https://contoso.com/` 与相反 `https://contoso.com` 。 尾随斜杠会导致令牌验证问题。 当 () 为 Azure 资源管理器请求令牌时，主要会出现问题 `https://management.azure.com/` 。 在这种情况下，资源 URI 上的尾随斜杠表示请求令牌时必须存在斜杠。  因此，当你请求令牌 `https://management.azure.com/` 并使用时 `/.default` ，你必须请求 `https://management.azure.com//.default` (注意到双斜杠！ ) 。 通常情况下，如果验证令牌是否正在被颁发，并且该令牌被接受它的 API 拒绝，请考虑添加另一个正斜杠，然后重试。 

## <a name="troubleshooting-permissions-and-consent"></a>权限和许可故障排除

有关故障排除步骤，请参阅对 [应用程序执行许可时出现意外错误](../manage-apps/application-sign-in-unexpected-user-consent-error.md)。

## <a name="next-steps"></a>后续步骤

* [Microsoft 标识平台中的 ID 令牌](id-tokens.md)
* [Microsoft 标识平台中的访问令牌](access-tokens.md)
