---
title: 在 Azure Active Directory 中为应用程序配置组声明 | Microsoft Docs
description: 有关如何配置要在 Azure AD 中使用的组声明的信息。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/31/2021
ms.author: billmath
author: billmath
ms.openlocfilehash: 0621cc58b5cf76505de5e1f914114ad601e7a9bf
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123316176"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>在 Azure Active Directory 中为应用程序配置组声明

Azure Active Directory 可以在应用程序内使用的令牌中提供用户组成员身份信息。  支持两种主要模式：

- 由组的 Azure Active Directory 对象标识符 (OID) 特性标识的组
- 由 Active Directory (AD) 同步组和用户的 sAMAccountName 或 GroupSID 特性标识的组

> [!IMPORTANT]
> 对于此功能，需要注意几个事项：
>
> - 支持使用从本地同步的 sAMAccountName 和安全标识符 (SID) 特性的目的是为了能够从 AD FS 和其他标识提供者移动现有应用程序。 在 Azure AD 中管理的组不包含发出这些声明所需的特性。
> - 在较大的组织中，某个用户所属的组的数量可能会超过 Azure Active Directory 可在令牌中添加的组数限制。 SAML 令牌限制为 150 个组，JWT 限制为 200 个组。 这可能会导致不可预知的结果。 如果你的用户是大量组的成员，我们建议使用相应的选项，将声明中发出的组限制为与应用程序相关的组。
> - 如果令牌是通过隐式流颁发的，则组声明具有 5 组限制。 如果用户位于超过 5 个组中，则通过隐式流请求的令牌将只有 "hasgroups":true 声明。
> - 对于新的应用程序开发，或者在可以为其配置应用程序以及不需要嵌套组支持的情况下，我们建议使应用内授权基于应用程序角色，而不是基于组。  这种做法可以限制需要传入令牌的信息量，且更安全，而且还能将用户分配与应用配置分隔开来。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>从 AD FS 和其他标识提供者迁移的应用程序的组声明

许多已配置为使用 AD FS 进行身份验证的应用程序依赖于 Windows AD 组特性形式的组成员身份信息。   这些特性是可按域名限定的组 sAMAccountName，或者是 Windows 组安全标识符 (GroupSID)。  当应用程序与 AD FS 联合时，AD FS 将使用 TokenGroups 函数来检索用户的组成员身份。

已从 AD FS 移动的应用需要相同格式的声明。 可以从 Azure Active Directory 发出包含已从 Active Directory 同步的域限定 sAMAccountName 或 GroupSID（而不是组的 Azure Active Directory objectID）的组和角色声明。

组声明支持的格式为：

- Azure Active Directory 组 ObjectId（可用于所有组）
- sAMAccountName（可用于从 Active Directory 同步的组）
- NetbiosDomain\sAMAccountName（可用于从 Active Directory 同步的组）
- DNSDomainName\sAMAccountName（可用于从 Active Directory 同步的组）
- 本地组安全标识符（可用于从 Active Directory 同步的组）

> [!NOTE]
> sAMAccountName 和本地组 SID 特性仅可用于从 Active Directory 同步的组对象。   它们不可用于在 Azure Active Directory 或 Office365 中创建的组。   在 Azure Active Directory 中配置为获取已同步本地组特性的应用程序只会获取已同步组的这些特性。

## <a name="options-for-applications-to-consume-group-information"></a>供应用程序使用组信息的选项

应用程序可以调用 MS Graph 组终结点来获取经过身份验证的用户的组信息。 此调用确保即使涉及到大量的组，某个用户所属的所有组也仍可供使用。  然后，组枚举可以不考虑令牌大小限制。

但是，如果现有应用程序预期通过声明使用组信息，你可以在 Azure Active Directory 中配置一些不同的声明格式。  请考虑以下选项：

