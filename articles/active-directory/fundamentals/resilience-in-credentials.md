---
title: 在 Azure Active Directory 中通过凭据管理构建复原能力
description: 介绍构建弹性凭据策略的架构师和 IT 管理员。
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
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919352"
---
# <a name="build-resilience-with-credential-management"></a>通过凭据管理构建复原能力

在令牌请求中向 Azure AD 提供凭据时，有多个必须可用于验证的依赖项。 第一种身份验证因素依赖于 Azure AD 身份验证，在某些情况下还依赖于本地基础结构。 有关混合身份验证体系结构的详细信息，请参阅 [在混合基础结构中构建复原能力](resilience-in-hybrid.md)。 

如果实现第二个因素，则会将第二个系数的依赖项添加到第一个的依赖项。 例如，如果第一个因素是通过 PTA，第二个因素是 SMS，则依赖项是：

* Azure AD 身份验证服务

* Azure MFA 服务

* 本地基础结构

* 电话运营商

* 用户的设备 (未) 

 
![身份验证方法和依赖项的图像](./media/resilience-in-credentials/admin-resilience-credentials.png)

凭据策略应考虑每种身份验证类型的依赖关系，并设置避免单点故障的方法。 

由于身份验证方法具有不同的依赖项，因此最好让用户注册尽可能多的第二因素选项。 如果可能，请确保包含具有不同依赖项的第二个因素。 例如，作为第二个因素的语音呼叫和短信共享相同的依赖项，因此将它们视为唯一的选项不会降低风险。

最灵活的凭据策略是使用无密码 authentication。 Windows Hello 企业版和 FIDO 2.0 安全密钥的依赖项比具有两个不同因素的强身份验证的依赖项少。 Microsoft Authenticator 应用、Windows Hello 企业版和 Fido 2.0 安全密钥是最安全的。 

对于第二个因素，使用基于时间的一次性密码的 Microsoft Authenticator 应用程序或其他验证器应用 (TOTP) 或 OATH 硬件令牌的依赖项最少，因此更具弹性。

## <a name="how-do-multiple-credentials-help-resilience"></a>多个凭据如何帮助复原能力？

预配多个凭据类型可为用户提供适合其首选项和环境约束的选项。 因此，系统会提示用户进行多重身份验证的交互式身份验证，在请求时无法获得特定依赖项。 你可以 [优化多重身份验证的身份验证提示](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)。

除了上面所述的单个用户复原能力外，企业还应针对大规模中断（例如，在本地联合身份验证服务中引入配置错误、自然灾害或企业范围的资源中断）来规划紧急情况， (尤其是在用于多重身份验证的情况下) 。 

## <a name="how-do-i-implement-resilient-credentials"></a>如何实现实现弹性凭据？

* 部署 [无密码凭据](../authentication/howto-authentication-passwordless-deployment.md) ，如 Windows Hello 企业版、手机身份验证和 FIDO2 安全密钥，以减少依赖项。

* 将 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md) 部署为第二个因素。

* 为从 Windows Server Active Directory 同步的混合帐户启用 [密码哈希同步](../hybrid/whatis-phs.md) 。 可以在联合身份验证服务（如 AD FS）启用此选项，并在联合身份验证服务发生故障时提供回退。

* [分析多重身份验证方法的使用情况](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) ，以改善用户的体验。

* [实现弹性访问控制策略](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [用设备状态生成复原能力](resilience-with-device-states.md)

* [使用持续存取评估 (CAE) 生成复原能力 ](resilience-with-continuous-access-evaluation.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)

面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
