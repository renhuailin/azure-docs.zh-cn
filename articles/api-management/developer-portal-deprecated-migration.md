---
title: 从旧开发人员门户迁移到新开发人员门户
titleSuffix: Azure API Management
description: 了解如何从旧开发人员门户迁移到 API 管理中的新开发人员门户。
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.openlocfilehash: f09f83fdac6de53f59801e33dd9e668b1009136c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642560"
---
# <a name="migrate-to-the-new-developer-portal"></a>迁移到新开发人员门户

本文介绍从已弃用的旧门户迁移到 API 管理中的新开发人员门户所需的步骤。

> [!IMPORTANT]
> 旧开发人员门户现在已弃用，并且将仅接收安全更新。 你可以像往常一样继续使用它，直到它在 2023 年 10 月停用，届时将从所有 API 管理服务中将其删除。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>新开发人员门户中的改进

新的开发人员门户解决了已弃用门户的许多限制。 它具有[用于编辑内容的可视化拖放编辑器](api-management-howto-developer-portal-customize.md)和设计人员用于设置网站样式的专用面板。 页面、自定义和配置将保存为 API 管理服务中的 Azure 资源管理器资源，这样你便能够[自动执行门户部署](automate-portal-deployments.md)。 最后，门户的代码库是开源的，因此[你可以使用自定义功能对其进行扩展](api-management-howto-developer-portal.md#managed-vs-self-hosted)。

## <a name="how-to-migrate-to-new-developer-portal"></a>如何迁移到新的开发人员门户

新的开发人员门户与已弃用门户不兼容，因此无法自动迁移。 需要手动重新创建内容（页面、文本、媒体文件）并自定义新门户的外观。 具体步骤将因门户的自定义内容和复杂性而异。 有关指南，请参阅[开发人员门户教程](api-management-howto-developer-portal-customize.md)。 其余的配置（如 API、产品、用户、标识提供程序）将在两个门户之间自动共享。

> [!IMPORTANT]
> 如果之前已启动新的开发人员门户，但尚未进行任何更改，请重置默认内容，将其更新到最新版本。

从已弃用的门户迁移时，请记住以下更改：

- 如果通过自定义域公开开发人员门户，请向新的开发人员门户[分配域](configure-custom-domain.md)。 使用 Azure 门户中下拉列表中的“开发人员门户”选项。
- 在 API 上[应用 CORS 策略](developer-portal-faq.md#cors)，以启用交互式测试控制台。
- 如果注入自定义 CSS 来设置门户样式，需要[使用内置设计面板复制样式](api-management-howto-developer-portal-customize.md)。 新门户中不允许使用 CSS 注入。
- 只能在[新门户的自托管版本](api-management-howto-developer-portal.md#managed-vs-self-hosted)中注入自定义 JavaScript。
- 如果你的 API 管理位于虚拟网络中，并且通过应用程序网关向 Internet 公开，请[参阅本文档文章](api-management-howto-integrate-internal-vnet-appgateway.md)，了解具体的配置步骤。 你需要：

    - 启用与 API 管理的管理终结点的连接。
    - 启用与新门户终结点的连接。
    - 禁用所选的 Web 应用程序防火墙规则。

- 如果将默认电子邮件通知模板更改为包含显式定义的弃用门户 URL，请将它们更改为使用门户 URL 参数或指向新的门户 URL。 如果模板改用内置门户 URL 参数，则不需要进行任何更改。
- 新开发人员门户不支持“问题”和“应用程序” 。
- 新开发者门户不支持以标识提供者身份与 Facebook、Microsoft、Twitter 和 Google 直接集成。 可以通过 Azure AD B2C 与这些提供商集成。
- 如果使用委派，请更改应用程序中的返回 URL，并使用[“获取共享访问令牌”API 终结点](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token)而不是“生成 SSO URL”终结点 。
- 如果你使用 Azure AD 作为标识提供者：

    - 更改应用程序中的返回 URL，使其指向新的开发人员门户域。
    - 将应用程序中返回 URL 的后缀从 `/signin-aad` 修改为 `/signin`。

- 如果你使用 Azure AD B2C 作为标识提供者：

    - 更改应用程序中的返回 URL，使其指向新的开发人员门户域。
    - 将应用程序中返回 URL 的后缀从 `/signin-aad` 修改为 `/signin`。
    - 在应用程序声明中包含“名”、“姓”和“用户的对象 ID”  。

- 如果在交互式测试控制台中使用 OAuth 2.0，请更改应用程序中的返回 URL，使其指向新的开发人员门户域并修改后缀：

    - 对于授权代码授予流，从 `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` 更改为 `/signin-oauth/code/callback/[serverName]`。
    - 对于隐式授权流，从 `/docs/services/[serverName]/console/oauth2/implicit/callback` 更改为 `/signin-oauth/implicit/callback`。
- 如果在交互式测试控制台中使用 OpenID Connect，请更改应用程序中的返回 URL，使其指向新的开发人员门户域并修改后缀：

    - 对于授权代码授予流，从 `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` 更改为 `/signin-oauth/code/callback/[serverName]`。
    - 对于隐式授权流，从 `/docs/services/[serverName]/console/openidconnect/implicit/callback` 更改为 `/signin-oauth/implicit/callback`。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
- [访问和自定义开发人员门户](api-management-howto-developer-portal-customize.md)