- 将组成员身份用于应用程序内授权时，最好是使用组 ObjectID。 组 ObjectID 在 Azure Active Directory 中不可变且唯一，并可用于所有组。
- 如果使用本地组 sAMAccountName 进行授权，请使用域限定的名称；这样可以减少名称冲突的可能性。 sAMAccountName 在 Active Directory 域中可能是唯一的，但如果有多个 Active Directory 域已与某个 Azure Active Directory 租户同步，则可能会出现多个组同名的情况。
- 请考虑使用[应用程序角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)在组成员身份与应用程序之间提供一个间接层。   然后，应用程序会根据令牌中的角色声明做出内部授权决策。
- 如果将应用程序配置为获取已从 Active Directory 同步的组特性，而某个组并不包含这些特性，则不会将该组包含在声明中。
- 令牌中的组声明包括嵌套组，但使用相应选项将组声明限制为已分配给应用程序的组时除外。  如果某个用户是组 B 的成员，而组 B 是组 A 的成员，则该用户的组声明将包含组 A 和组 B。 当组织中的用户是大量组的成员时，令牌中列出的组数可能会增大令牌大小。  对于 SAML 断言和 JWT，Azure Active Directory 将它在令牌中发出的组数分别限制为 150 个和 200 个。  如果某个用户是更多组的成员，则会忽略这些组，并改为在令牌中包含用于获取组信息的 Graph 终结点的链接。

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>使用从 Active Directory 同步的组特性的先决条件

如果使用 ObjectId 格式，则可以在任何组的令牌中发出组成员资格声明。 若要使用格式并非组 ObjectId 的组声明，必须使用 Azure AD Connect 从 Active Directory 同步组。

可通过两个步骤将 Azure Active Directory 配置为发出 Active Directory 组的组名。

1. 从 Active Directory 同步组名：在 Azure Active Directory 可以在组或角色声明中发出组名或本地组 SID 之前，需要从 Active Directory 同步所需的特性。  必须运行 Azure AD Connect 1.2.70 或更高版本。   低于 1.2.70 的 Azure AD Connect 版本将从 Active Directory 同步组对象，但不会包含所需的组名特性。  请升级到最新版本。

2. 将 Azure Active Directory 中的应用程序注册配置为在令牌中包含组声明：可以在门户的“企业应用程序”部分，或者使用“应用程序注册”部分的“应用程序清单”来配置组声明。  若要在应用程序清单中配置组声明，请参阅下面的“在 Azure Active Directory 应用程序注册中配置组特性”。

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>使用 SSO 配置将组声明添加到 SAML 应用程序的令牌

若要为库或非库 SAML 应用程序配置组声明，请打开“企业应用程序”，在列表中单击该应用程序，选择“单一登录配置”，然后选择“用户特性和声明”  。

单击“添加组声明”

