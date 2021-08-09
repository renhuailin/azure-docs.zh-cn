---
title: 使用 Azure Active Directory 满足 NIST AAL1 要求
description: 有关如何使用 Azure Active Directory 满足 NIST 验证器保证等级 1 (AAL1) 要求的指南。
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
ms.openlocfilehash: d38dfd79eefe8b7b1bfb8119a62b139b654f2fe5
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540464"
---
# <a name="achieve-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>使用 Azure Active Directory 满足 NIST 验证器保证等级 1 要求

美国国家标准与技术研究院 (NIST) 为实施标识解决方案的美国联邦机构制定了技术要求。 与联邦机构合作的组织也必须满足这些要求。 

尝试满足验证器保证等级 1 (AAL1) 要求之前，你可能希望查看的资源：
* [NIST 概述](nist-overview.md)：了解不同的 AAL 级别。
* [身份验证基础知识](nist-authentication-basics.md)：重要的术语和身份验证类型。
* [NIST 验证器类型](nist-authenticator-types.md)：了解每种验证器类型。
* [NIST AAL](nist-about-authenticator-assurance-levels.md)：了解 AAL 的组成部分、Azure Active Directory (Azure AD) 身份验证方法与之映射的方式以及受信任的平台模块 (TPM)。 

## <a name="permitted-authenticator-types"></a>允许的验证器类型

 可使用允许的任何 NIST 单一或多重[验证器](nist-authenticator-types.md)来满足 AAL1 要求。 下表包含 [AAL2](nist-authenticator-assurance-level-2.md) 和 [AAL3](nist-authenticator-assurance-level-2.md) 中未涵盖的内容。

| Azure AD 身份验证方法| NIST 验证器类型 |
| - | - |
| 密码 |记住的密码 |
| 电话（短信）|  带外 |
|  FIDO 2 安全密钥 <br>适用于 iOS 的 Microsoft Authenticator 应用（无密码）<br>带软件 TPM 的 Windows Hello 企业版 <br>智能卡（Active Directory 联合身份验证服务） |  多重加密软件 |

> [!TIP]
> 建议至少满足 AAL2 要求。 除非业务原因、行业标准或合规性要求需要你满足 AAL3 要求。

## <a name="fips-140-validation"></a>FIPS 140 验证

### <a name="verifier-requirements"></a>验证程序要求

Azure AD 会使用经过验证且总体达到 Windows FIPS 140 级别 1 的加密模块来执行其所有与身份验证相关的加密操作。 因此，根据政府机构的要求，该验证程序符合 FIPS 140 标准。

## <a name="man-in-the-middle-resistance"></a>抵御中间人 (MitM) 攻击 

请求方和 Azure AD 之间的所有通信均通过经身份验证的受保护通道进行，以抵御中间人 (MitM) 攻击。 这满足 AAL1、AAL2 和 AAL3 对抵御 MitM 攻击的要求。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md) 