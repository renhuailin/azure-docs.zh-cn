---
title: 使用 Azure Active Directory 满足 NIST AAL3 要求
description: 文本提供有关如何使用 Azure Active Directory 满足 NIST 验证器保证等级 3 (AAL3) 要求的指南。
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
ms.openlocfilehash: d95bfadd23e1a8e0d2db70952c81667d24dc4230
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771020"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>使用 Azure Active Directory 满足 NIST 验证器保证等级 3 要求

本文将帮助你满足美国国家标准与技术研究院验证器保证等级 (NIST AAL) 3 要求。 在尝试满足 AAL3 要求之前，你可能需要查看这些资源：
* [NIST 概述](nist-overview.md)：了解不同的 AAL 级别。
* [身份验证基础知识](nist-authentication-basics.md)：重要的术语和身份验证类型。
* [NIST 验证器类型](nist-authenticator-types.md)：了解每种验证器类型。
* [NIST AAL](nist-about-authenticator-assurance-levels.md)：介绍 AAL 的组件以及 Azure Active Directory (Azure AD) 身份验证方法与之相映射的方式。

## <a name="permitted-authenticator-types"></a>允许的验证器类型
Microsoft 提供的身份验证方法有助于满足所需的 NIST 验证器类型要求。 本部分提供 Microsoft 建议。
 

| Azure AD 身份验证方法| NIST 验证器类型 |
| - | -|
| 推荐的方法|    |
| FIDO2 安全密钥<br>或<br> 智能卡（Active Directory 联合身份验证服务 [AD FS]）<br>或<br>带硬件 TPM 的 Windows Hello 企业版| 多重加密硬件 |
| 其他方法|   |
| 密码<br> 和<br>（已联接硬件 TPM 的混合 Azure AD <br>或 <br> 已联接硬件 TPM 的 Azure AD）| 记住的密码<br>和<br> 单因素加密硬件 |
| 密码 <br>和<br>（单因素一次性密码硬件（来自 OTP 制造商） <br>或<br>已联接软件 TPM 的混合 Azure AD <br>或 <br> 已联接软件 TPM 的 Azure AD <br>或<br> 兼容的托管设备）| 记住的密码 <br>和<br>单因素一次性密码硬件<br> 和<br>单因素加密软件 |

### <a name="our-recommendations"></a>我们的建议 

建议使用多重加密硬件验证器来满足 AAL3 要求。 无密码身份验证消除了最大的攻击面（密码），并为用户提供了一种简化的身份验证方法。 如果你的组织完全基于云，建议使用 FIDO2 安全密钥。

请注意，Windows Hello 企业版尚未在所需的 FIPS 140 安全级别进行验证，因此，在认可其达到 AAL3 之前，联邦客户需要进行风险评估和评价。 .

有关详细指南，请参阅[在 Azure Active Directory 中规划无密码身份验证部署](../authentication/howto-authentication-passwordless-deployment.md)。

有关实施 Windows Hello 企业版的详细信息，请参阅 [Windows Hello 企业版部署指南](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)。

## <a name="fips-140-validation"></a>FIPS 140 验证

### <a name="verifier-requirements"></a>验证程序要求

Azure AD 会使用经过验证且总体达到 Windows FIPS 140 级别 1 的加密   
‎模块来执行其所有与身份验证相关的加密操作。 因此，该验证程序符合 FIPS 140 标准。

### <a name="authenticator-requirements"></a>验证器要求

单因素和多重加密硬件验证器需要满足不同的验证器要求。 

单因素加密硬件验证器需要满足以下要求：

* 整体达到 FIPS 140 级别 1（或更高）。

* 物理安全性达到 FIPS 140 级别 3（或更高）。

Azure AD 联接和混合 Azure AD 联接设备在下列情况中满足此要求： 

* [以 FIPS 140 批准的操作模式运行 Windows](/windows/security/threat-protection/fips-140-validation)。 

* 计算机的 TPM 整体达到 FIPS 140 级别 1（或更高）或物理安全性达到 FIPS 140 级别 3。 

   * 通过在[加密模块验证计划](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)页中搜索“受信任的平台模块”和“TPM”，查找兼容 TPM。

咨询移动设备供应商，了解你的供应商是否符合 FIPS 140 要求。

多重加密硬件验证器需要满足以下要求： 

* 整体达到 FIPS 140 级别 2（或更高）。

* 物理安全性达到 FIPS 140 级别 3（或更高）。

FIDO2 安全密钥、智能卡和 Windows Hello 企业版有助于满足这些要求。

* FIDO2 密钥提供程序处于 FIPS 认证的不同阶段，其中包括已完成验证的一些程序。 建议查看[支持的 FIDO2 密钥供应商列表](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)，并向提供商咨询当前的 FIPS 验证状态。

