---
title: Azure AD B2C 的 ISV 合作伙伴库
titleSuffix: Azure AD B2C
description: 了解如何与我们的 ISV 合作伙伴集成，以根据你的需求定制你的最终用户体验。 我们的合作伙伴网络扩展了我们的解决方案功能；启用 MFA、安全的客户身份验证、基于角色的访问控制；通过身份验证证明打击欺诈行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1ee632e3aaae7b858ab43b45f6e72aff8d1fb77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361752"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 合作伙伴

我们的 ISV 合作伙伴网络扩展了我们的解决方案功能，可帮助你构建无缝的最终用户体验。 使用 Azure AD B2C，你可以与 ISV 合作伙伴集成，以启用多重身份验证 (MFA) 方法，执行基于角色的访问控制，启用身份验证和证明，使用机器人检测和欺诈保护提高安全性，以及满足支付服务指令 2 (PSD2) 安全客户身份验证 (SCA) 要求。 通过详细示例演练来了解如何将应用与 ISV 合作伙伴集成。

>[!NOTE]
>[GitHub 上的 Azure Active Directory B2C 社区网站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)还提供了社区的示例自定义策略。

## <a name="identity-verification-and-proofing"></a>身份验证和证明

通过 Azure AD B2C 合作伙伴，客户可以在允许帐户注册或访问之前，对最终用户进行身份验证和证明。 身份验证和证明可以检查文档、知识库信息和运行情况。

高级体系结构图对流进行了说明。

![图中显示身份证明流](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft 与以下 ISV 合作进行身份验证和证明。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
|![Experian 徽标的屏幕截图。](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 是身份验证和证明提供商，其根据用户属性执行风险评估，以防止欺诈。 |
|![IDology 徽标的屏幕截图。](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 是身份验证和证明提供商，提供 ID 验证解决方案、欺诈防御解决方案、合规性解决方案和其他解决方案。|
|![Jumio 徽标的屏幕截图。](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 是一种 ID 验证服务，用于启用实时自动化 ID 验证，从而保护客户数据。 |
| ![LexisNexis 徽标的屏幕截图。](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 是分析和身份验证提供商，验证用户标识，并根据用户设备提供全面风险评估。 |
| ![Onfido 徽标的屏幕截图](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 是一个文档 ID 和面部生物识别验证解决方案，可让公司实时满足“了解你的客户”要求和身份要求。  |

## <a name="mfa-and-passwordless-authentication"></a>MFA 和无密码身份验证

Microsoft 与以下 ISV 合作进行 MFA 和无密码身份验证。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![Hypr 徽标的屏幕截图](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) 是无密码身份验证提供商，可将密码替换为公钥加密，从而消除欺诈、仿冒和凭据重用。 |
| ![itsme 徽标的屏幕截图](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 是一种符合电子标识、身份验证和信任服务 (eiDAS) 标准的数字 ID 解决方案，允许用户安全登录，而无需使用读卡器、密码、双因素身份验证和多个 PIN 代码。 |
|![Keyless 徽标的屏幕截图。](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) 是无密码身份验证提供商，其以面部生物识别扫描的形式提供身份验证，并消除了欺诈、仿冒和凭据重用。
| ![nevis 徽标的屏幕截图](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) 支持无密码身份验证，并提供 Nevis Access 应用移动优先的全品牌最终用户体验，实现强大的客户身份验证并符合 PSD2 事务要求。 |
| ![trusona 徽标的屏幕截图](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 集成可帮助你安全登录并启用无密码身份验证、MFA 和数字许可扫描。 |
| ![twilio 徽标的屏幕截图。](./media/partner-gallery/twilio-logo.png) | [Twilio Verify 应用](./partner-twilio.md)提供多个解决方案，通过 SMS 一次性密码 (OTP)、基于时间的一次性密码 (TOTP) 和推送通知启用 MFA，并符合 PSD2 的 SCA 要求。 |
| ![typingDNA 徽标的屏幕截图](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) 通过分析用户的键入模式，实现强大的客户身份验证。 它可帮助公司实现无提示 MFA 并符合 PSD2 的 SCA 要求。 |
| ![whoiam 徽标的屏幕截图](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 是一个贴有商标的身份管理系统 (BRIMS) 应用程序，使组织能够通过语音、短信和电子邮件来验证其用户群。 |

## <a name="role-based-access-control"></a>基于角色的访问控制 
 
Microsoft 与以下 ISV 合作进行基于角色的访问控制。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![n8identity 徽标的屏幕截图](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) 是一种身份即服务治理平台，提供解决在 Microsoft Azure 上运行的客户帐户迁移和客户服务请求 (CSR) 管理的解决方案。 |
| ![Saviynt 徽标的屏幕截图](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) 云原生平台通过智能分析和跨应用程序集成提高了安全性、合规性和治理能力，从而简化 IT 现代化。 |

## <a name="security"></a>安全性

Microsoft 与以下 ISV 合作实现安全性。

| ISV 合作伙伴 | 说明和集成演练 |
|:-------------------------|:--------------|
| ![Arkose lab 徽标的屏幕截图](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) 是欺诈防御解决方案提供商，可帮助组织防范机器人攻击、帐户接管攻击和欺诈性帐户开户。 |
| ![Microsoft Dynamics 365 徽标的屏幕截图](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 欺诈防护](./partner-dynamics-365-fraud-protection.md)是一种解决方案，可帮助组织通过设备指纹识别防范欺诈性帐户开户。 |
| ![Ping 徽标的屏幕截图](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) 允许跨多个云对本地旧应用程序进行安全的混合访问。 |
| ![strata 徽标的屏幕截图](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) 通过强制使用一致访问策略，使身份保持同步，并简化将应用程序从旧的身份系统转换为 Azure AD B2C 提供的基于标准的身份验证和访问控制，提供对本地应用程序的安全混合访问。 |
| ![zscaler 徽标的屏幕截图](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) 提供对专用应用程序和资产的基于策略的安全访问，而不会带来 VPN 的成本、麻烦或安全风险。 |

## <a name="additional-information"></a>其他信息

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>后续步骤

在所述的表中选择一个合作伙伴，了解如何将其解决方案与 Azure AD B2C 集成。
