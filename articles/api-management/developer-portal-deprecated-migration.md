---
title: 从旧开发人员门户迁移到新的开发人员门户
titleSuffix: Azure API Management
description: 了解如何从旧开发人员门户迁移到 API 管理中的新开发人员门户。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325967"
---
# <a name="migrate-to-the-new-developer-portal"></a>迁移到新的开发人员门户

本文介绍从弃用的旧门户迁移到 API 管理中的新开发人员门户时需要执行的步骤。

> [!IMPORTANT]
> 旧开发人员门户现已弃用，并且仅接收安全更新。 在从所有 API 管理服务中删除该服务时，你可以继续按平时使用它，直到它在10月2023开始停用。

![API 管理开发人员门户](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>新开发人员门户中的改进

新的开发人员门户解决了不推荐使用的门户的许多限制。 它使用 [可视化拖放编辑器来编辑内容](api-management-howto-developer-portal-customize.md) ，使用专用面板来设计网站样式。 页面、自定义和配置作为 Azure 资源管理器资源保存在 API 管理服务中，使你能够 [自动执行门户部署](api-management-howto-developer-portal.md#automate)。 最后，门户的基本代码是开放源代码，因此 [你可以通过自定义功能对其进行扩展](api-management-howto-developer-portal.md#managed-vs-self-hosted)。

## <a name="how-to-migrate-to-new-developer-portal"></a>如何迁移到新的开发人员门户

新的开发人员门户与不推荐使用的门户不兼容，无法进行自动迁移。 需要手动重新创建内容（页面、文本、媒体文件）并自定义新门户的外观。 具体步骤会因门户的自定义和复杂性而异。 有关指南，请参阅[开发人员门户教程](api-management-howto-developer-portal-customize.md)。 其余配置（如 Api、产品、用户、标识提供者的列表）会自动在这两个门户之间共享。

> [!IMPORTANT]
> 如果之前已启动新的开发人员门户，但尚未进行任何更改，请 [重置默认内容](api-management-howto-developer-portal.md#preview-to-ga) 以将其更新到最新版本。

从弃用的门户中进行迁移时，请记住以下更改：

- 如果通过自定义域公开开发人员门户，请 [将域分配](configure-custom-domain.md) 给新的开发人员门户。 使用 "Azure 门户中的下拉列表中的" **开发人员门户** "选项。
- 对 Api[应用 CORS 策略](api-management-howto-developer-portal.md#cors)，以启用交互式测试控制台。
- 如果插入自定义 CSS 来设置门户样式，则需要 [使用内置设计面板复制样式](api-management-howto-developer-portal-customize.md)。 新门户不允许使用 CSS 注入。
- 只能在 [新门户的自承载版本](api-management-howto-developer-portal.md#managed-vs-self-hosted)中插入自定义 JavaScript。
- 如果你的 API 管理位于虚拟网络中，并通过应用程序网关向 Internet 公开，请 [参阅此文档一文](api-management-howto-integrate-internal-vnet-appgateway.md) ，了解具体的配置步骤。 你需要：

    - 启用到 API 管理的管理终结点的连接。
    - 启用与新门户终结点的连接。
    - 禁用所选 Web 应用程序防火墙规则。

- 如果更改了默认电子邮件通知模板以包括显式定义的不推荐使用的门户 URL，请将其更改为使用门户 URL 参数或指向新门户 URL。 如果模板使用内置门户 URL 参数，则不需要进行任何更改。
- 新的开发人员门户不支持*问题*和*应用程序*。
- 新的开发人员门户不支持将 Facebook、Microsoft、Twitter 和 Google 直接集成为标识提供者。 可以通过 Azure AD B2C 与这些提供程序集成。
- 如果使用委派，请在应用程序中更改返回 URL，并使用 [*获取共享访问令牌* API 终结点](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) ，而不是 *生成 SSO URL* 终结点。
- 如果使用 Azure AD 作为标识提供者：

    - 更改应用程序中的返回 URL，使其指向新的开发人员门户域。
    - 将应用程序中返回 URL 的后缀从更改 `/signin-aad` 为 `/signin` 。

- 如果使用 Azure AD B2C 作为标识提供者：

    - 更改应用程序中的返回 URL，使其指向新的开发人员门户域。
    - 将应用程序中返回 URL 的后缀从更改 `/signin-aad` 为 `/signin` 。
    - 在应用程序声明中包括 *给定的名称*、 *姓氏*和 *用户的对象 ID* 。

- 如果在交互式测试控制台中使用 OAuth 2.0，请更改应用程序中的返回 URL，使其指向新的开发人员门户域，并修改此后缀：

    - `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` 对于授权代码授予流，从到到。
    - 用于 `/docs/services/[serverName]/console/oauth2/implicit/callback` `/signin-oauth/implicit/callback` 隐式授予流的。
- 如果在交互式测试控制台中使用 OpenID Connect，请更改应用程序中的返回 URL，使其指向新的开发人员门户域，并修改此后缀：

    - `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` 对于授权代码授予流，从到到。
    - 用于 `/docs/services/[serverName]/console/openidconnect/implicit/callback` `/signin-oauth/implicit/callback` 隐式授予流的。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
- [访问和自定义开发人员门户](api-management-howto-developer-portal-customize.md)