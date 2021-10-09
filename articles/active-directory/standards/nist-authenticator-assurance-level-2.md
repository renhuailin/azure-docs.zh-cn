---
title: 使用 Azure Active Directory 满足 NIST AAL2 要求
description: 使用 Azure Active Directory 满足 NIST 验证器保证级别 2 (AAL2) 要求的指南。
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
ms.openlocfilehash: 30287957ca9d44c27f0d46efb6ae358db7d6ab44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662260"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>使用 Azure Active Directory 满足 NIST 验证器保证级别 2 要求

美国国家标准与技术研究院 (NIST) 为实施标识解决方案的美国联邦机构制定了技术要求。 与联邦机构合作的组织也必须满足这些要求。 

在尝试满足验证器保证级别 2 (AAL2)　要求之前，可能需要查看以下资源：
* [NIST 概述](nist-overview.md)：了解不同的 AAL 级别。
* [身份验证基础知识](nist-authentication-basics.md)：重要的术语和身份验证类型。
* [NIST 验证器类型](nist-authenticator-types.md)：了解每种验证器类型。
* [NIST AAL](nist-about-authenticator-assurance-levels.md)：介绍 AAL 的组件以及 Azure Active Directory (Azure AD) 身份验证方法与之相映射的方式。

## <a name="permitted-authenticator-types"></a>允许的验证器类型

下表提供有关 AAL2 允许的验证器类型的详细信息：

| Azure AD 身份验证方法| NIST 验证器类型 | 
| - | - |
| 推荐的方法 |   | 
| 适用于 iOS 的 Microsoft Authenticator 应用（无密码）<br>带受信任的平台模块 (TPM) 软件的 Windows Hello 企业版 | 多重加密软件 |
| FIDO 2 安全密钥<br>适用于 Android 的 Microsoft Authenticator 应用（无密码）<br>带硬件 TPM 的 Windows Hello 企业版<br>智能卡（Active Directory 联合身份验证服务） | 多重加密硬件 |
| 其他方法 |  |
| 密码 + 电话 (SMS) | 记住的密码 + 带外 |
| 密码 + Microsoft Authenticator 应用 (OTP)<br>密码 + SF OTP | 记住的密码 + 单因素一次性密码 |
| 密码 + 带软件 TPM 的 Azure AD 联接 <br>密码 + 兼容的移动设备<br>密码 + 带软件 TPM 的混合 Azure AD 联接 <br>密码 + Microsoft Authenticator 应用（通知） | 记住的密码 + 单因素加密软件 |
| 密码 + 带硬件 TPM 的 Azure AD 联接 <br>密码 + 带硬件 TPM 的混合 Azure AD 联接 | 记住的密码 + 单因素加密硬件 |

> [!NOTE]
> 在条件访问策略中，如果需要将设备标记为合规或已建立混合 Azure AD 联接，则 Authenticator 会充当验证器模拟电阻。

### <a name="our-recommendations"></a>我们的建议

若要满足 AAL2 要求，请使用多重加密硬件或软件验证器。 无密码身份验证消除了最大的攻击面（密码），并为用户提供了一种简化的身份验证方法。 

有关选择无密码身份验证方法的详细指南，请参阅[在 Azure Active Directory 中规划无密码身份验证部署](../authentication/howto-authentication-passwordless-deployment.md)。

有关实施 Windows Hello 企业版的详细信息，请参阅 [Windows Hello 企业版部署指南](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)。

## <a name="fips-140-validation"></a>FIPS 140 验证

以下部分讨论如何满足 FIPS 140 验证要求。

### <a name="verifier-requirements"></a>验证程序要求

Azure AD 会使用经过验证且总体达到 Windows FIPS 140 级别 1 的加密模块来执行其所有与身份验证相关的加密操作。 因此，根据政府机构的要求，该验证程序符合 FIPS 140 要求。

### <a name="authenticator-requirements"></a>验证器要求

政府机构的加密验证器需要经过验证且整体达到 FIPS 140 级别 1 要求。 对于非政府机构，则无此要求。 [在 Windows 上以 FIPS 140 批准的操作模式](/windows/security/threat-protection/fips-140-validation)运行时，以下 Azure AD 验证器满足此要求：

* 密码

* 带软件或带硬件 TPM 的 Azure AD 联接

* 带软件或带硬件 TPM 的混合 Azure AD 联接

* 带软件或带硬件 TPM 的 Windows Hello 企业版

* 智能卡（Active Directory 联合身份验证服务） 

尽管 Microsoft Authenticator 应用在其所有模式（通知、OTP 和无密码）中均使用 FIPS 140 批准的加密，但未经 FIPS 140 级别 1 验证。

FIDO2 安全密钥提供程序处于 FIPS 认证的不同阶段，其中包括已完成验证的一些程序。 建议查看[支持的 FIDO2 密钥供应商列表](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)，并向提供商咨询当前的 FIPS 验证状态。


## <a name="reauthentication"></a>重新身份验证 

对于 AAL2　级别，NIST 要求无论用户是否处于活动状态，均需每 12 小时重新进行身份验证。 在持续 30 分钟或更长时间内处于非活动状态时也需要重新进行身份验证。 由于你拥有会话机密，因此需要提供你知道或关于你身份的内容。

为了满足不考虑用户活动而重新进行身份验证的要求，Microsoft 建议将[用户登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)配置为 12 小时。 

NIST 还允许使用补偿控制来确认订阅者的状态：

* 利用 Microsoft System Center Configuration Manager、组策略对象 (GPO) 或 Intune，用户可将会话不活动超时设置为 30 分钟，进而在操作系统级别锁定设备。 还需要进行本地身份验证，订阅者才能将其解锁。

* （利用 Configuration Manager、GPO 或 Intune）运行计划任务，在 12 小时后锁定计算机（无论用户是否处于活动状态），从而实现与活动状态无关的超时。

## <a name="man-in-the-middle-resistance"></a>抵御中间人攻击 

请求方和 Azure AD 之间的所有通信均通过经身份验证的受保护通道进行，以抵御中间人 (MitM) 攻击。 这满足 AAL1、AAL2 和 AAL3 对抵御 MitM 攻击的要求。

## <a name="replay-resistance"></a>重放抵抗

AAL2 上的所有 Azure AD 身份验证方法都使用 nonce 或质询。 这些方法可以抵御重放攻击，因为验证程序可轻松检测重放的身份验证事务。 此类事务不包含相应的 nonce 或及时性数据。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md)
