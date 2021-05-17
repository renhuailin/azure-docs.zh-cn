---
title: 使用 Azure Active Directory 满足 NIST AAL2 要求
description: 本指南关于如何使用 Azure Active Directory 满足 NIST 验证器保证级别 2 (AAL 2) 要求。
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
ms.openlocfilehash: aaa40141eaa0617c34a0ae8c4a7cf396d624c1b6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293953"
---
# <a name="achieving-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>使用 Azure Active Directory 满足 NIST 验证器保证级别 2 要求

美国国家标准与技术研究院 (NIST) 为实施标识解决方案的美国联邦机构制定了技术要求。 与联邦机构合作的组织也必须满足这些要求。 本文指导你满足 NIST 身份验证保证级别 2 (AAL2) 要求。 

尝试满足 AAL 2 要求之前，你可能希望查看的资源：
* [NIST 概述](nist-overview.md) - 了解不同的 AAL 级别。
* [身份验证基础知识](nist-authentication-basics.md) - 重要的术语和身份验证类型。
* [NIST 验证器类型](nist-authenticator-types.md) - 了解每种验证器类型。
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - AAL 的组件以及 Microsoft Azure Active Directory 身份验证方法与之相映射的方式。

## <a name="permitted-authenticator-types"></a>允许的验证器类型


| Azure AD 身份验证方法| NIST 验证器类型 | 
| - | - |
| 推荐的方法 |   | 
| 适用于 iOS 的 Microsoft Authenticator 应用（无密码）<br>带软件 TPM 的 Windows Hello 企业版 | 多重加密软件 |
| FIDO 2 安全密钥<br>适用于 Android 的 Microsoft Authenticator 应用（无密码）<br>带硬件 TPM 的 Windows Hello 企业版<br>智能卡 (ADFS) | 多重加密硬件 |
| 其他方法 |  |
| 密码 + 电话 (SMS) | 记住的密码 + 带外 |
| 密码 + Microsoft Authenticator 应用 (OTP)<br>密码 + SF OTP | 记住的密码 + 单因素一次性密码 |
| 密码 + 带软件 TPM 的 Azure AD 联接 <br>密码 + 兼容的移动设备<br>密码 + 带软件 TPM 的混合 Azure AD 联接 <br>密码 + Microsoft Authenticator 应用（通知） | 记住的密码 + 单因素加密软件 |
| 密码 + 带硬件 TPM 的 Azure AD 联接 <br>密码 + 带硬件 TPM 的混合 Azure AD 联接 | 记住的密码 + 单因素加密硬件 |


### <a name="our-recommendations"></a>我们的建议

建议使用多重加密硬件或软件验证器来满足 AAL2 要求。 无密码身份验证消除了最大的攻击面（密码），并为用户提供了一种简化的身份验证方法。 

有关选择无密码身份验证方法的详细指南，请参阅[在 Azure Active Directory 中规划无密码身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment)。

有关实施 Windows Hello 企业版的详细信息，请参阅 [Windows Hello 企业版部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)。

## <a name="fips-140-validation"></a>FIPS 140 验证

以下信息可指导你满足 FIPS 140 验证要求。

### <a name="verifier-requirements"></a>验证程序要求

Azure AD 将经过验证且总体达到 FIPS 140 级别 1 的 Windows 加密模块   
用于其所有与身份验证相关的加密操作。 因此，根据政府机构的要求，该验证程序符合 FIPS 140 标准。

### <a name="authenticator-requirements"></a>验证器要求

政府机构的加密验证器需要经过验证且总体达到 FIPS 140 级别 1。 对于非政府机构，则无此要求。 [在 Windows 上以 FIPS 140 批准的操作模式](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)运行时，以下 Azure AD 验证器满足要求

* 密码

* 带软件或带硬件 TPM 的 Azure AD 联接

* 带软件或带硬件 TPM 的混合 Azure AD 联接

* 带软件或带硬件 TPM 的 Windows Hello 企业版

* 智能卡 (ADFS) 

截至本文撰写之时，FIDO2 安全密钥和 Microsoft Authenticator 应用（通知、OTP 和无密码等所有模式）均不符合政府机构对 FIPS 140 级别 1 总体验证的要求。

* Microsoft Authenticator 应用使用 FIPS 140 批准的加密；但是，它尚未经过 FIPS 140 级别 1 的总体验证。 

* FIDO2 密钥是最新创新，因此仍处于 FIPS 认证过程中。

## <a name="reauthentication"></a>重新身份验证 

对于 AAL2，NIST 要求无论用户是否处于活动状态，需每 12 小时重新进行身份验证，并且在持续 30 分钟或更长时间内处于非活动状态时也需要重新进行身份验证。 由于你拥有会话机密，因此需要提供你知道或关于你身份的内容。

为了满足不考虑用户活动而重新进行身份验证的要求，Microsoft 建议将[用户登录频率](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)配置为 12 小时。 

NIST 还允许使用补偿控制来确认订阅者的状态：

* 利用 Microsoft System Center Configuration Manager (SCCM)、组策略对象 (GPO) 或 Intune，可以在 OS 级别锁定设备，从而可在用户处于非活动状态 30 分钟后结束会话。 还需要进行本地身份验证，订阅者才能将其解锁。

* （利用 SCCM、GPO 或 Intune）运行计划任务，可以在 12 小时后锁定计算机（无论用户是否处于活动状态），从而实现与活动状态无关的超时。

## <a name="man-in-the-middle-mitm-resistance"></a>抵御中间人 (MitM) 攻击 

请求方和 Azure AD 之间的所有通信均通过经身份验证的受保护通道进行，以抵御 MitM 攻击。 这满足 AAL1、AAL2 和 AAL3 对抵御 MitM 攻击的要求。

## <a name="replay-resistance"></a>重放抵抗

AAL2 上的所有 Azure AD 身份验证方法要么使用 nonce，要么使用质询，并且可以抵抗重放攻击，这是因为身份验证事务不包含相应的 nonce 或及时性数据，因此验证程序可轻松检测重放的身份验证事务。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md)  