* 智能卡是已证实的技术。 多个供应商产品满足 FIPS 要求。

   * 有关详细信息，请参阅[加密模块验证计划](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)页。

**Windows Hello for Business**

FIPS 140 要求包括软件、固件和硬件在内的整个加密边界都在评估范围之内。 Windows 操作系统是开放式计算平台，可以与数千个硬件组合配对。 Microsoft 无法为每个组合维护 FIPS 认证。 在将 Windows Hello 企业版用作 AAL3 验证器的风险评估中，应评估以下组件的各个认证：

* Windows 10 和 Windows Server 使用[经美国政府批准的常规用途操作系统 4.2.1 版保护配置文件](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442)，该配置文件由美国国家信息保障合作伙伴关系 (NIAP) 提供。 NIAP 负责监督国家/地区计划，以评估商用现货 (COTS) 信息技术 (IT) 产品是否符合国际通用标准。 

* Windows 加密库[在 NIST 加密模块验证计划 (CMVP) 中整体达到 FIPS 级别 1](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544)。 CMVP 由 NIST 与加拿大网络安全中心联合执行，旨在验证加密模块是否符合 FIPS 标准。 

* 所选择的受信任的平台模块 (TPM) 总体应达到 FIPS 140 级别 2，物理安全性应达到 FIPS 140 级别 3。 组织有责任确保所使用的硬件 TPM 满足自己希望实现的 AAL 级别的需求。   
若要确定符合当前标准的 TPM，请转到 [NIST 计算机安全资源中心加密模块验证计划](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search)页。 在“模块名称”框中，输入“受信任的平台模块”。 生成的列表中包含符合当前标准的硬件 TPM。

## <a name="reauthentication"></a>重新身份验证 

对于 AAL3 级别，NIST 要求无论用户是否处于活动状态，均需每 12 小时重新进行身份验证。 在持续 15 分钟或更长时间内处于非活动状态时也需要重新进行身份验证。 满足这两个因素，即需重新进行身份验证。

为了满足不考虑用户活动而重新进行身份验证的要求，Microsoft 建议将[用户登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)配置为 12 小时。 

NIST 还允许使用补偿控制来确认订阅者的状态：

* 可以通过在 OS 级别锁定设备，将会话非活动超时设置为 15 分钟。 可以通过使用 Microsoft Endpoint Configuration Manager、组策略对象 (GPO) 或 Intune 来执行此操作。 还需要进行本地身份验证，订阅者才能将其解锁。

* （利用 Configuration Manager、GPO 或 Intune）运行计划任务，在 12 小时后锁定计算机（无论用户是否处于活动状态），从而实现与活动状态无关的超时。

## <a name="man-in-the-middle-resistance"></a>抵御中间人攻击 

请求方和 Azure AD 之间的所有通信均通过经身份验证的受保护通道进行，以抵御中间人 (MitM) 攻击。 此配置满足 AAL1、AAL2 和 AAL3 对抵御 MitM 攻击的要求。

## <a name="verifier-impersonation-resistance"></a>验证程序模拟抵抗

所有满足 AAL3 的 Azure AD 身份验证方法均利用加密验证器，将验证器输出与正在进行身份验证的特定会话绑定。 这些验证器通过使用由请求方控制的私钥来执行上述操作，而其公钥已为验证程序所知。 此配置满足 AAL3 对验证程序模拟抵抗的要求。

## <a name="verifier-compromise-resistance"></a>验证程序泄露抵抗

所有满足 AAL3 的 Azure AD 身份验证方法均执行以下操作之一：
- 使用加密验证器（要求验证程序存储与验证器所持有的私钥相对应的公钥）。 
- 使用经 FIPS 140 验证的哈希算法存储所需的验证器输出。 

有关详细信息，请参阅 [Azure AD 数据安全注意事项](https://aka.ms/AADDataWhitepaper)。

## <a name="replay-resistance"></a>重放抵抗

所有满足 AAL3 的 Azure AD 身份验证方法均使用 nonce 或质询。 这些方法可以抵御重放攻击，因为验证程序可轻松检测重放的身份验证事务。 此类事务不包含相应的 nonce 或及时性数据。

## <a name="authentication-intent"></a>身份验证意向

身份验证意向旨在达成如下目标：在主体不知情的情况下，更加难以使用直接连接的物理验证器（如多重加密设备）。 例如，终结点上的恶意软件。

NIST 允许使用补偿控制以缓解恶意软件风险。 运行 Windows Defender 系统防护和 Windows Defender ATP 的所有 Intune 兼容设备都满足此风险缓解要求。

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)
