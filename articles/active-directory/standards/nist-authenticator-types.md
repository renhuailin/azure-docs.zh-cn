---
title: NIST 验证器类型和相应的 Azure Active Directory 方法
description: 说明 Azure Active Directory 身份验证方法如何与 NIST 验证器类型保持一致。
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
ms.openlocfilehash: 113dddb371faab784e96acc2076180118af7cfd8
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293938"
---
# <a name="nist-authenticator-types-and-aligned-azure-active-directory-methods"></a>NIST 验证器类型和相应的 Azure Active Directory 方法

当请求方声称其控制了与订阅者关联的一个或多个验证器时，会开始身份验证过程。 订阅者可以是人员，也可以是其他实体。

| NIST 验证器类型| Azure AD 身份验证方法 |
| - | - |
|  记住的密码 <br> （已知内容）|  密码（云帐户）  <br>密码（联合）<br> 密码（密码哈希同步）<br>密码（直通身份验证） |
|查找机密 <br> （已有内容）| 无。 根据定义，查找秘密是未保存在系统中的数据。 |
|带外 <br>（已有内容）| 电话 (SMS) - 不建议 |
| 单因素一次性密码 <br>（已有内容）| Microsoft Authenticator 应用（一次性密码）  <br>单因素一次性密码（通过 OTP 制造商）<sup data-htmlnode="">1</sup> | 
| 多因素一次性密码<br>（已有内容 + 已知内容或表明身份的内容）| 多因素一次性密码（通过 OTP 制造商）<sup data-htmlnode="">1</sup>| 
|单因素加密软件<br>（已有内容）|兼容的移动设备 <br> Microsoft Authenticator 应用（通知） <br> 带软件 TPM 的混合 Azure AD 联接<sup data-htmlnode="">2</sup><br> 带软件 TPM 的 Azure AD 联接<sup data-htmlnode="">2</sup> |
| 单因素加密硬件 <br>（已有内容） | 带硬件 TPM 的 Azure AD 联接<sup data-htmlnode="">2</sup> <br> 带硬件 TPM 的混合 Azure AD 联接<sup data-htmlnode="">2</sup>|
|多重加密软件<br>（已有内容 + 已知内容或表明身份的内容） | 适用于 iOS 的 Microsoft Authenticator 应用（无密码）<br> 带软件 TPM 的 Windows Hello 企业版 |
|多重加密硬件 <br>（已有内容 + 已知内容或表明身份的内容） |适用于 Android 的 Microsoft Authenticator 应用（无密码）<br> 带硬件 TPM 的 Windows Hello 企业版<br> 智能卡（联合标识提供程序） <br> FIDO 2 安全密钥 |


<sup data-htmlnode="">1</sup> 30 秒或 60 秒的 OATH-TOTP SHA-1 令牌。

<sup data-htmlnode="">2</sup> 有关设备联接状态的详细信息，请参阅 [Azure AD 设备标识文档](https://docs.microsoft.com/azure/active-directory/devices/)。 

## <a name="why-sms-isnt-recommended"></a>不建议使用 SMS 的原因 

SMS 短信虽符合 NIST 标准，但 NIST 并不建议使用。 设备交换、SIM 更改、数字移植和其他行为所面临的风险可能导致问题。 如果恶意执行这些操作，则可能会导致不安全的体验。 尽管不建议使用短信，但它们比单独使用密码要好，因为它们需要黑客付出更多的精力。 

## <a name="next-steps"></a>后续步骤 

[NIST 概述](nist-overview.md)

[了解 AAL](nist-about-authenticator-assurance-levels.md)

[身份验证基础知识](nist-authentication-basics.md)

[NIST 验证器类型](nist-authenticator-types.md)

[使用 Azure AD 满足 NIST AAL1 要求](nist-authenticator-assurance-level-1.md)

[使用 Azure AD 满足 NIST AAL2 要求](nist-authenticator-assurance-level-2.md)

[使用 Azure AD 满足 NIST AAL3 要求](nist-authenticator-assurance-level-3.md) 
