---
title: 通过 Azure Active Directory 实现 NIST 验证器的保障级别
description: 的概述
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: ''
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b27145a67c46578230fe9a9d588a149937d7f56
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293951"
---
# <a name="configure-azure-active-directory-to-meet-nist-authenticator-assurance-levels"></a>配置 Azure Active Directory 以满足 NIST 验证器保障级别

由于必须满足的标准数量较多且较为复杂，因此为联邦机构提供服务也比较复杂。 作为云服务提供商 (CSP) 或联邦机构，你应确保符合所有相关标准。 通过 Azure 和 Azure Active Directory，你可以充分利用我们的认证，然后再配置你的特定需求，轻松实现此目的。
Azure 获得了 90 多个合规性产品/服务的认证。 有关 Azure 合规性和认证的详细信息，请参阅[信任你的云](https://azure.microsoft.com/overview/trusted-cloud/)。

## <a name="why-meet-nist-standards"></a>为什么要满足 NIST 标准？ 

美国国家标准与技术研究院 (NIST) 为美国联邦机构实现标识解决方案制定技术要求。 与联邦机构合作的组织也必须满足这些要求。 如需了解 NIST 标识要求，请参阅文档 [Special Publication 800-63 Revision 3](https://pages.nist.gov/800-63-3/sp800-63-3.html)（特殊出版物 800-63 修订版 3）(NIST SP 800-63-3)。

NIST SP 800-63 还被以下内容引用过：
* 受管制电子药物处方 [ECPS](https://deadiversion.usdoj.gov/ecomm/e_rx/) 计划 
* [美国金融业监管局 (FINRA) 要求](https://www.finra.org/rules-guidance)。 
* 医疗保健、国防和其他行业协会通常使用 NIST SP 800-63-3 作为标识和访问管理 (IAM) 要求的基线。

NIST 准则是其他标准的参考，最值得注意的是 CSP 的联邦风险与授权管理计划 (FedRAMP)。 Azure 经过 FedRAMP High Impact 认证。 

NIST 数字标识准则涵盖用户的证明和身份验证，这些用户包括员工、合作伙伴、供应商、客户或公民。 

NIST SP 800-63-3 数字标识准则包含以下三个方面：

* [SP 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html) 涵盖注册和身份证明

* [SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) 涵盖身份验证和声明周期管理

* [SP 800-63C](https://pages.nist.gov/800-63-3/sp800-63c.html) 涵盖联合身份验证和断言

每个方面都制定了保障级别。 本文介绍如何通过使用 Azure Active Directory 和其他 Microsoft 解决方案实现 NIST SP 800-63B 中的验证器保障级别 (AAL)。

## <a name="next-steps"></a>后续步骤 

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[通过 Azure AD 实现 NIST AAL1](nist-authenticator-assurance-level-1.md)

[通过 Azure AD 实现 NIST AAL2](nist-authenticator-assurance-level-2.md)

[通过 Azure AD 实现 NIST AAL3](nist-authenticator-assurance-level-3.md) 