![显示“用户特性和声明”页的屏幕截图，其中选择了“添加组声明”。](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用单选按钮选择要将哪些组包含在令牌中

![显示“组声明”窗口的屏幕截图，其中选择了“安全组”。](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 选择 | 说明 |
|----------|-------------|
| **所有组** | 发出安全组、通讯组列表和角色。  |
| **安全组** | 在组声明中发出用户所属的安全组 |
| **目录角色** | 如果为用户分配了目录角色，这些角色将作为“wids”声明发出（不会发出组声明） |
| **分配给应用程序的组** | 仅发出已显式分配给应用程序并且用户是其成员的组 |

例如，若要发出用户是其成员的所有安全组，请选择“安全组”

![显示“组声明”窗口的屏幕截图，其中选择了“安全组”，并已打开“源特性”下拉菜单。](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要使用已从 Active Directory 同步的 Active Directory 特性（而不是 Azure AD objectID）发出组，请从下拉菜单中选择所需的格式。 声明中只会包含已从 Active Directory 同步的组。

![该屏幕截图显示“源特性”下拉菜单已打开。](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

若要仅发出已分配给应用程序的组，请选择“分配给应用程序的组”

![显示“组声明”窗口的屏幕截图，其中选择了“分配给应用程序的组”。](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

令牌中将包含已分配给应用程序的组。  将忽略用户所属的其他组。  使用此选项时，将不包含嵌套组，并且用户必须是已分配给应用程序的组的直属成员。

要更改分配给应用程序的组，请在“企业应用程序”列表中选择该应用程序，然后在该应用程序的左侧导航菜单中单击“用户和组” 。

有关管理应用程序的组分配的详细信息，请参阅文档[向企业应用分配用户或组](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="advanced-options"></a>高级选项

可以通过“高级选项”下的设置修改组声明的发出方式

自定义组声明的名称：如果选择此设置，则可为组声明指定不同的声明类型。   在“名称”字段中输入声明类型，并在“命名空间”字段中输入声明的可选命名空间。

![显示“高级选项”部分的屏幕截图，其中选择了“自定义组声明的名称”，并输入了“名称”和“命名空间”值。](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些应用程序要求在“role”声明中显示组成员身份信息。 可以选择性地选中“将组作为角色声明发出”框，以将用户的组作为角色发出。

![显示“高级选项”部分的屏幕截图，其中选择了“自定义组声明的名称”和“将组作为角色声明发出”。](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用了将组数据作为角色发出的选项，则角色声明中只会显示组。  分配给用户的任何应用程序角色都不会显示在角色声明中。

### <a name="edit-the-group-claims-configuration"></a>编辑组声明配置

将组声明配置添加到“用户属性和声明”配置后，添加组声明的选项将灰显。若要更改组声明配置，请单击“其他声明”列表中的组声明。

![声明 UI](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>在 Azure AD 应用程序注册中配置组特性

还可以在[应用程序清单](../../active-directory/develop/reference-app-manifest.md)的[可选声明](../../active-directory/develop/active-directory-optional-claims.md)部分配置组声明。

1. 门户 ->“Azure Active Directory”->“应用程序注册”->“选择应用程序”>“清单”

2. 通过更改 groupMembershipClaim 来启用组成员身份声明

有效值为：

| 选择 | 说明 |
|----------|-------------|
| **"All"** | 发出安全组、通讯组列表和角色 |
| **"SecurityGroup"** | 在组声明中发出用户所属的安全组 |
| **"DirectoryRole"** | 如果为用户分配了目录角色，这些角色将作为“wids”声明发出（不会发出组声明） |
| **"ApplicationGroup"** | 仅发出已显式分配给应用程序并且用户是其成员的组 |
| **"None"** | 不返回任何组。（此值不区分大小写，因此也可以指定 none；可以直接在应用程序清单中设置。） |

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   默认情况下，将在组声明值中发出组 ObjectID。  若要将声明值修改为包含本地组特性，或者要将声明类型更改为角色，请按如下所示使用 OptionalClaims 配置：

3. 设置组名配置可选声明。

   如果你希望令牌中的组包含本地 AD 组特性，请在“可选声明”部分指定要将可选声明应用到哪个令牌类型。  可以列出多个令牌类型：

   - OIDC ID 令牌的 idToken
   - OAuth/OIDC 访问令牌的 accessToken
   - SAML 令牌的 Saml2Token。

   > [!NOTE]
   > Saml2Token 类型将应用到 SAML1.1 和 SAML2.0 格式令牌

   对于每个相关的令牌类型，请修改组声明以在清单中使用 OptionalClaims 节。 OptionalClaims 架构如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 可选声明架构 | 值 |
   |----------|-------------|
   | **name：** | 必须是“groups” |
   | **source：** | 未使用。 省略或指定 null |
   | **essential：** | 未使用。 省略或指定 false |
   | **additionalProperties：** | 附加属性列表。  有效选项为“sam_account_name”、“dns_domain_and_sam_account_name”、“netbios_domain_and_sam_account_name”、“emit_as_roles” |

   在 additionalProperties 中，只需要指定“sam_account_name”、“dns_domain_and_sam_account_name”和“netbios_domain_and_sam_account_name”中的一个。  如果存在多个，则将使用第一个，而忽略其他。

   某些应用程序需要角色声明中有关用户的组信息。  要将声明类型从组声明更改为角色声明，请将“emit_as_roles”添加到附加属性。  组值将在角色声明中发出。

   > [!NOTE]
   > 如果使用“emit_as_roles”，则分配了用户的任何已配置应用程序角色不会显示在角色声明中

### <a name="examples"></a>示例

以 dnsDomainName\SAMAccountName 格式将组作为 OAuth 访问令牌中的组名发出

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
```

若要发出以 netbiosDomain\samAccountName 格式作为 SAML 和 OIDC ID 令牌中的角色声明返回的组名，请使用以下代码：

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
}
```

## <a name="next-steps"></a>后续步骤

- [使用组和组声明向 ASP.NET Core Web 应用添加授权（代码示例）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [向企业应用分配用户或组](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [配置角色声明](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
