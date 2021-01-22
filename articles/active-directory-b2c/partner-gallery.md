---
title: Azure AD B2C 的 ISV 合作伙伴库
titleSuffix: Azure AD B2C
description: 了解如何与 ISV 合作伙伴集成，以根据你的需求定制你的最终用户体验。 我们的合作伙伴网络扩展了我们的解决方案功能;启用 MFA、安全的客户身份验证、基于角色的访问控制;通过身份验证验证来对付欺诈行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9aca01bae7911d2b6234d48bedc14b116e911a79
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675365"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 合作伙伴

我们的 ISV 合作伙伴网络扩展了我们的解决方案功能，可帮助你构建无缝的最终用户体验。 使用 Azure AD B2C，你可以与 ISV 合作伙伴集成，以启用多重身份验证 (MFA) 方法，执行基于角色的访问控制，启用身份验证和验证，使用机器人检测和欺诈保护提高安全性，并满足支付服务指令 2 (PSD2) 安全客户身份验证 (SCA) 要求。 使用我们的详细示例演练来了解如何将应用与 ISV 合作伙伴集成。

>[!NOTE]
>[GitHub 上的 Azure Active Directory B2C 社区网站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)还提供了社区提供的示例自定义策略。

## <a name="identity-verification-and-proofing"></a>身份验证和验证

通过 Azure AD B2C 合作伙伴，客户可以在允许帐户注册或访问之前，对最终用户进行身份验证和验证。 身份验证和校对可以检查文档、基于知识的信息和活动。

高级体系结构关系图说明了流。

![关系图显示标识校对流](./media/partner-gallery/third-party-identity-proofing.png)

具有以下用于身份验证和验证的 Isv 的 Microsoft 合作伙伴。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
|![Experian 徽标的屏幕截图。](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 是一种身份验证和校对提供程序，它根据用户属性执行风险评估，以防止欺诈。 |
|![IDology 徽标的屏幕截图。](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 是具有 ID 验证解决方案、欺诈防御解决方案、合规性解决方案和其他信息的身份验证和校对提供程序。|
|![Jumio 徽标的屏幕截图。](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 是一种 ID 验证服务，用于启用实时自动 ID 验证，从而保护客户数据。 |
|![无键徽标的屏幕截图。](./media/partner-gallery/keyless-logo.png) | [无键](./partner-keyless.md) 是一种 ID 验证服务，以面部生物识别扫描的形式提供身份验证，并消除欺诈、网络钓鱼和凭据重用。
| ![LexisNexis 徽标的屏幕截图。](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 是一种分析和身份验证提供程序，用于验证用户标识，并根据用户设备提供全面的风险评估。 |
| ![Onfido 徽标的屏幕截图](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 是一个文档 ID 和面部生物识别验证解决方案，可让公司实时 *了解你的客户* 和标识要求。  |

## <a name="mfa-and-passwordless-authentication"></a>MFA 和无密码 authentication

Microsoft 合作伙伴，其中包含以下用于 MFA 和无密码 authentication 的 Isv。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![Hypr 徽标的屏幕截图](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) 是无密码 authentication 提供程序，可将密码替换为公钥加密，消除欺诈、仿冒和凭据重用。 |
| ![Itsme 徽标的屏幕截图](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 是一种电子识别、身份验证和信任服务 (eiDAS) 相容的数字标识解决方案，允许用户安全登录，无需使用智能卡读卡器、密码、双因素身份验证和多个 PIN 代码。 |
| ![尼维斯标志的屏幕截图](./media/partner-gallery/nevis-logo.png) | [尼维斯](./partner-nevis.md) 支持无密码身份验证，并为用户提供了一种具有客户身份验证的最优秀最终用户体验。 |
| ![Trusona 徽标的屏幕截图](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 集成可帮助你安全登录并启用无密码 AUTHENTICATION、MFA 和数字许可扫描。 |
| ![Twilio 徽标的屏幕截图。](./media/partner-gallery/twilio-logo.png) | [Twilio 验证应用程序](./partner-twilio.md) 提供多个解决方案，可通过 SMS 一次性密码 (OTP) 、基于时间的一次性密码 (TOTP) 和推送通知，以及符合 PSD2 的 SCA 要求。 |
| ![TypingDNA 徽标的屏幕截图](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) 通过分析用户的键入模式，实现了强大的客户身份验证。 它可帮助公司实现无提示 MFA 并符合 PSD2 的 SCA 要求。 |
| ![Whoiam 徽标的屏幕截图](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 是一种署名的标识管理系统 (BRIMS) 应用程序，使组织能够通过语音、短信和电子邮件来验证其用户群。 |

## <a name="role-based-access-control"></a>基于角色的访问控制 
 
使用以下 Isv 进行基于角色的访问控制的 Microsoft 合作伙伴。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![N8identity 徽标的屏幕截图](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) 是一种身份即服务治理平台，它提供解决方案来解决客户帐户迁移和客户服务请求 (Microsoft Azure 上运行的 CSR) 管理。 |
| ![Saviynt 徽标的屏幕截图](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) 的云本机平台通过智能分析和跨应用程序集成提高了安全性、合规性和监管能力，简化 IT 现代化。 |

## <a name="security"></a>安全性

具有以下安全 Isv 的 Microsoft 合作伙伴。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![Arkose 实验室徽标的屏幕截图](./media/partner-gallery/arkose-logo.png) | [Arkose 实验室](./partner-arkose-labs.md) 是一种欺诈防御解决方案提供商，可帮助组织防范机器人攻击、帐户接管攻击和欺诈性帐户。 |
| ![阶层徽标的屏幕截图](./media/partner-gallery/strata-logo.png) | [阶层](./partner-strata.md) 提供对本地应用程序的安全混合访问，方法是强制使用一致的访问策略，使身份保持同步，并简化将应用程序从旧的标识系统转换到 Azure AD B2C 提供的基于标准的身份验证和访问控制。 |
| ![Zscaler 徽标的屏幕截图](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) 提供对专用应用程序和资产的基于策略、安全的访问，而不会带来 VPN 的成本、麻烦或安全风险。 |

## <a name="additional-information"></a>其他信息

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>后续步骤

在所述的表中选择一个合作伙伴，了解如何将其解决方案与 Azure AD B2C 集成。