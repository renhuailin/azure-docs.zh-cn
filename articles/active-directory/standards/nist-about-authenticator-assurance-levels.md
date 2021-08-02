---
title: 使用 Azure Active Directory 的 NIST 验证器保证级别
description: 概述适用于 Azure Active Directory 的验证器保证级别
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
ms.openlocfilehash: 72462bf14fb8c287335e0497cbaa00102521b310
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889843"
---
# <a name="about-authenticator-assurance-levels"></a>关于验证器保证级别

美国国家标准与技术研究院 (NIST) 为实施标识解决方案的美国联邦机构制定了技术要求。 [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) 定义了实施数字身份验证的技术指导原则。 这可通过验证器保证级别 (AAL) 框架来实现。 AAL 描述了数字标识身份验证的优势。 本指南还介绍了如何管理验证器生命周期（包括吊销）。 

该标准包含这些要求类别应满足的 AAL 要求：

* 允许的验证器类型

* 美国联邦信息处理标准 140 (FIPS 140) 验证级别（[FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) 或更高版本满足 FIPS 140 要求）

* 重新身份验证

* 安全控件

* 抵御中间人 (MitM) 攻击

* 验证程序模拟抵抗（抵御网络钓鱼攻击）

* 验证程序泄露抵抗

* 重放抵抗

* 身份验证意向

* 记录保留策略

* 隐私控制

## <a name="apply-nist-aals-in-your-environment"></a>在环境中应用 NIST AAL

> [!TIP]
> 建议至少满足 AAL2 要求。 除非业务原因、行业标准或合规性要求需要你满足 AAL3 要求。

通常，不建议使用 AAL1，因为它接受仅使用密码的解决方案，而密码是最容易遭到破坏的身份验证形式。 有关详细信息，请参阅以下博客文章：[密码并不重要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)。 

虽然 NIST 规定在达到 AAL3 之前，无需验证程序模拟（也称为凭据网络钓鱼）抵抗，但仍强烈建议你在所有级别解决此威胁。 可以选择提供验证程序模拟抵抗（例如需要联接到 Azure Active Directory (Azure AD) 或混合 Azure AD 的设备）的验证器。 如果使用的是 Office 365，则可以使用 Office 365 高级威胁防护，尤其是[防网络钓鱼策略](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies)。

在评估适合组织的 NIST AAL 时，请考虑整个组织是否必须满足 NIST 标准。 如果可以隔离特定的用户和资源组，则可以将 NIST AAL 配置仅适用于特定的用户和资源组。 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>安全控制、隐私控制、记录保留策略

Azure 和 Azure 政府已从联合授权委员会获得了 [NIST SP 800-53 高影响级别](https://nvd.nist.gov/800-53/Rev4/impact/high)的暂时运营授权 (P-ATO)。 此级别表示 FedRAMP 认证的最高标准，并授权使用 Azure 和 Azure 政府版处理高度敏感的数据。

这些 Azure 和 Azure 政府版认证满足 AAL1、AAL2 和 AAL3 对安全控制、隐私控制和记录保留策略的要求。

对 Azure 和 Azure 政府版的 FedRAMP 审核包括信息安全管理系统，该系统包含范围内服务的基础结构、开发、操作、管理和支持。 获得 P-ATO 授权后，云服务提供商仍需要从与之合作的任何政府机构获得授权 (ATO)。 对于 Azure，政府机构或与之合作的组织可以在其自身的安全授权过程中使用 Azure ATO。 该机构或组织可以此为基础来发布也满足 FedRAMP 要求的机构 ATO。

与任何其他云提供商相比，Azure 继续在 FedRAMP High 影响级别支持更多的服务。 尽管 Azure 公有云中的 FedRAMP High 可满足多个美国政府客户的需求，但要求更严格的机构会依赖于 Azure 政府。 Azure 政府提供额外的保护措施，例如加强人员筛选。 Microsoft 列出了 Azure 政府版中当前可满足 FedRAMP High 的所有 Azure 公共服务，以及针对当前年份计划的服务。

此外，Microsoft 还完全致力于通过明确规定的记录保留策略来[保护和管理客户数据](https://www.microsoft.com/trust-center/privacy/data-management)。 Microsoft 是一家全球性公司，其客户几乎遍及世界上所有国家/地区，拥有强大的合规性产品组合，可为客户提供帮助。 若要查看我们的合规性产品/服务的完整列表，请参阅 [Microsoft 合规性产品/服务](/compliance/regulatory/offering-home)。 

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md)