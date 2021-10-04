---
title: Azure AD 身份验证和国家云 | Azure
titleSuffix: Microsoft identity platform
description: 了解国家云的应用注册和身份验证终结点。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: d920747b3af826a3009c602e81baa9157160eeb8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551517"
---
# <a name="national-clouds"></a>国家云

国家云是物理上独立的 Azure 实例。 Azure 的这些区域旨在确保数据驻留、主权和合规性要求在地理边界内得到遵从。

包括全球 Azure 云在内，Azure Active Directory (Azure AD) 在以下国家/地区云中部署：

- Azure Government
- Azure 中国世纪互联
- Azure 德国（[于 2021 年 10 月 29 日关闭](https://www.microsoft.com/cloud-platform/germany-cloud-regions)）。 详细了解 [Azure 德国迁移](#azure-germany-microsoft-cloud-deutschland)。

每个云实例（单个国家/地区云和全球 Azure 云）都是一个独立的环境，都有自己的终结点。 特定于云的终结点包括 OAuth 2.0 访问令牌和 OpenID Connect ID 令牌请求终结点，以及用于应用管理和部署的 URL，例如 Azure 门户。

开发应用时，请使用要在其中部署应用程序的云实例的终结点。

## <a name="app-registration-endpoints"></a>应用注册终结点

每个国家云都有一个单独的 Azure 门户。 若要在国家云中将应用程序与 Microsoft 标识平台集成，需要在每个特定于环境的 Azure 门户中单独注册应用程序。

下表列出了用于为每个国家云注册应用程序的 Azure AD 终结点的基 URL。

| 国家云                          | Azure 门户终结点      |
| --------------------------------------- | -------------------------- |
| 适用于美国政府的 Azure 门户          | `https://portal.azure.us`  |
| 由世纪互联运营的 Azure 门户中国版 | `https://portal.azure.cn`  |
| Azure 门户（全局服务）           | `https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 身份验证终结点

所有各国云在每个环境中分别对用户进行身份验证，并具有单独的身份验证终结点。

下表列出了用于获取每个国家云的令牌的 Azure AD 终结点的基 URL。

| 国家云                      | Azure AD 身份验证终结点           |
| ----------------------------------- | ------------------------------------------ |
| 适用于美国政府的 Azure AD          | `https://login.microsoftonline.us`         |
| 由世纪互联运营的 Azure AD 中国 | `https://login.partner.microsoftonline.cn` |
| Azure AD（全局服务）           | `https://login.microsoftonline.com`        |

可以使用适当的特定于区域的基 URL 来形成对 Azure AD 授权或令牌终结点的请求。 例如，对于全球 Azure：

- 授权常用终结点为 `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`。
- 令牌常用终结点为 `https://login.microsoftonline.com/common/oauth2/v2.0/token`。

对于单租户应用程序，请将先前 URL 中的“common”替换为你的租户 ID 或名称。 示例为 `https://login.microsoftonline.com/contoso.com`。

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure 德国（Microsoft 云德国版）

> [!WARNING]
> Azure 德国（Microsoft 云德国版）将[于 2021 年 10 月 29 日关闭](https://www.microsoft.com/cloud-platform/germany-cloud-regions)。 你选择不在该日期之前将其迁移到全球 Azure 中的某个区域的服务和应用程序将变得不可访问。

如果尚未从 Azure 德国迁移应用程序，请按[有关如何从 Azure 德国进行迁移的 Azure Active Directory 信息](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)中的说明开始迁移。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

若要了解如何在国家云环境中调用 Microsoft Graph API，请转到[国家云部署中的 Microsoft Graph](/graph/deployments)。

> [!IMPORTANT]
> 全球服务的特定区域中的某些服务和功能可能并非在所有国家云中都可用。 若要了解哪些服务可用，请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)。

若要了解如何使用 Microsoft 标识平台来生成应用程序，请按[使用授权代码流的单页应用程序 (SPA) 教程](tutorial-v2-angular-auth-code.md)操作。 具体而言，此应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌。

## <a name="next-steps"></a>后续步骤

了解如何[在国家云环境中使用 Microsoft 身份验证库 (MSAL)](msal-national-cloud.md)。

国家云文档：

- [Azure Government](../../azure-government/index.yml)
- [Azure 中国世纪互联](/azure/china/)
- [Azure 德国（于 2021 年 10 月 29 日关闭）](../../germany/index.yml)
