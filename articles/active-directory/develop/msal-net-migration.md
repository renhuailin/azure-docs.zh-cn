---
title: 迁移到 MSAL.NET 和 Microsoft.Identity.Web
titleSuffix: Microsoft identity platform
description: 了解从适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 或 Microsoft.Identity.Web 的原因和方法
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5a3dd6265c9fbefb85cf72b80473538983c682b7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214696"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>将应用程序迁移到 MSAL.NET 或 Microsoft.Identity.Web

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>迁移到 MSAL.NET 或 Microsoft.Identity.Web 的原因

适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 与适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 用于对 Azure AD 实体进行身份验证，以及从 Azure AD 请求令牌。 到目前为止，大多数开发人员都使用 Azure AD 身份验证库 (ADAL) 从适用于开发人员的 Azure AD 平台 (v1.0) 请求令牌。 这些令牌用于对 Azure AD 标识（工作和学校帐户）进行身份验证。 

MSAL 具有优于 ADAL 的优势。 下面列出了其中一些优势：

- 你可以使用 Azure AD B2C 对更广泛的 Microsoft 标识（工作或学校帐户、个人 Microsoft 帐户以及社交或本地帐户）进行身份验证，
- 用户将获得最佳单一登录体验，
- 应用程序可以启用增量许可、条件访问，
- 你可以从安全性和复原方面的持续创新中受益，
- 应用程序可以在复原和安全性方面实现最佳实践。

现在建议将 MSAL.NET 或 Microsoft.Identity.Web 作为身份验证库，与 Microsoft 标识平台配合。 不会使用 ADAL.NET 实现任何新功能。 工作的重点是改进 MSAL.NET。 有关详细信息，请参阅公告：[更新应用程序以使用 Microsoft 身份验证库和 Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>是该迁移到 MSAL.NET 还是 Microsoft.Identity.Web

在深入了解 MSAL.NET 与 ADAL.NET 的详细信息之前，可能需要检查是要使用 MSAL.NET 还是更高级别的抽象（如 [Microsoft.Identity.Web](microsoft-identity-web.md)）

有关以下决策树的详细信息，请参阅 [MSAL.NET 或 Microsoft.Identity.Web？](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)

![“此框图说明了从 ADAL.NET 迁移时如何判断是否需要使用 MSAL.NET 和 Microsoft.Identity.Web 或两者兼用”](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>后续步骤

- 了解[公共客户端和机密客户端应用程序](msal-client-applications.md)。
- 了解如何[将基于 ASP.NET MVC 或 .NET Classic 构建的机密客户端应用程序从 ADAL.NET 迁移到 MSAL.NET](msal-net-migration-confidential-client.md)。
- 了解如何[将基于 .NET 或 .NET Classic 构建的公共客户端应用程序从 ADAL.NET 迁移到 MSAL.NET](msal-net-migration-public-client.md)。
- 详细了解 [ADAL.NET 与 MSAL.NET 应用之间的差异](msal-net-differences-adal-net.md)。
- 了解如何将基于 ASP.NET Core 构建的机密客户端应用程序从 ADAL.NET 迁移到 Microsoft.Identity.Web：
  -  [Web 应用](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [Web API](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)
