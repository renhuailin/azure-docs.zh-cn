---
title: 迁移到 Microsoft 身份验证库 (MSAL)
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 (MSAL) 与 Azure AD 身份验证库 (ADAL) 之间的差异，以及如何迁移到 MSAL。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 145302d3569c2fc9c5d5bd58a5f9b7ccefac0b8e
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418521"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>将应用程序迁移到 Microsoft 身份验证库 (MSAL)

如果有任何应用程序使用 Azure Active Directory 身份验证库 (ADAL) 进行身份验证和授权功能，则现在需要将它们迁移到 [Microsoft 身份验证库 (MSAL)](msal-overview.md#languages-and-frameworks)。

- Microsoft 对 ADAL 的所有支持和开发（包括安全修补程序）将于 2022 年 6 月 30 日结束。
- 自 2020 年 6 月 30 以来，未向 ADAL 添加任何新功能。

> [!WARNING]
> 如果不在 2022 年 6 月 30 日 ADAL 支持结束前选择迁移到 MSAL，应用的安全性会面临风险。 使用 ADAL 的现有应用在支持结束日期后将继续工作，但 Microsoft 将不再发布 ADAL 的安全修补程序。

## <a name="why-switch-to-msal"></a>为什么要切换到 MSAL？

与 ADAL 相比，MSAL 具有多种优势，包括以下功能： 

|功能|MSAL|ADAL|
|---------|---------|---------|
|**安全性**|||
|2022 年 6 月 30 日之后的安全修补程序|![2022 年 6 月 30 日之后的安全修补程序 - MSAL 提供该功能][y]|![2022 年 6 月 30 日之后的安全修补程序 - ADAL 不提供该功能][n]|
| 根据 Microsoft Graph 和其他支持[连续访问评估 (CAE)](app-resilience-continuous-access-evaluation.md) 的 API 的策略或关键事件主动刷新和撤销令牌。|![根据 Microsoft Graph 和其他支持连续访问评估 (CAE) 的 API 的策略或关键事件主动刷新和撤销令牌 - MSAL 提供该功能][y]|![根据 Microsoft Graph 和其他支持连续访问评估 (CAE) 的 API 的策略或关键事件主动刷新和撤销令牌 - ADAL 不提供该功能][n]|
| 符合 OAuth v2.0 和 OpenID Connect (OIDC) 的标准 |![符合 OAuth v2.0 和 OpenID Connect (OIDC) 的标准 - MSAL 提供该功能][y]|![符合 OAuth v2.0 和 OpenID Connect (OIDC) 的标准 - ADAL 不提供该功能][n]|
|**用户帐户和体验**|||
|Azure Active Directory (Azure AD) 帐户|![Azure Active Directory (Azure AD) 帐户 - MSAL 提供该功能][y]|![Azure Active Directory (Azure AD) 帐户 - ADAL 提供该功能][y]|
| Microsoft 帐户 (MSA) |![Microsoft 帐户 (MSA) - MSAL 提供该功能][y]|![Microsoft 帐户 (MSA) - ADAL 不提供该功能][n]|
| Azure AD B2C 帐户 |![Azure AD B2C 帐户 - MSAL 提供该功能][y]|![Azure AD B2C 帐户 - ADAL 不提供该功能][n]|
| 最佳单一登录体验 |![最佳单一登录体验 - MSAL 提供该功能][y]|![最佳单一登录体验 - ADAL 不提供该功能][n]|
|**复原能力**|||
| 主动令牌续订 |![主动令牌续订 - MSAL 提供该功能][y]|![主动令牌续订 - ADAL 不提供该功能][n]|
| 限制 |![限制 - MSAL 提供该功能][y]|![限制 - ADAL 不提供该功能][n]|

## <a name="ad-fs-support-in-msalnet"></a>MSAL.NET 中的 AD FS 支持

可使用 MSAL.NET、MSAL Java 和 MSAL Python 从 Active Directory 联合身份验证服务 (AD FS) 2019 或更高版本获取令牌。 MSAL 不支持早期版本的 AD FS，包括 AD FS 2016。

如果需要继续使用 AD FS，应升级到 AD FS 2019 或更高版本，然后再将应用程序从 ADAL 更新为 MSAL。

## <a name="how-to-migrate-to-msal"></a>如何迁移到 MSAL

在开始迁移之前，需要确定哪些应用正在使用 ADAL 进行身份验证。 按照以下文章中的步骤使用 Azure 门户获取列表：
- [如何：在租户中通过 ADAL 获取应用的完整列表](howto-get-list-of-all-active-directory-auth-library-apps.md)

确定使用 ADAL 的应用后，根据应用程序类型将它们迁移到 MSAL，如下所示。

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>迁移帮助

如果对将应用从 ADAL 迁移到 MSAL 有疑问，以下是一些选项：

- 将问题发布在 [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) 上并用 `[azure-ad-adal-deprecation]` 标记。
- 在库的 GitHub 存储库中创建问题。 请参阅 MSAL 概述文章的[语言和框架](msal-overview.md#languages-and-frameworks)部分，获取指向每个库的存储库的链接。

如果你与独立软件供应商 (ISV) 合作开发应用程序，则建议你直接联系他们，了解他们向 MSAL 的迁移过程。

## <a name="next-steps"></a>后续步骤

若要详细了解 MSAL（包括使用情况信息以及可用于不同编程语言和应用程序类型的库），请参阅：

- [使用 MSAL 获取和缓存令牌](msal-acquire-cache-tokens.md)
- [应用程序配置选项](msal-client-application-configuration.md)
- [MSAL 身份验证库](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png