---
title: 使用 Azure Active Directory 满足 NIST AAL1 要求
description: 使用 Azure Active Directory 满足 NIST 验证器保证等级 1 (AAL 1) 要求的指南。
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
ms.openlocfilehash: 640c401beb42b07465f533fa14b5b7fd4b0200e5
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293948"
---
# <a name="achieving-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>使用 Azure Active Directory 满足 NIST 验证器保证等级 1 要求

美国国家标准与技术研究院 (NIST) 为实施标识解决方案的美国联邦机构制定了技术要求。 与联邦机构合作的组织也必须满足这些要求。 本文指导你满足 NIST 身份验证保证等级 1 (AAL1) 要求。 

尝试满足 AAL 1 要求之前，你可能希望查看的资源：
* [NIST 概述](nist-overview.md) - 了解不同的 AAL 级别。
* [身份验证基础知识](nist-authentication-basics.md) - 重要的术语和身份验证类型。
* [NIST 验证器类型](nist-authenticator-types.md) - 了解每种验证器类型。
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - 了解 AAL 的组成部分、Microsoft Azure Active Directory 身份验证方法与之映射的方式以及受信任的平台模块 (TPM)。 

## <a name="permitted-authenticator-types"></a>允许的验证器类型

 可使用允许的任何 NIST 单一或多重[验证器](nist-authenticator-types.md)来满足 AAL1 要求。 下表包含 [AAL2](nist-authenticator-assurance-level-2.md) 和 [AAL3](nist-authenticator-assurance-level-2.md) 中未涵盖的内容。

| Azure AD 身份验证方法| NIST 验证器类型 |
| - | - |
| 密码 |记住的密码 |
| 电话（短信）|  带外 |
|  FIDO 2 安全密钥 <br>适用于 iOS 的 Microsoft Authenticator 应用（无密码）<br>带软件 TPM 的 Windows Hello 企业版 <br>智能卡 (ADFS) |  多重加密软件 |

> [!TIP]
> 建议至少满足 AAL 2 要求，除非业务原因、行业标准或合规性要求需要你满足 AAL3 要求。

## <a name="fips-140-validation"></a>FIPS 140 验证

### <a name="verifier-requirements"></a>验证程序要求

Azure AD 将经过验证且总体达到 FIPS 140 级别 1 的 Windows 加密模块   
用于其所有与身份验证相关的加密操作。 因此，根据政府机构的要求，该验证程序符合 FIPS 140 标准。

## <a name="man-in-the-middle-mitm-resistance"></a>抵御中间人 (MitM) 攻击 

请求方和 Azure AD 之间的所有通信均通过经身份验证的受保护通道进行，以抵御 MitM 攻击。 这满足 AAL1、AAL2 和 AAL3 对抵御 MitM 攻击的要求。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md) 