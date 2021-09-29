---
title: 在 Azure Active Directory 中通过凭据管理来构建复原能力
description: 面向架构师和 IT 管理员的有关如何构建具有复原能力的凭据策略的指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd03ce16f0695c23f30146cb8099eb67614982dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754420"
---
# <a name="build-resilience-with-credential-management"></a>通过凭据管理来构建复原能力

在令牌请求中向 Azure AD 提供凭据时，有多个依赖项必须可供验证。 第一种身份验证因素依赖于 Azure AD 身份验证，在某些情况下还依赖于本地基础结构。 有关混合身份验证体系结构的详细信息，请参阅[在混合基础结构中构建复原能力](resilience-in-hybrid.md)。 

如果实现第二种因素，则会将第二种因素的依赖项添加到第一种因素的依赖项。 例如，如果第一种因素是通过 PTA，第二种因素是短信，则依赖项是：

* Azure AD 身份验证服务

* Azure MFA 服务

* 本地基础结构

* 电话运营商

* 用户的设备（未绘出）

 
![身份验证方法和依赖项的插图](./media/resilience-in-credentials/admin-resilience-credentials.png)

凭据策略应考虑每种身份验证类型的依赖项，并预配避免单点故障的方法。 

由于身份验证方法具有不同的依赖项，因此最好允许用户注册尽可能多的第二因素选项。 如果可能，请确保包含带有不同依赖项的第二种因素。 例如，作为第二种因素的语音呼叫和短信共享相同的依赖项，因此将它们作为仅有的选项不会降低风险。

最具复原能力的凭据策略是使用无密码身份验证。 Windows Hello 企业版和 FIDO 2.0 安全密钥的依赖项少于采用两种不同因素的强身份验证的依赖项。 Microsoft Authenticator 应用、Windows Hello 企业版和 Fido 2.0 安全密钥是最安全的。 

对于第二种因素，使用基于时间的一次性密码 (TOTP) 或 OATH 硬件令牌的 Microsoft Authenticator 应用或其他验证器应用具有最少的依赖项，并且因此更具复原能力。

## <a name="how-do-multiple-credentials-help-resilience"></a>多个凭据如何帮助复原？

预配多个凭据类型可为用户提供适合其首选项和环境约束的选择。 因此，对于在请求时不可用的特定依赖项，提示用户进行多重身份验证的交互式身份验证将更具复原能力。 可以[优化多重身份验证的重新身份验证提示](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

除了上面所述的单个用户复原能力外，企业还应针对引入错误配置的操作错误、自然灾害或本地联合身份验证服务（尤其是在用于多重身份验证时）的企业级资源中断等大规模中断做出应变计划。 

## <a name="how-do-i-implement-resilient-credentials"></a>如何实现实现具有复原能力的凭据？

* 部署[无密码凭据](../authentication/howto-authentication-passwordless-deployment.md)（如 Windows Hello 企业版、电话身份验证和 FIDO2 安全密钥）以减少依赖项。

* 将 [Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)部署为第二个因素。

* 为从 Windows Server Active Directory 同步的混合帐户启用[密码哈希同步](../hybrid/whatis-phs.md)。 此选项可以随联合身份验证服务（如 AD FS）一起启用，并将在联合身份验证服务发生故障时提供回退。

* [分析多重身份验证方法的使用情况](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)以改善用户体验。

* [实现可复原的访问控制策略](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [使用设备状态构建复原能力](resilience-with-device-states.md)

* [使用连续访问评估 (CAE) 构建复原能力](resilience-with-continuous-access-evaluation.md)

* [构建外部用户身份验证的复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [利用应用程序代理构建应用程序访问的复原能力](resilience-on-premises-access.md)

适用于开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)