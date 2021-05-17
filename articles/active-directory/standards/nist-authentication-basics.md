---
title: NIST 身份验证基础知识和 Azure Active Directory
description: 解释 NIST 的术语和身份验证因素。
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
ms.openlocfilehash: 028ce5fc6ae3cef586803e1b04948006b58fd17e
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293987"
---
# <a name="nist-authentication-basics"></a>NIST 身份验证基础知识 

若要了解 NIST 指导原则，你必须牢牢掌握受信任的平台模块 (TPM) 和身份验证因素的术语和概念。

## <a name="terminology"></a>术语

这些 NIST 相关文章中将使用以下术语。

|术语| 定义-“斜体”术语在此表中定义|
| - | - |
| 断言| 从“验证方”发送到包含“订阅方”信息的“信赖方”的语句。 可能包含已验证的属性。 |
|身份验证| 验证“主体”身份的过程。 |
| 身份验证因素| 你知道的信息、拥有的信息或你的某种身份信息：每个“验证器”都有一个或多个身份验证因素。 |
| 不支持| “请求方”拥有和控制的用于验证“请求方”身份的内容。 |
| 请求方| 将使用一个或多个身份验证协议验证其身份的“主体”。 |
|凭据| 一种对象或数据结构，能以可信的方式将一个身份与“订阅方”拥有和控制的至少一个“验证器”进行绑定。 |
| 凭据服务提供商 (CSP)| 一个受信任的实体，可颁发或注册“订阅方验证器”，并向“订阅方”发送电子“凭据”。 |
|信赖方| 依赖“验证程序断言”或“请求方”的验证器和“凭据”的实体，通常用于授予对系统的访问权限。 |
|  Subject| 个人、组织、设备、硬件、网络、软件或服务。 |
| 订阅者| 从“CSP”接收“凭据”或“验证器”的一方。 |
|受信任的平台模块 (TPM)  | TPM 是一种防篡改模块，可执行加密操作（包括生成密钥）。 |
|  验证方| 用于验证“请求方”身份的实体，具体方法是验证请求方是否拥有和控制“验证器”。 |


## <a name="about-trusted-platform-modules"></a>关于受信任的平台模块 

受信任的平台模块 (TPM) 技术设计用于提供基于硬件的安全性相关的功能。 TPM 芯片（或称硬件 TPM）是一种安全的加密处理器，可帮助你执行各种操作，例如生成、存储加密密钥和限制加密密钥的使用。 

Microsoft 提供了 TPM 如何与 Microsoft Windows 配合使用的重要信息。 有关详细信息，请参阅本文中的[受信任的平台模块](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node)。 

软件 TPM 是模仿此功能的仿真器。 

 ## <a name="authentication-factors-and-their-strengths"></a>身份验证因素及其优点

身份验证因素可以分为三种类别。 下表提供了每种类别中各因素类型的示例。

![用图形表示你知道的信息、拥有的信息或你的某种身份信息。](media/nist-authentication-basics/nist-authentication-basics-0.png)

身份验证因素的强度取决于我们确定只有订阅者知道、拥有或属于此信息的程度。

NIST 对身份验证因素的相对强度提供有限的指导。 在 Microsoft，我们将按如下所示评估这种强度。 

你知道的信息：密码是你知道的最常见的信息，表示具有最大攻击面的信息。 以下缓解措施增强了对订阅方相关性的信心，并有效阻止了密码攻击（如暴力攻击、窃听和社交工程）：

* [密码复杂性要求](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [禁止的密码](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-configure-custom-password-protection)

* [泄露的凭据标识](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [安全的哈希存储](https://aka.ms/AADDataWhitepaper)

* [帐户锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)

你拥有的信息：你拥有的信息的强度取决于订阅方占有此信息的可能性，以及攻击者获取其访问权限的难度。 例如，当尝试防范内部威胁时，个人移动设备或硬件密钥的相关性比办公室台式计算机更高，因而更安全。

你的某些身份信息：攻击者获得你某些身份信息的副本或欺骗生物特征的难易程度很重要。 NIST 正在起草生物特征识别框架。 现在，NIST 不接受将生物特征识别作为单独的身份验证方法。 它必须是多重身份验证的其中一个因素。 这是因为生物特征识别本来就带有概率性。 也就是说，这些识别方法使用确定这是同一个人的可能性的算法。 这并不一定像密码那样完全匹配。 如需了解[验证器的功能强度 - 生物识别](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B)，请参阅本文档。 SOFA-B 尝试提出一种量化生物识别技术强度的框架，旨在根据错误匹配率、错误率、失败率、呈现攻击检测错误率和发起攻击所需的努力来量化处理。 

## <a name="single-factor-authentication"></a>单因素身份验证

单因素身份验证可通过构成你知道的信息或你的某些身份信息的单因素身份验证器来实现。 虽然可以接受将“你的某些身份信息”作为身份验证因素，但其本身不应作为验证器。 

![单因素身份验证的概念图。](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multi-factor-authentication"></a>多重身份验证

多重身份验证可通过多重身份验证器或两个单因素验证器的组合来实现。 多重身份验证器需要两个身份验证因素，才能执行单个身份验证事务。

### <a name="multi-factor-authentication-using-two-single-factor-authenticators"></a>使用两个单因素验证器的多重身份验证

多重身份验证需要两个不同的身份验证因素。 这些因素可能是两个独立的验证器，例如 

* 记住的密码 [密码] 和带外 [SMS]

* 记住的密码 [密码] 和一次性密码 [硬件或软件]

这些方法使用 Azure AD 执行两个独立的身份验证事务。

![使用两个单独的验证器进行多重身份验证的概念图。](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multi-factor-authentication-using-a-single-multi-factor-authenticator"></a>使用单个多重身份验证器进行多重身份验证

多重身份验证要求使用一个身份验证因素（你知道的信息或你的某些身份信息）来解锁第二个身份验证因素。 与多个独立的身份验证器相比，这种用户体验通常更为简单。

![多重身份验证、单个多重身份验证器的概念图。](media/nist-authentication-basics/nist-authentication-basics-3a.png)

例如，在无密码模式下使用的 Microsoft Authenticator 应用程序。 使用此方法，用户可尝试访问受保护的资源（信赖方），并在其验证器应用程序上收到通知。 用户通过以下方式响应通知：提供生物特征（你的某些身份信息）或 PIN（你知道的信息），然后根据这些信息解锁手机上的加密密钥（你拥有的信息），此信息随后由验证方进行验证。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md) 