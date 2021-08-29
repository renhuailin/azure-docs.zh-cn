---
title: NIST 身份验证基础知识和 Azure Active Directory
description: 本文阐释了重要的术语，并介绍了适用于 NIST 的受信任平台模块和身份验证因素。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eacdb19dcd5121f1d7fecdf6a3cefbafd609c9a9
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889825"
---
# <a name="nist-authentication-basics"></a>NIST 身份验证基础知识 

若要了解美国国家标准和技术协会 (NIST) 准则，你需要了解术语。 你还需要了解受信任的平台模块 (TPM) 技术和身份验证因素。 本文提供了该信息。 

## <a name="terminology"></a>术语

以下术语贯穿这些 NIST 文章。

|术语| 定义。 此表阐释了以斜体显示的术语。|
| - | - |
| Assertion| 从 *验证方* 发送到 *信赖方* 的陈述，其中包含 *订阅方* 信息。 断言可能包含已验证的属性。 |
|身份验证| 验证“主体”身份的过程。 |
| 身份验证因素| 你知道的信息、你拥有的东西，或者你的身份信息。 每个验证器都有一个或多个身份验证因素。 |
| 不支持| 请求方拥有和控制的用于验证请求方身份的内容。 |
| 请求方| 将通过一个或多个身份验证协议验证其身份的主体。 |
|凭据| 一种对象或数据结构，能以可信的方式将一个身份与“订阅方”拥有和控制的至少一个“验证器”进行绑定。 |
| 凭据服务提供商 (CSP)| 一个受信任的实体，可颁发或注册“订阅方验证器”，并向“订阅方”发送电子“凭据”。 |
|信赖方| 依赖于验证方的断言或请求方的验证器和凭据的一个实体，通常用于授予对系统的访问权限。 |
|  Subject| 个人、组织、设备、硬件、网络、软件或服务。 |
| 订阅者| 从“CSP”接收“凭据”或“验证器”的一方。 |
|受信任的平台模块  | TPM 是一个防篡改模块，用于执行加密操作（包括生成密钥）。 |
|  验证方| 用于验证“请求方”身份的实体，具体方法是验证请求方是否拥有和控制“验证器”。 |


## <a name="about-trusted-platform-module-technology"></a>关于受信任的平台模块技术

受信任的平台模块技术设计用于提供与基于硬件的安全性相关的功能。 TPM 芯片（也称硬件 TPM）是一种安全的加密处理器，可帮助你执行各种操作，例如生成、存储加密密钥和限制加密密钥的使用。 

Microsoft 提供了有关 TPM 如何与 Windows 配合使用的重要信息。 有关详细信息，请参阅[受信任的平台模块](/windows/security/information-protection/tpm/trusted-platform-module-top-node)。 

软件 TPM 是模仿硬件 TPM 功能的仿真器。 

 ## <a name="authentication-factors-and-their-strengths"></a>身份验证因素及其优点

身份验证因素可以分为三种类别：

![图中提供了身份验证因素示例，这些因素分为你知道的信息、你拥有的东西以及你的身份信息。](media/nist-authentication-basics/nist-authentication-basics-0.png)

身份验证因素的强度取决于你在多大程度上确信它是只有订阅方知道的信息、拥有的东西或所属的身份。

NIST 针对身份验证因素的相对强度提供了有限的指导。 本部分的其余内容介绍了在 Microsoft 我们如何评估这些强度。 

你知道的信息。 密码是你知道的最常见的信息，是具有最大攻击面的信息。 以下缓解措施可增强对订阅方相关性的信心。 它们可有效防止密码攻击，例如暴力攻击、窃听和社交工程：

* [密码复杂性要求](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [禁止的密码](../authentication/tutorial-configure-custom-password-protection.md)

* [泄露的凭据标识](../identity-protection/overview-identity-protection.md)

* [安全的哈希存储](https://aka.ms/AADDataWhitepaper)

* [帐户锁定](../authentication/howto-password-smart-lockout.md)

你拥有的东西。 你拥有的东西：你拥有的东西的强度取决于订阅方始终拥有它的可能性，以及攻击者获得它的难度。 例如，当你尝试防范内部威胁时，个人移动设备或硬件密钥的相关性会更高。 因此，它比办公室中的台式机更安全。

你的身份信息。 攻击者获得你的身份信息的副本或伪造生物特征的难易程度至关重要。 NIST 正在起草生物特征识别框架。 目前，NIST 不接受将生物识别作为单独的身份验证方法。 它必须是多重身份验证的其中一个因素。 实施此预防措施是因为生物识别在本质上是基于概率的。 也就是说，生物识别使用的是确定关联可能性的算法。 生物识别不一定提供完全匹配（与密码一样）。 有关详细信息，请参阅[验证器的功能强度 - 生物识别](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B)。 

SOFA-B 试图提供一个框架来量化生物特征的强度，用于计算：
- 误匹配率。
- 误失败率。
- 呈现攻击检测错误率。
- 执行攻击所需的工作量。 

## <a name="single-factor-authentication"></a>单因素身份验证

可以使用一个对你知道的信息或你的身份信息进行验证的单因素验证器来实施单因素身份验证。  虽然可以接受将“你的身份信息”因素作为身份验证因素，但其本身不应作为验证器。 

![图中展示了单因素身份验证如何工作。](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multifactor-authentication"></a>多重身份验证

可以使用一个多因素验证器或两个单因素验证器来实施多重身份验证。 多因素验证器需要两个身份验证因素才能完成单个身份验证事务。

### <a name="multifactor-authentication-by-using-two-single-factor-authenticators"></a>使用两个单因素验证器的多重身份验证

多重身份验证需要两个不同的身份验证因素。 这些验证器可以是独立的。 例如： 

* 记住的机密（密码）和带外数据（短信）

* 记住的机密（密码）和一次性密码（硬件或软件）

这些方法使用 Azure Active Directory (Azure AD) 执行两个独立的身份验证事务。

![图中描绘了通过两个单独的验证器进行的多重身份验证。](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multifactor-authentication-by-using-a-single-multifactor-authenticator"></a>使用单个多因素验证器的多重身份验证

多重身份验证要求使用一个身份验证因素（你知道的信息或你的身份信息）来解锁另二个身份验证因素。  用户体验通常比多个独立的验证器更轻松。

![图中展示了使用单个多因素验证器进行的多重身份验证。](media/nist-authentication-basics/nist-authentication-basics-3a.png)

例如，在无密码模式下使用的 Microsoft Authenticator 应用。 使用此方法，用户在尝试访问受保护的资源（信赖方）时会在 Authenticator 应用上收到通知。 用户通过提供生物特征（“你的身份信息”）或 PIN（“你知道的信息”）来响应通知。 此因素会解锁手机上的加密密钥（“你拥有的东西”），然后验证方对该密钥进行验证。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md)