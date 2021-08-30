---
title: 身份验证方法和功能 - Azure Active Directory
description: 了解 Azure Active Directory 中可用的不同身份验证方法和功能，以帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 5adc3bd8ef03b2613198518fc22284686c2bfee9
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730744"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中有哪些可用的身份验证和验证方法？

Microsoft 建议使用无密码身份验证方法（例如 Windows Hello、FIDO2 安全密钥和 Microsoft Authenticator 应用），因为它们提供最安全的登录体验。 尽管用户可使用其他常见方法（例如用户名和密码）登录，但应将密码替换为更安全的身份验证方法。

![Azure AD 中的身份验证方法强度和首选身份验证方法表](media/concept-authentication-methods/authentication-methods.png)

用户登录时，与仅使用密码相比，Azure AD 多重身份验证 (MFA) 增加了额外的安全性。 可以提示用户进行其他形式的身份验证，例如响应推送通知、输入软件或硬件令牌中的验证码，或者响应短信或电话。

若要简化用户加入体验并注册 MFA 和自助式密码重置 (SSPR)，我们建议[启用组合式安全信息注册](howto-registration-mfa-sspr-combined.md)。 为了提供复原能力，建议要求用户注册多种身份验证方法。 在登录或 SSPR 期间，当用户无法使用某种方法时，他们可以选择使用另一种方法进行身份验证。 有关详细信息，请参阅[在 Azure AD 中创建可复原的访问控制管理策略](concept-resilient-controls.md)。

我们制作了[此视频](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be)帮助你选择最佳的身份验证方法来保护组织的安全。

## <a name="authentication-method-strength-and-security"></a>身份验证方法强度和安全性

当你在组织中部署 Azure AD 多重身份验证等功能时，请查看可用的身份验证方法。 选择在安全性和可用性方面满足或超过你的要求的方法。 如果可能，请使用具有最高安全性级别的身份验证方法。

下表概述了可用身份验证方法的安全注意事项。 可用性表示用户能够使用身份验证方法，而不是 Azure AD 中的服务可用性：

| 身份验证方法          | 安全性 | 可用性 | 可用性 |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello 企业版     | 高     | 高      | 高         |
| Microsoft Authenticator 应用    | 高     | 高      | 高         |
| FIDO2 安全密钥             | 高     | 高      | 高         |
| OATH 硬件令牌（预览版） | 中   | 中    | 高         |
| OATH 软件令牌           | 中   | 中    | 高         |
| SMS                            | 中型   | 高      | 中等       |
| 语音                          | 中   | 中    | 中       |
| Password                       | 低      | 高      | 高         |

有关安全性的最新信息，请查看我们的博客文章：

- [It's time to hang up on phone transports for authentication](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)（是时候放弃电话传输进行身份验证了）
- [Authentication vulnerabilities and attack vectors](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)（身份验证漏洞和攻击途径）

> [!TIP]
> 为实现灵活性和可用性，建议使用 Microsoft Authenticator 应用。 此身份验证方法提供最佳用户体验和多种模式，如无密码、MFA 推送通知和 OATH 代码。

## <a name="how-each-authentication-method-works"></a>每种身份验证方法的工作原理

登录到应用程序或设备时，可将某些身份验证方法用作主要因素，如使用 FIDO2 安全密钥或密码。 其他身份验证方法仅在使用 Azure AD 多重身份验证或 SSPR 时用作次要因素。

下表概述了在登录事件期间，何时可以使用身份验证方法：

| 方法                         | 主要身份验证 | 辅助身份验证  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello 企业版     | 是                    | MFA                       |
| Microsoft Authenticator 应用    | 是                    | MFA 和 SSPR              |
| FIDO2 安全密钥             | 是                    | MFA                       |
| OATH 硬件令牌（预览版） | 否                     | MFA 和 SSPR              |
| OATH 软件令牌           | 否                     | MFA 和 SSPR              |
| SMS                            | 是                    | MFA 和 SSPR              |
| 语音呼叫                     | 否                     | MFA 和 SSPR              |
| 密码                       | 是                    |                           |

所有这些身份验证方法都可以在 Azure 门户中进行配置，但更多的是使用 [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview) 进行配置。

若要详细了解每种身份验证方法的工作原理，请参阅以下单独的概念文章：

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator 应用](concept-authentication-authenticator-app.md)
* [FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH 硬件令牌（预览版）](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH 软件令牌](concept-authentication-oath-tokens.md#oath-software-tokens)
* [短信登录](howto-authentication-sms-signin.md)和[验证](concept-authentication-phone-options.md#mobile-phone-verification)
* [语音呼叫验证](concept-authentication-phone-options.md)
* 密码

> [!NOTE]
> 在 Azure AD 中，密码通常是主要身份验证方法之一。 不能禁用密码身份验证方法。 如果使用密码作为主要身份验证因素，则使用 Azure AD 多重身份验证提高登录事件的安全性。

在某些情况下，可使用以下附加验证方法：

* [应用密码](howto-mfa-app-passwords.md) - 用于不支持新式身份验证的旧应用程序，对于按用户的 Azure AD 多重身份验证可以配置此验证方法。
* [安全性问题](concept-authentication-security-questions.md) - 仅用于 SSPR
* [电子邮件地址](concept-sspr-howitworks.md#authentication-methods) - 仅用于 SSPR

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure AD 多重身份验证][tutorial-azure-mfa]。

要详细了解 SSPR 概念，请参阅[ Azure AD 自助式密码重置的工作原理][concept-sspr]。

若要详细了解 MFA 的概念，请参阅 [Azure AD 多重身份验证的工作原理][concept-mfa]。

详细了解如何使用 [Microsoft Graph REST API 版本](/graph/api/resources/authenticationmethods-overview)配置身份验证方法。

若要审查正在使用的身份验证方法，请参阅[使用 PowerShell 进行 Azure AD 多重身份验证的身份验证方法分析](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
