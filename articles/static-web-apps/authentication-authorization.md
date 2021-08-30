---
title: Azure 静态 Web 应用的身份验证和授权
description: 了解如何使用不同的授权提供程序保护静态应用。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 1ba278b80d75e64b4394bfeb1b98cd0ec2d805a8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469951"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps"></a>Azure 静态 Web 应用的身份验证和授权

Azure 静态 Web 应用提供简化的身份验证体验。 默认情况下，访问一系列已预配置的提供程序或用于 [注册自定义提供程序](./authentication-custom.md) 的选项。

- 任何用户都可以使用已启用的提供程序进行身份验证。
- 登录后，用户默认属于 `anonymous` 和 `authenticated` 角色。
- 授权用户可以通过在 [staticwebapp.config.json 文件](./configuration.md) 中定义的规则获取对受限 [路由](configuration.md#routes) 的访问权限。
- 用户通过特定于提供程序的 [邀请](#invitations)或通过 [自定义的 Azure Active Directory 提供程序注册](./authentication-custom.md) 来加入自定义角色。
- 默认情况下，将启用所有身份验证提供程序。
  - 若要限制身份验证提供程序，请使用自定义路由规则[阻止访问](#block-an-authorization-provider)。
- 已预配置的提供程序包括：
  - Azure Active Directory
  - GitHub
  - Twitter

身份验证和授权的主题明显与路由概念重叠，这在 [应用程序配置指南](configuration.md#routes) 中进行了详细介绍。

## <a name="roles"></a>角色

访问静态 Web 应用的每个用户都属于一个或多个角色。 用户可以属于两个内置角色：

- 匿名：所有用户都自动属于“匿名”角色。
- 已通过身份验证：已登录的所有用户都属于“已通过身份验证”角色。

除了内置角色以外，你还可以创建新角色，通过邀请将其分配给用户，并在 staticwebapp.config.json 文件中引用它们。

## <a name="role-management"></a>角色管理

### <a name="add-a-user-to-a-role"></a>将用户添加到角色

若要将用户添加到角色，请生成允许将用户关联到特定角色的邀请。 角色在 staticwebapp.config.json 文件中定义和维护。

> [!NOTE]
> 可以选择 [注册自定义的 Azure Active Directory 提供程序](./authentication-custom.md)，以避免发出组管理邀请。

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>创建邀请

邀请特定于单个授权提供程序，因此，在选择要支持的提供程序时，请考虑应用的需求。 某些提供程序公开用户的电子邮件地址，而其他提供程序仅提供站点的用户名。

<a name="provider-user-details" id="provider-user-details"></a>

| 授权提供程序 | 公开用户的 |
| ---------------------- | ---------------- |
| Azure Active Directory | 电子邮件地址    |
| GitHub                 | username         |
| Twitter                | username         |

1. 在 [Azure 门户](https://portal.azure.com)中导航到静态 Web 应用资源。
1. 在“设置”下面，单击“角色管理”。
1. 单击“邀请”按钮。
1. 从选项列表中选择“授权提供程序”。
1. 在“被邀请者详细信息”框中添加收件人的用户名或电子邮件地址。
   - 对于 GitHub 和 Twitter，输入用户名。 对于所有其他工具，输入收件人的电子邮件地址。
1. 从“域”下拉列表中选择静态站点的域。
   - 你选择的域是在邀请中显示的域。 如果有与站点关联的自定义域，则可能需要选择自定义域。
1. 在“角色”框中添加以逗号分隔的角色名称列表。
1. 输入希望邀请保持有效的最大小时数。
   - 可能的最大限制为 168 小时，即 7 天。
1. 单击“生成”按钮。
1. 从“邀请链接”框中复制链接。
1. 将邀请链接通过电子邮件的方式发送给相关人员，你授予了该人员访问你的应用的权限。

当用户单击邀请中的链接时，系统将提示他们使用相应的帐户登录。 成功登录后，用户将与所选角色相关联。

> [!CAUTION]
> 请确保路由规则不会与所选的身份验证提供程序冲突。 使用路由规则阻止提供程序会阻止用户接受邀请。

### <a name="update-role-assignments"></a>更新角色分配

1. 在 [Azure 门户](https://portal.azure.com)中导航到静态 Web 应用资源。
1. 在“设置”下面，单击“角色管理”。
1. 在列表中单击该用户。
1. 在“角色”框中编辑角色的列表。
1. 单击“更新”按钮。

### <a name="remove-user"></a>删除用户

1. 在 [Azure 门户](https://portal.azure.com)中导航到静态 Web 应用资源。
1. 在“设置”下面，单击“角色管理”。
1. 在列表中找到该用户。
1. 选中用户行上的复选框。
1. 单击“删除” 按钮。

删除用户时，请记住以下事项：

1. 删除用户会使其权限失效。
1. 全球传播可能需要几分钟时间。
1. 如果用户已重新添加到应用，则 [`userId` 会发生更改](user-information.md)。

## <a name="remove-personal-identifying-information"></a>删除个人身份信息

以最终用户身份向应用程序授予许可时，应用程序可以访问你的电子邮件地址或你的用户名，具体取决于标识提供程序。 提供此信息后，应用程序的所有者可决定如何管理个人身份信息。

最终用户需要联系各个 Web 应用的管理员，才能从他们的系统中撤消此信息。

若要从 Azure 静态 Web 应用平台中删除个人身份信息，并阻止平台提供有关将来请求的此信息，请使用 URL 提交请求：

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

若要阻止平台向各个应用提供有关将来请求的此信息，请将请求提交到以下 URL：

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

备注，如果你使用的是 Azure Active Directory，请使用 `aad` 作为 `<AUTHENTICATION_PROVIDER_NAME>` 占位符的值。

## <a name="system-folder"></a>系统文件夹

Azure 静态 Web 应用使用 `/.auth` 系统文件夹来提供对与授权相关的 API 的访问权限。 请考虑创建[路由规则](configuration.md#routes)来创建友好 URL，而不是直接向最终用户公开 `/.auth` 文件夹下的任何路由。

## <a name="login"></a>登录

使用下表查找特定于提供程序的路由。

| 授权提供程序 | 登录路由             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| GitHub                 | `/.auth/login/github`   |
| Twitter                | `/.auth/login/twitter`  |

例如，若要使用 GitHub 登录，可以包含类似于以下代码片段的链接：

```html
<a href="/.auth/login/github">Login</a>
```

如果选择支持多个提供程序，则需要向网站上的每个提供程序提供特定于提供程序的链接。

可以使用[路由规则](./configuration.md#routes)将默认提供程序映射到类似于 /login 的友好路由。

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>登录后重定向

如果希望用户登录后返回到特定页面，请在 `post_login_redirect_uri` 查询字符串参数中提供完全限定的 URL。

例如：

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Logout

`/.auth/logout` 路由将用户从网站注销。 可以添加指向站点导航的链接，以允许用户注销，如以下示例所示。

```html
<a href="/.auth/logout">Log out</a>
```

可以使用[路由规则](./configuration.md#routes)映射类似于 /logout 的友好路由。

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>注销后重定向

如果希望用户注销后返回到特定页面，请在 `post_logout_redirect_uri` 查询字符串参数中提供 URL。

## <a name="block-an-authorization-provider"></a>阻止授权提供程序

你可能想要限制应用使用授权提供程序。 例如，你的应用可能想要仅对[公开电子邮件地址的提供程序](#provider-user-details)进行标准化。

若要阻止提供程序，可以创建[路由规则](configuration.md#routes)，将请求的 404 返回到阻止的特定于提供程序的路由。 例如，若要将 Twitter 限制为提供程序，请添加以下路由规则。

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>限制

有关一般限制和局限性，请参阅[有关配额的文章](quotas.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [访问用户身份验证和授权数据](user-information.md)

<sup>1</sup> 挂起的外部认证。
