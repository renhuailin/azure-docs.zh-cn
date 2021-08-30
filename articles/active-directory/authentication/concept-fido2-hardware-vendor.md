---
title: 成为与 Microsoft 兼容的 FIDO2 安全密钥供应商，以便登录 Azure AD
description: 说明成为 FIDO2 硬件合作伙伴的过程
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778449"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>成为与 Microsoft 兼容的 FIDO2 安全密钥供应商

大多数与黑客攻击相关的违规都使用盗取的密码或弱密码。 通常，IT 部门会强制实施更强的密码复杂性或频繁的密码更改，以降低安全事件的风险。 但是，这会增加技术支持成本，并导致用户体验不佳，因为用户需要记住或存储新的复杂密码。

FIDO2 安全密钥提供了替代方案。 FIDO2 安全密钥可以将弱凭据替换为硬件提供支持的公钥/私钥强凭据，这些凭据不能在服务之间重复使用、重播或共享。 安全密钥支持共享设备方案，使你可以携带凭据，并安全地在组织中加入 Azure Active Directory 的 Windows 10 设备上进行身份验证。

Microsoft 与 FIDO2 安全密钥供应商合作，确保安全设备适用于 Windows、Microsoft Edge 浏览器和联机 Microsoft 帐户，以实现无密码强身份验证。

可以通过以下流程成为与 Microsoft 兼容的 FIDO2 安全密钥供应商。  Microsoft 不承诺与合作伙伴联合开展市场推广活动，并且会根据客户需求评估合作伙伴优先级。

1. 首先，你的验证器需要通过 FIDO2 认证。 我们将无法与未通过 FIDO2 认证的提供商合作。 若要详细了解认证，请访问此网站：[https://fidoalliance.org/certification/](https://fidoalliance.org/certification/)
2. 通过 FIDO2 认证后，请在此处的表单中填写请求：[https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF)。 我们的工程团队只会测试 FIDO2 设备的兼容性。 我们不会测试你的解决方案的安全性。
3. 我们确认可以前进到测试阶段后，此流程通常需要大约 3 到 6 个月才能完成。 步骤通常包括：
    - Microsoft 和你的团队进行初步讨论。
        - 验证 FIDO 联盟认证或认证路径（如果未完成）
        - 从供应商接收设备概述
    - Microsoft 将与你共享我们的测试脚本。 如果你有任何特定需求，我们的工程团队将能回答问题。
    - 你将完成所有通过的结果并将其发送给 Microsoft 工程团队
    - Microsoft 确认后，你将每个设备的多个硬件/解决方案示例发送给 Microsoft 工程团队
        - 收到后，Microsoft 工程团队将执行测试脚本验证和用户体验流程
4. Microsoft 工程团队成功通过了所有测试后，Microsoft 将确认供应商的设备已在 [FIDO MDS](https://fidoalliance.org/metadata/) 中列出。
5. Microsoft 会在 Azure AD 后端将你的 FIDO2 安全密钥添加到我们批准的 FIDO2 供应商列表。

## <a name="current-partners"></a>当前合作伙伴

下表列出了与 Microsoft 兼容的 FIDO2 安全密钥供应商合作伙伴。

| **提供程序** | **链接**。 |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2 Switzerland | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| TrustKey Solutions | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>后续步骤

[FIDO2 兼容性](fido2-compatibility.md)

