---
title: 比较外部标识 - Azure Active Directory | Microsoft Docs
description: Azure AD 外部标识允许组织外部人员使用自己的标识访问应用和资源。 比较外部标识的解决方案，包括 Azure Active Directory B2B 协作和 Azure AD B2C。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/13/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56fd9449e3954f7fe7cf3ab43a62df05869e2112
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729589"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory 中的外部标识是什么？

借助 Azure AD 中的外部标识，可以允许组织外部人员访问应用和资源，而让他们使用所需的任何标识进行登录。 合作伙伴、分销商、供应商和其他来宾用户可以“自带标识”。 无论他们是具有公司或政府颁发的数字标识，还是具有 Google 或 Facebook 等非托管社交标识，他们都可以使用自己的凭据登录。 外部用户的标识提供程序管理他们的标识，而你使用 Azure AD 管理对应用的访问，以便保护资源。

## <a name="external-identities-scenarios"></a>外部标识方案

Azure AD 外部标识在用户与组织的关系上关注较少，而更多地关注用户想要如何登录到应用和资源。 在此框架中，Azure AD 支持各种场景，从企业对企业 (B2B) 协作到面向消费者/客户或公民的应用程序（企业对客户或 B2C）的访问管理。

- 与外部用户共享应用和资源（B2B 协作）。 邀请外部用户加入你自己的租户，作为可向其分配权限（用于授权）的“来宾”用户，同时允许他们使用现有凭据（用于身份验证）。 用户使用其工作帐户、学校帐户或其他电子邮件帐户通过简单的邀请和兑换过程登录到共享资源。 还可以使用 [Azure AD 权利管理](../governance/entitlement-management-overview.md)配置[管理外部用户访问权限](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)的策略。 现在，随着[自助注册用户流](self-service-sign-up-overview.md)的推出，你可以允许外部用户自行注册应用程序。 可以自定义体验，以允许使用工作、学校或社交标识（如 Google 或 Facebook）进行注册。 你还可以在注册过程中收集有关用户的信息。 有关详细信息，请参阅 [Azure AD B2B 文档](index.yml)。

- 使用面向消费者和客户的应用 (Azure AD B2C) 的白标标识管理解决方案来构建用户旅程。 如果你是一个创建面向客户的应用的企业或开发人员，则可以使用 Azure AD B2C 扩展到数百万消费者、客户或公民。 开发人员可以将 Azure AD 用作其应用程序的全功能客户标识和访问管理 (CIAM) 系统。 客户可以使用已建立的标识（如 Facebook 或 Gmail）登录。 借助 Azure AD B2C，可以在使用你的应用程序时，完全自定义和控制客户的注册和登录方式以及管理其个人资料。 有关详细信息，请参阅 [Azure AD B2C 文档](../../active-directory-b2c/index.yml)。

## <a name="compare-external-identities-solutions"></a>比较外部标识解决方案

下表详细比较了可使用 Azure AD 外部标识实现的场景。

|   | 外部用户协作 (B2B) | 访问面向消费者/客户的应用 (B2C)  |
| ---- | --- | --- |
| 主要场景 | 使用 Microsoft 应用程序（Microsoft 365、Teams 等）或你自己的应用程序（SaaS 应用、自定义开发的应用等）进行协作。  | 新式 SaaS 或自定义开发的应用程序（非第一方 Microsoft 应用）的标识和访问管理。   |
| **适用对象**    | 与外部组织（如供应商和合作伙伴）的业务合作伙伴协作。 用户在目录中显示为来宾用户。 这些用户可能管理过 IT，也可能没有管理过 IT。  | 产品的客户。 这些用户在单独的 Azure AD 目录中进行管理。  |
| 支持的标识提供程序 | 外部用户可以使用工作帐户、学校帐户、任何电子邮件地址、SAML 和基于 WS 联合身份验证的标识提供程序、Gmail 和 Facebook 进行协作。  | 具有本地应用程序帐户（任何电子邮件地址或用户名）的消费者用户、受支持的各种社会标识，以及通过基于 SAML/WS 联合身份验证的标识提供者身份验证具有公司和政府颁发的标识的用户。       |
| 外部用户管理   | 外部用户在员工所在的目录中进行管理，但通常批注为来宾用户。 可采用与员工相同的方式管理来宾用户，还可将其添加到相同组等。    | 外部用户在 Azure AD B2C 目录中管理。 他们与组织的员工和合作伙伴目录（若有）分开管理。  |
| **单一登录 (SSO)**      | 支持 SSO 到所有 Azure AD 连接的应用。 例如，可允许访问 Microsoft 365 或本地应用以及其他 SaaS 应用（例如 Salesforce 或 Workday）。    | 支持在 Azure AD B2C 租户中单一登录到客户自有应用。 不支持单一登录到 Microsoft 365 或其他 Microsoft SaaS 应用。    |
| 安全策略和合规性        | 由主办/邀请组织管理（例如，通过[条件访问策略](conditional-access.md)）。 | 由组织通过条件访问和标识保护进行管理。        |
| **品牌打造**  | 使用主办/邀请组织的品牌。    | 每个应用程序或组织可完全自定义的品牌。   |
| **计费模式** | 基于月度活跃用户 (MAU) 的[外部标识定价](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)。 <br>（另请参阅：[B2B 设置详细信息](external-identities-pricing.md)） | 基于月度活跃用户 (MAU) 的[外部标识定价](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)。 <br>（另请参阅：[B2C 设置详细信息](../../active-directory-b2c/billing.md)） |
| **详细信息** | [博客文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文档](what-is-b2b.md)                   | [产品页](https://azure.microsoft.com/services/active-directory-b2c/)、[文档](../../active-directory-b2c/index.yml)       |

使用 Azure AD 外部标识保护和管理超出组织边界的客户和合作伙伴。

## <a name="about-multitenant-applications"></a>关于多租户应用程序

如果你将应用作为服务提供，而不想管理客户的用户帐户，那么多租户应用可能是你的最佳选择。 当你开发面向其他 Azure AD 租户的应用程序时，可以定向到单个组织中的用户（单租户），或已具有 Azure AD 租户的任何组织的用户（多租户应用程序）。 默认情况下，Azure AD 中的应用注册是单租户，但你可以将注册设置为多租户。 这个多租户应用程序由你在自己的 Azure AD 中注册一次。 但之后，任何组织的任何 Azure AD 用户都可以使用该应用程序，而无需你执行其他操作。 有关详细信息，请参阅[管理多租户应用程序中的标识](/azure/architecture/multitenant-identity/)和[操作指南](../develop/howto-convert-app-to-be-multi-tenant.md)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [关于 Azure AD B2C](../../active-directory-b2c/overview.md)