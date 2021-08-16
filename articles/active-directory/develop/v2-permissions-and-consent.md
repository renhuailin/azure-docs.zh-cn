---
title: Microsoft 标识平台的范围、权限和同意
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
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102472"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft 标识平台中的权限和许可

与 Microsoft 标识平台集成的应用程序遵循的授权模型可让用户和管理员控制数据的访问方式。 授权模型的实现已在 Microsoft 标识平台上更新。 它更改了应用必须与 Microsoft 标识平台交互的方式。 本文介绍此授权模型的基本概念，包括范围、权限和许可。

## <a name="scopes-and-permissions"></a>范围和权限

Microsoft 标识平台实现 [OAuth 2.0](active-directory-v2-protocols.md) 授权协议。 OAuth 2.0 是可让第三方应用代表用户访问 Web 托管资源的方法。 与 Microsoft 标识平台集成的任何 Web 托管资源都有一个资源标识符（也称为“应用程序 ID URI”）。 

下面是 Microsoft Web 托管资源的一些示例：

* Microsoft Graph： `https://graph.microsoft.com`
* Microsoft 365 邮件 API：`https://outlook.office.com`
* Azure Key Vault：`https://vault.azure.net`

这同样适用于已与 Microsoft 标识平台集成的任何第三方资源。 以上任意资源还可以定义一组可用于将该资源的功能划分成较小区块的权限。 例如， [Microsoft Graph](https://graph.microsoft.com) 已定义执行以下任务及其他任务所需的权限：

* 读取用户的日历
* 写入用户的日历
* 以用户身份发送邮件

由于存在这些类型的权限定义，因此资源可以更精细地控制其数据以及 API 功能的公开方式。 第三方应用可以从用户和管理员请求这些权限，只有在用户或管理员批准该请求之后，应用才能代表用户访问或处理数据。 

将资源的功能分割成小的权限集时，可将第三方应用构建为只请求执行其功能所需的权限。 用户和管理员能够知道应用可以访问哪些数据， 因此可以更加确信应用不会出现恶意行为。 开发人员应始终遵守“最低特权”原则，仅请求分配正常运行应用程序所需的权限。

在 OAuth 2.0 中，这些类型的权限集称为“范围”。 它们通常也称为“权限”。 在 Microsoft 标识平台中，权限以字符串值形式表示。 以 Microsoft Graph 为例，下面是每个权限的字符串值：

* 使用 `Calendars.Read`
* 使用 `Calendars.ReadWrite`
* 使用 `Mail.Send`

应用往往是通过在发往 Microsoft 标识平台授权终结点的请求中指定范围来请求这些权限。 但是，某些高特权权限只能通过管理员同意来授予。 可以通过使用[管理员同意终结点](#admin-restricted-permissions)来请求或授予这些权限。 请继续阅读以了解详细信息。

## <a name="permission-types"></a>权限类型

Microsoft 标识平台支持两种类型的权限：委托的权限和应用程序权限 。

* **委托的权限** 由包含登录用户的应用使用。 对于这些应用，由用户或管理员同意应用请求的权限， 并向应用授予委托的权限，以便该应用在对目标资源进行调用时可充当登录的用户。 

    某些委托的权限可由非管理用户同意， 但某些高特权权限需要[管理员同意](#admin-restricted-permissions)。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure Active Directory (Azure AD) 中的管理员角色权限](../roles/permissions-reference.md)。

* 应用程序权限由无需存在登录用户即可运行的应用（例如，以后台服务或守护程序形式运行的应用）使用。 只有[管理员才能同意](#requesting-consent-for-an-entire-tenant)应用程序权限。

有效权限是你的应用在对目标资源发出请求时拥有的权限。 必须了解委托权限、为应用授予的应用程序权限，以及在应用调用目标资源时为应用授予的有效权限之间的差异。

- 对于委托的权限，应用的有效权限是授予应用的委托权限（通过同意的方式）与当前登录用户特权的最低特权交集。 应用的特权永远不会超过登录用户的特权。 

   在组织内部，可以通过策略或者一个或多个管理员角色的成员身份来确定登录用户的特权。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure AD 中的管理员角色权限](../roles/permissions-reference.md)。

   例如，假设为应用授予了 Microsoft Graph 中的 _User.ReadWrite.All_ 委托权限。 此权限在名义上会授予应用读取和更新组织中每个用户的个人资料的权限。 如果登录的用户是全局管理员，则应用可以更新组织中每个用户的个人资料。 但是，如果登录的用户没有管理员角色，则应用只能更新登录的用户的个人资料。 应用无法更新组织中其他用户的个人资料，因为它有权代表的用户没有这些特权。

- 对于应用程序权限，应用的有效权限是权限默示的整个级别的特权。 例如，拥有 _User.ReadWrite.All_ 应用程序权限的应用可以更新组织中每个用户的个人资料。

## <a name="openid-connect-scopes"></a>OpenID Connect 范围

OpenID Connect 的 Microsoft 标识平台实现具有一些已明确定义并托管在 Microsoft Graph 上的范围：`openid`、`email`、`profile` 和 `offline_access`。 不支持 `address` 和 `phone` OpenID Connect 范围。

如果请求 OpenID Connect 范围和令牌，你会获得一个用于调用 [UserInfo 终结点](userinfo.md)的令牌。

### <a name="openid"></a>openid

如果应用使用 [OpenID Connect](active-directory-v2-protocols.md) 登录，它必须请求 `openid` 范围。 `openid` 范围作为“登录”权限显示在工作帐户同意页上。 在个人 Microsoft 帐户同意页上，它显示为“使用你的 Microsoft 帐户查看配置文件并连接到应用和服务”权限。 

应用可使用此权限以 `sub` 声明的形式接收用户的唯一标识符。 此权限还会向应用提供对 UserInfo 终结点的访问权限。 可以在 Microsoft 标识平台令牌终结点上使用 `openid` 范围来获取 ID 令牌。 应用可以使用这些令牌进行身份验证。

### <a name="email"></a>电子邮件

`email` 范围可与 `openid` 范围以及任何其他范围一起使用。 它以 `email` 声明的形式向应用提供对用户主要电子邮件地址的访问权限。 

仅当某个电子邮件地址与用户帐户相关联（并非总是如此）时， `email` 声明才包含在令牌中。 如果应用使用 `email` 范围，则应用需要能够处理令牌中不存在 `email` 声明的情况。

### <a name="profile"></a>个人资料

`profile` 范围可与 `openid` 范围以及任何其他范围一起使用。 它向应用提供对大量有关该用户的信息的访问权限。 可访问的信息包括但不限于用户的名字、姓氏、首选用户名和对象 ID。 

有关特定用户的 `id_tokens` 参数中提供的 `profile` 声明的完整列表，请参阅 [`id_tokens` 参考](id-tokens.md)。

### <a name="offline_access"></a>offline_access

[`offline_access` 范围](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) 可让应用长时间代表用户访问资源。 在同意页上，此范围显示为“维持对已授予访问权限的数据的访问权限”权限。 

用户批准 `offline_access` 范围后，应用可接收来自 Microsoft 标识平台令牌终结点的刷新令牌。 刷新令牌的生存期较长。 旧的访问令牌过期时，应用可以获取新的访问令牌。

> [!NOTE]
> 目前，此权限会出现在所有同意页上，即使对于那些不提供刷新令牌的流（例如[隐式流](v2-oauth2-implicit-grant-flow.md)），也是这种情况。 该设置解决的是此类情况：客户端可以从隐式流开始，然后转到需要刷新令牌的代码流。

在 Microsoft 标识平台上（向 v2.0 终结点发出的请求），应用程序必须显式请求 `offline_access` 范围才能接收刷新令牌。 因此，在 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)中兑换授权代码时，你从 `/token` 终结点接收的只是访问令牌。 

访问令牌在短期内有效。 它的有效期通常为一小时。 到时，应用需要将用户重定向回到 `/authorize` 终结点以获取新的授权代码。 此重定向期间，用户可能需要再次输入其凭据或重新同意权限，具体取决于应用类型。

有关如何获取及使用刷新令牌的详细信息，请参阅 [Microsoft 标识平台协议参考](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>请求单个用户的许可

在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授权请求中，应用可以使用 `scope` 查询参数来请求所需的权限。 例如，当用户登录应用时，应用会发送类似于以下示例的请求 （添加换行符是为了方便阅读）。

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

`scope` 参数是应用程序所请求的委托权限列表（以空格分隔）。 每个权限都是通过将权限值附加到资源的标识符（应用程序 ID URI）来指示的。 在该请求示例中，应用需要相应的权限来读取用户的日历，以及以用户身分发送邮件。

在用户输入其凭据之后，Microsoft 标识平台将检查是否有匹配的用户同意记录。 如果用户过去未曾同意所请求权限的任何一项，并且管理员尚未代表整个组织同意这些权限，则 Microsoft 标识平台会请求用户授予请求的权限。

在此期间，`offline_access`（“维持对已授予访问权限的数据的访问权限”）权限和 `user.read`（“登录并读取个人资料”）权限会自动包含在对应用程序的初始同意中。  这些权限通常是应用正常运行所需的权限。 `offline_access` 权限为应用提供对刷新令牌的访问权限，而刷新令牌对本机应用和 Web 应用至关重要。 `user.read` 权限提供对 `sub` 声明的访问权限。 它允许客户端或应用随时间推移正确标识用户并访问基本用户信息。

![显示工作帐户同意的示例屏幕截图。](./media/v2-permissions-and-consent/work_account_consent.png)

当用户批准权限请求时，系统会记录同意。 这样，用户在以后登录到应用程序时就无需再次同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>请求整个租户的许可

组织在购买应用程序的许可证或订阅时，通常需要主动设置该应用程序，以使其可供组织的所有成员使用。 在此过程中，管理员可以代表租户中的任何用户授予对该应用程序的许可。 如果管理员为整个租户授予同意，则组织的用户不会看到应用程序的同意页。

若要为租户中的所有用户请求许可委托的权限，应用可以使用管理员许可终结点。

此外，应用程序必须使用管理员同意终结点来请求应用程序权限。

## <a name="admin-restricted-permissions"></a>管理员限制的权限

可将 Microsoft 资源中的某些高特权权限设置为受管理员限制。 下面是这些类型的权限的一些示例：

* 使用 `User.Read.All` 读取所有用户的完整个人资料
* 使用 `Directory.ReadWrite.All`
* 使用 `Groups.Read.All` 读取组织目录中的所有组

尽管使用者用户可以授予应用程序对此类数据的访问权限，但组织用户无法授予对同一公司敏感数据集的的访问权限。 如果应用程序向组织用户请求访问以下权限之一，用户会收到错误消息，指出他们未经授权，无法许可应用的权限。

如果你的应用需要受管理员限制的权限的范围，则组织的管理员必须代表组织的用户同意这些范围。 对于为自己无法授予的权限请求同意的用户，若要避免向其显示提示，可以让你的应用使用管理员同意终结点。 下一部分介绍管理员同意终结点。

如果应用程序请求高特权的委托权限，而管理员通过管理员同意终结点授予这些权限，则会为租户中的所有用户授予同意。

如果应用程序请求应用程序权限，而管理员通过管理员同意终结点授予这些权限，则不会代表任何特定用户进行此授权。 而是直接为客户端应用程序授予权限。 这些类型的权限只由在后台运行的守护程序服务和其他非交互式应用程序使用。

## <a name="using-the-admin-consent-endpoint"></a>使用管理员许可终结点

使用管理员同意终结点授予管理员同意后，操作便完成了。 用户不需执行任何进一步的操作。 授予管理员同意后，用户可以通过典型的授权流获得访问令牌。 生成的访问令牌将具有同意的权限。

当全局管理员使用你的应用程序并被定向到授权终结点时，Microsoft 标识平台会检测用户的角色， 并询问全局管理员是否要代表整个租户同意你请求的权限。 你可以改用一个专用的管理员同意终结点，这样就可以主动请求管理员代表整个租户授予权限。 请求应用程序权限时，也必须使用此终结点。 不能使用授权终结点来请求应用程序权限。

如果你遵循了这些步骤，则应用就能为租户中的所有用户请求权限，包括受管理员限制的范围。 此操作是一个高特权操作， 仅当对于你的方案而言为必需时才应使用。

若要查看实现这些步骤的代码示例，请参阅 GitHub 中的[受管理员限制的范围示例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在应用注册门户中请求权限

在应用注册门户中，应用程序可以列出其所需的权限，包括委托的权限和应用程序权限。 此设置允许使用 `/.default` 范围和 Azure 门户的“授予管理员同意”选项。  

通常，这些权限应该是为给定的应用程序静态定义的。 它们应该是应用会以动态或增量方式请求的权限的超集。

> [!NOTE]
>只能通过使用 [`/.default`](#the-default-scope) 来请求应用程序权限。 因此，如果应用需要应用程序权限，请确保这些权限在应用注册门户中列出。

配置应用程序的静态请求权限列表：

1. 在 <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure 门户 - 应用注册</a> 快速入门体验中转到你的应用程序。
1. 选择一个应用程序，或[创建一个应用](quickstart-register-app.md)（如尚未创建）。
1. 在应用程序的“概述”页的“管理”下，选择“API 权限” > “添加权限”。   
1. 从可用 API 的列表中选择“Microsoft Graph”。 然后添加应用所需的权限。
1. 选择“添加权限”。

### <a name="recommended-sign-the-user-in-to-your-app"></a>建议：让用户登录到应用

在构建使用管理员许可终结点的应用程序时，应用通常需要一个页面或视图，使管理员能够批准应用的权限。 此页可以是：

* 应用的注册流的一部分。
* 应用的设置的一部分。
* 专用的“连接”流。 

在许多情况下，合理的结果是应用只在用户使用工作 Microsoft 帐户或学校 Microsoft 帐户登录之后才显示此“连接”视图。

将用户登录到应用时，可以先确定管理员所属的组织，然后要求管理员批准所需的权限。 尽管严格说来此步骤不是必需的，但它有助于为组织用户带来更直观的体验。 

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
| `tenant` | 必须 | 要向其请求权限的目录租户。 可使用 GUID 或易记名称格式提供它， 也可以采用常规方式使用组织来引用它，如示例所示。 请勿使用“common”，因为除非在租户的上下文中，否则个人帐户无法提供管理员同意。 为了确保与管理租户的个人帐户的兼容性最佳，请尽可能使用租户 ID。 |
| `client_id` | 必需 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验分配给你的应用的应用程序（客户端）ID。 |
| `redirect_uri` | 必须 |要向其发送响应，供应用处理的重定向 URI。 必须与在应用注册门户中注册的重定向 URI 之一完全匹配。 |
| `state` | 建议 | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 使用该状态可在身份验证请求出现之前，在应用中编码用户的状态信息，例如用户过去所在的页面或视图。 |
|`scope`        | 必须        | 定义应用程序请求的权限集。 范围可以是静态范围（使用 [`/.default`](#the-default-scope)），也可以是动态范围。  此集可以包括 OpenID Connect 范围（`openid`、`profile`、`email`）。 如果需要应用程序权限，必须使用 `/.default` 来请求静态配置的权限列表。  |


此时，Azure AD 要求租户管理员登录，以完成请求。 系统会要求管理员批准你在 `scope` 参数中请求的所有权限。  如果你使用了静态 (`/.default`) 值，则它的功能将类似于 v1.0 管理员同意终结点，并且它会请求对应用所需权限中找到的所有范围的同意。

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

如果管理员未批准应用的权限，失败响应将如下所示：

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 参数 | 说明 |
| --- | --- |
| `error` | 错误代码字符串，可用于对发生的错误类型进行分类。 它还可用于对错误做出响应。 |
| `error_description` | 可帮助开发人员识别错误根本原因的具体错误消息。 |

从管理员许可终结点收到成功响应后，应用便已获得所请求的权限。 接下来，可以请求所需的资源令牌。

## <a name="using-permissions"></a>使用权限

用户同意应用的权限之后，应用即可获取访问令牌，这些令牌表示应用以某种身份访问资源的权限。 访问令牌只能用于单个资源， 但其内部编码了应用已被授予的该资源的每一项权限。 若要获取访问令牌，应用可以对 Microsoft 标识平台令牌终结点发出类似于以下请求：

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

可在资源的 HTTP 请求中使用生成的访问令牌。 该令牌以可靠方式向资源表明，应用已获得适当权限，可执行特定的任务。

有关 OAuth 2.0 协议以及如何获取访问令牌的详细信息，请参阅 [Microsoft 标识平台终结点协议参考](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 范围

可以使用 `/.default` 范围，帮助将应用从 v1.0 终结点迁移到 Microsoft 标识平台终结点。 `/.default` 范围是内置的，适用于每个引用在应用程序注册时配置的权限静态列表的应用程序。 

`scope` 值为 `https://graph.microsoft.com/.default` 时，其作用与 v1.0 终结点上的 `resource=https://graph.microsoft.com` 相同。 在请求中指定 `https://graph.microsoft.com/.default` 范围即表示应用程序在请求一个访问令牌，该令牌包含你在应用注册门户中为应用选择的每个 Microsoft Graph 权限的范围。 范围是使用资源 URI 和 `/.default` 构造的。 因此，如果资源 URI 为 `https://contosoApp.com`，则请求的范围为 `https://contosoApp.com/.default`。  有关必须包含另一个斜杠才能正确请求令牌的情况，请参阅[有关尾随斜杠的部分](#trailing-slash-and-default)。

`/.default` 范围可用于任何 OAuth 2.0 流， 但在[代理流](v2-oauth2-on-behalf-of-flow.md)和[客户端凭据流](v2-oauth2-client-creds-grant-flow.md)中是必需的。 在使用 v2 管理员同意终结点来请求应用程序权限时，你也需要它。

客户端不能在单个请求中合并静态同意 (`/.default`) 和动态同意。 因此 `scope=https://graph.microsoft.com/.default+mail.read` 会导致错误，因为它合并了范围类型。

### <a name="default-and-consent"></a>/.default 和 consent

`/.default` 范围也可触发 `prompt=consent` 的 v1.0 终结点行为。 它会针对应用程序注册的所有权限请求同意，而不考虑资源。 如果它已作为请求的一部分包含在内，`/.default` 范围会返回一个令牌，该令牌包含已请求的资源的范围。

### <a name="default-when-the-user-has-already-given-consent"></a>用户已授予许可时为 /.default

`/.default` 范围在功能上等同于以 `resource` 为中心的 v1.0 终结点的行为。 它也带有 v1.0 终结点的同意行为。 也就是说，仅当用户在客户端和资源之间未授予任何权限的情况下，`/.default` 才触发同意提示。 

如果存在任何此类同意，则返回的令牌会包含用户为该资源授予的所有范围。 但是，如果尚未授予任何权限，或者已提供了 `prompt=consent` 参数，则会对客户端应用程序注册的所有范围显示同意提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>示例 1：用户或租户管理员已授予权限

在此示例中，用户或租户管理员向客户端授予了 `mail.read` 和 `user.read` Microsoft Graph 权限。 

如果客户端请求 `scope=https://graph.microsoft.com/.default`，则不会显示任何同意提示，不管客户端应用程序已为 Microsoft Graph 注册的权限的内容如何。 返回的令牌包含范围 `mail.read` 和 `user.read`。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>示例 2：用户在客户端和资源之间未授予权限

在此示例中，用户尚未在客户端和 Microsoft Graph 之间授予同意。 客户端已针对权限 `user.read` 和 `contacts.read` 进行注册。 它还针对 Azure Key Vault 范围 `https://vault.azure.net/user_impersonation` 进行了注册。 

当客户端请求用于 `scope=https://graph.microsoft.com/.default` 的令牌时，用户会看到针对 `user.read` 范围、`contacts.read` 范围和 Key Vault `user_impersonation` 范围的同意页面。 返回的令牌仅包含 `user.read` 和 `contacts.read` 范围。 它只能针对 Microsoft Graph 进行使用。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>示例 3：用户已同意，客户端请求更多范围

在此示例中，用户已为客户端许可 `mail.read`。 客户端已针对 `contacts.read` 范围进行注册。 

当客户端通过使用 `scope=https://graph.microsoft.com/.default` 请求令牌并通过 `prompt=consent` 请求同意时，用户会看到一个同意页面，该页面显示由应用程序注册的所有权限（且仅显示这些权限）。 `contacts.read` 范围在同意页面上，但 `mail.read` 不在。 返回的令牌用于 Microsoft Graph。 它包含 `mail.read` 和 `contacts.read`。

### <a name="using-the-default-scope-with-the-client"></a>对客户端使用 /.default 范围

在某些情况下，客户端可以请求其自己的 `/.default` 范围。 以下示例演示了这种情况。

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

此代码示例为所有已注册的权限生成同意页，前提是前面有关同意和 `/.default` 的说明适用于此方案。 然后，此代码返回 `id_token`，而不是访问令牌。  

此行为适合某些从 Azure AD 身份验证库 (ADAL) 迁移到 Microsoft 身份验证库 (MSAL) 的旧客户端。 此设置不应由面向 Microsoft 标识平台的新客户端使用。

### <a name="client-credentials-grant-flow-and-default"></a>客户端凭据授权流和“/.default”  

`/.default` 的另一种用法是在非交互式应用程序（例如，使用[客户端凭据](v2-oauth2-client-creds-grant-flow.md)授权流来调用 Web API 的守护程序应用）中请求应用程序权限（或角色）。

若要为 Web API 创建应用程序权限（角色），请参阅[在应用程序中添加应用角色](howto-add-app-roles-in-azure-ad-apps.md)。

客户端应用中的客户端凭据请求必须包含 `scope={resource}/.default`。 在这里，`{resource}` 是应用要调用的 Web API。 不支持使用单个应用程序权限（角色）发出客户端凭据请求。 为该 Web API 授予的所有应用程序权限（角色）都包含在返回的访问令牌中。

若要授予对所定义的应用程序权限的访问权限，包括为应用程序授予管理员同意，请参阅[配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md)。

### <a name="trailing-slash-and-default"></a>尾部斜杠和 /.default

某些资源 URI 包含尾随正斜杠，例如 `https://contoso.com/`（不是 `https://contoso.com`）。 尾随斜杠可能导致令牌验证出现问题。 问题主要发生在为 Azure 资源管理器 (`https://management.azure.com/`) 请求令牌时。 在这种情况下，资源 URI 上的尾随斜杠意味着请求令牌时必须存在此斜杠。  因此，在请求 `https://management.azure.com/` 的令牌并使用 `/.default` 时，必须请求 `https://management.azure.com//.default`（注意双斜杠！）。 一般情况下，如果你确认令牌被颁发，但 API 在应该接受令牌的情况下拒绝了它，请考虑添加另一个正斜杠并重试。 

## <a name="troubleshooting-permissions-and-consent"></a>权限和许可故障排除

有关故障排除步骤，请参阅[针对应用程序执行同意操作时出现意外错误](../manage-apps/application-sign-in-unexpected-user-consent-error.md)。

## <a name="next-steps"></a>后续步骤

* [Microsoft 标识平台中的 ID 令牌](id-tokens.md)
* [Microsoft 标识平台中的访问令牌](access-tokens.md)
