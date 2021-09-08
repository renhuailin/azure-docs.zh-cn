---
title: 添加应用角色并从令牌获取它们 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何在注册到 Azure Active Directory 的应用程序中添加应用角色、如何向这些角色分配用户和组，以及如何在令牌的“角色”声明中接收它们。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2021
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f213c6b41a0a34850b35fb24dd4288c00693511
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039331"
---
# <a name="add-app-roles-to-your-application-and-receive-them-in-the-token"></a>将应用角色添加到应用程序中并在令牌中接收它们

基于角色的访问控制 (RBAC) 是一种常用的机制，用于在应用程序中强制进行授权。 使用 RBAC 时，管理员将权限授予角色而不是单个用户或组。 然后，管理员再将角色分配给不同的用户和组，以便控制用户对特定内容和功能的访问。

将 RBAC 与应用程序角色和角色声明配合使用，开发人员就可以安全地在应用中强制实施授权，相当轻松。

另一种方法是使用 Azure AD 组和组声明，如 GitHub 上的 [active-directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) 代码示例中所示。 Azure AD 组和应用程序角色不会互相排斥；它们可以配合使用，进行更精细的访问控制。

## <a name="declare-roles-for-an-application"></a>为应用程序声明角色

使用 [Azure 门户](https://portal.azure.com)定义应用角色。 应用角色通常在表示服务、应用或 API 的应用程序注册上定义。 当用户登录到应用程序时，Azure AD 会针对每个角色发出一个 `roles` 声明。这些角色包括单独授予用户或服务主体的，以及通过组成员身份获得的。 这可用于实现基于声明的授权。 应用角色可以分配[给一个用户或一组用户](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)。 应用角色也可以分配给另一个应用程序的服务主体，或者[分配给托管标识的服务主体](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)。

> [!IMPORTANT]
> 目前，如果将服务主体添加到组，然后将应用角色分配给该组，则 Azure AD 不会将 `roles` 声明添加到它颁发的令牌中。

可以通过两种方法使用 Azure 门户来声明应用角色：

- [应用程序角色 UI](#app-roles-ui)
- [应用清单编辑器](#app-manifest-editor)

你添加的角色数量计入 Azure Active Directory 强制实施的应用程序清单限制。 有关这些限制的信息，请参阅 [Azure Active Directory 应用程序清单参考](reference-app-manifest.md)的[清单限制](./reference-app-manifest.md#manifest-limits)部分。

### <a name="app-roles-ui"></a>应用角色 UI

若要使用 Azure 门户的用户界面创建应用角色，请执行以下操作：

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含要向其添加应用角色的应用注册的 Azure Active Directory 租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”下，选择“应用注册”，然后选择要在其中定义应用角色的应用程序 。
1. 选择“应用角色”，然后选择“创建应用角色”。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure 门户中应用注册的应用角色窗格":::

1. 在“创建应用角色”窗格中，输入角色的设置。 图像下面的表格描述每个设置及其参数。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Azure 门户中应用注册的“应用角色创建上下文”窗格":::

   | 字段                                    | 说明                                                                                                                                                                                                                                                                                                       | 示例                       |
   | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
   | **显示名称**                         | 管理员同意和应用分配体验中显示的应用角色显示名称。 此值可以包含空格。                                                                                                                                                                                    | `Survey Writer`               |
   | **允许的成员类型**                 | 指定是否可以将此应用角色分配给用户和/或应用程序。<br/><br/>可供 `applications` 使用时，应用角色会在应用注册的“管理”部分 >“API 权限”>“添加权限”>“我的 API”>“选择 API”>“应用程序权限”中显示为应用程序权限 。 | `Users/Groups`                |
   | **值**                                | 指定应用程序在令牌中所需的角色声明的值。 该值应与应用程序代码中引用的字符串完全匹配。 该值不能包含空格。                                                                                                          | `Survey.Create`               |
   | **说明**                          | 在管理应用分配和同意体验期间显示的应用角色的更详细说明。                                                                                                                                                                                                        | `Writers can create surveys.` |
   | **要启用此应用角色吗？** | 指定是否启用应用角色。 若要删除应用角色，请取消选中此复选框，并在尝试删除操作之前应用更改。                                                                                                                                                             | _已选中_                     |

1. 选择“应用”以保存所做的更改。

### <a name="app-manifest-editor"></a>应用清单编辑器

如需通过直接编辑清单来添加角色，请执行以下操作：

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含要向其添加应用角色的应用注册的 Azure Active Directory 租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”下，选择“应用注册”，然后选择要在其中定义应用角色的应用程序 。
1. 再次在“管理”下选择“清单” 。
1. 编辑应用清单，方法是先查找 `appRoles` 设置，然后添加应用程序角色。 可以针对 `users` 和/或 `applications` 来定义应用角色。 以下 JSON 代码片段演示了这两者的示例。
1. 保存清单。

清单中的每个应用角色定义的 `id` 值必须具有唯一 GUID。

每个应用角色定义的 `value` 属性应该与应用程序的代码中使用的字符串完全匹配。 `value` 属性不能包含空格。 如果包含空格，则在保存清单时会收到错误。

#### <a name="example-user-app-role"></a>示例：用户应用角色

此示例定义了一个名为 `Writer` 的应用角色，可将其分配给 `User`：

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>示例：应用程序应用角色

可供 `applications` 使用时，应用角色会在应用注册的“管理”部分 >“API 权限”>“添加权限”>“我的 API”>“选择 API”>“应用程序权限”中显示为应用程序权限 。

此示例显示一个以 `Application` 为目标的应用角色：

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>将用户和组分配到角色

在应用程序中添加应用角色以后，即可将此角色分配给用户和组。 可以通过门户的 UI 为用户或组分配角色，也可以使用 [Microsoft Graph](/graph/api/user-post-approleassignments) 以编程方式进行分配。 当分配到各种应用角色的用户登录到应用程序时，其令牌会在 `roles` 声明中具有其分配的角色。

若要使用 Azure 门户将用户和组分配给角色，请执行以下操作：

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 在 Azure Active Directory 的左侧导航菜单中选择“企业应用程序” 。
1. 选择“所有应用程序”，查看所有应用程序的列表。 如果应用程序未显示在列表中，请使用“所有应用程序”列表顶部的筛选器来限制此列表，或者在列表中向下滚动寻找你的应用程序。
1. 选择一个应用程序，以便在其中为角色分配用户或安全组。
1. 在“管理”下，选择“用户和组”。
1. 选择“添加用户”，打开“添加分配”窗格 。
1. 在“添加分配”窗格中，选择“用户和组”选择器。 随即显示用户和安全组的列表。 可以搜索特定用户或组，并选择列表中显示的多个用户和组。
1. 选择用户和组后，请选择“选择”按钮以继续。
1. 在“添加分配”窗格中选择“选择角色” 。 随即显示已为应用程序定义的所有角色。
1. 选择一个角色，然后选择“选择”按钮。
1. 选择“分配”按钮可完成将用户和组分配到应用的操作。

确认已添加的用户和组显示在“用户和组”列表中。

## <a name="assign-app-roles-to-applications"></a>向应用程序分配应用角色

在应用程序中添加应用角色后，可以使用 Azure 门户或以编程方式使用 [Microsoft Graph](/graph/api/user-post-approleassignments) 将应用角色分配给客户端应用。

将应用角色分配给应用程序时，将创建应用程序权限。 应用程序权限通常由需要进行身份验证和经授权的 API 调用的守护程序应用或后端服务使用，而无需用户的交互。

若要使用 Azure 门户将应用角色分配给应用程序，请执行以下操作：

1. 登录到 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 在 Azure Active Directory 的左侧导航菜单中，选择“应用注册” 。
1. 选择“所有应用程序”，查看所有应用程序的列表。 如果应用程序未显示在列表中，请使用“所有应用程序”列表顶部的筛选器来限制此列表，或者在列表中向下滚动寻找你的应用程序。
1. 选择要向其分配应用角色的应用程序。
1. 选择“API 权限” > “添加权限” 。
1. 选择“我的 API”选项卡，然后选择为其定义了应用角色的应用。
1. 选择“应用程序权限”。
1. 选择要分配的角色。
1. 选择“添加权限”按钮完成角色添加。

新添加的角色应显示在应用注册的“API 权限”窗格中。

#### <a name="grant-admin-consent"></a>授予管理员同意

由于这些是应用程序权限，而不是委派的权限，因此管理员必须授予同意，才能使用分配给应用程序的应用角色。

1. 在应用注册的“API 权限”窗格中，选择“为 \<tenant name\> 授予管理员同意” 。
1. 当系统提示授予所请求权限的同意时，选择“是”。

“状态”列应反映已为 \<tenant name\> 授予同意 。

## <a name="use-app-roles-in-your-web-api"></a>在 Web API 中使用应用角色

定义应用角色并将其分配给用户、组或应用程序后，下一步是向 Web API 添加代码，以便在调用 API 时检查这些角色。 也就是说，当客户端应用请求你决定需要授权的 API 操作时，API 的代码必须验证范围是否在客户端应用调用中提供的访问令牌中。

若要了解如何将授权添加到 Web API，请参阅[受保护的 Web API：验证范围和应用角色](scenario-protected-web-api-verification-scope-app-roles.md)。

## <a name="app-roles-vs-groups"></a>应用角色和组

尽管可以使用应用角色或组进行授权，但它们之间的关键差异可能会影响你将哪一种用于你的场景的决定。

| 应用角色                                                                                                    | 组                                                      |
| ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| 它们特定于应用程序，并在应用注册中定义。 它们随应用程序一起移动。 | 它们不是特定于应用，而是特定于 Azure AD 租户。 |
| 删除应用注册时，应用角色会被删除。                                                | 即使删除了应用，组也会保持不变。            |
| 在 `roles` 声明中提供。                                                                               | 在 `groups` 声明中提供。                                 |

开发人员可以使用应用角色来控制用户是否可以登录到应用，或者应用是否可以获取 Web API 的访问令牌。 为了将此安全控件扩展到组，开发人员和管理员还可以将安全组分配给应用角色。

当开发人员想要描述和控制其应用中的授权参数时，应用角色是首选。 例如，使用组进行授权的应用将在下一个租户中中断，因为组 ID 和名称可能不同。 使用应用角色的应用处于安全状态。 事实上，出于同样的原因，将组分配给应用角色在 SaaS 应用中非常流行。

## <a name="next-steps"></a>后续步骤

通过以下资源详细了解应用角色。

- GitHub 上的代码示例
  - [使用组和组声明向 ASP.NET Core Web 应用添加授权](https://aka.ms/groupssample)
  - [Angular 单页应用程序 (SPA) 调用 .NET Core Web API 并使用应用角色和安全组](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl)
  - [React 单页应用程序 (SPA) 调用 Node.js Web API 并使用应用角色和安全组](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl)
- 参考文档
  - [Azure AD 应用清单](./reference-app-manifest.md)
  - [Azure AD 访问令牌](access-tokens.md)
  - [Azure AD ID 令牌](id-tokens.md)
  - [向应用提供可选声明](active-directory-optional-claims.md)
- 视频：[在应用程序中通过 Microsoft 标识平台实现授权](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
