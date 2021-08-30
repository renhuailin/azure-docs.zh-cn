---
title: Azure Active Directory 无密码登录
description: 了解使用 FIDO2 安全密钥或 Microsoft Authenticator 应用进行 Azure Active Directory 无密码登录的选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 115f792c82f2e3eea42636c788c3461f7cc55f77
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014604"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory 的无密码身份验证选项

多重身份验证 (MFA) 等功能是保护组织的一种好方法，但用户通常对必须记住密码之外的其他安全保护感到沮丧。 无密码身份验证方法更为方便，因为密码会被删除并替换为你指定的内容，以及你自己的或你已知的内容。

| 身份验证  | 已有内容 | 你自己的或你已知的内容 |
| --- | --- | --- |
| 无密码 | Windows 10 设备、电话或安全密钥 | 生物识别或 PIN |

当涉及身份验证时，每个组织都有不同的需求。 Microsoft 全球 Azure 和 Azure 政府提供了下面 3 个与 Azure Active Directory (Azure AD) 集成的无密码身份验证选项：

- Windows Hello 企业版
- Microsoft Authenticator 应用
- FIDO2 安全密钥

![身份验证：安全性和便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企业版

Windows Hello 企业版非常适合拥有自己的指定 Windows 电脑的信息工作者。 生物识别和 PIN 凭据直接绑定到用户的电脑，可阻止除所有者之外的任何人访问。 利用公钥基础结构 (PKI) 集成和内置单一登录 (SSO) 支持，Windows Hello 企业版提供了一种方便的方法，可无缝访问本地和云中的公司资源。

![用户登录 Windows Hello 企业版的示例](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

以下步骤演示了使用 Azure AD 进行登录的过程：

![概述用户登录 Windows Hello 企业版所涉及的步骤的示意图](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 用户使用生物识别或 PIN 手势登录 Windows。 手势会解除对 Windows Hello 企业版私钥的锁定，并发送到云身份验证安全支持提供商（称为“云 AP 提供商”）。
1. 云 AP 提供商从 Azure AD 请求一个 nonce（一个随机的任意数字，只能使用一次）。
1. Azure AD 将返回有效期为 5 分钟的 nonce。
1. 云 AP 提供商使用用户的私钥对 nonce 进行签名，然后将签名的 nonce 返回给 Azure AD。
1. Azure AD 使用用户安全注册的公钥对 nonce 签名进行签名来验证已签名的 nonce。 验证签名后，Azure AD 随后验证返回的签名 nonce。 验证 nonce 后，Azure AD 将使用对设备传输密钥进行加密的会话密钥 (PRT) 创建主刷新令牌，并将其返回给云 AP 提供商。
1. 云 AP 提供商接收使用会话密钥加密的 PRT。 云 AP 提供商使用设备的专用传输密钥解密会话密钥，并使用设备的受信任的平台模块 (TPM) 来保护会话密钥。
1. 云 AP 提供商向 Windows 返回成功的身份验证响应。 然后，用户可以访问 Windows 以及云和本地应用程序，而无需再次进行身份验证 (SSO)。

Windows Hello 企业版[规划指南](/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用于帮助你决定 Windows Hello 企业版部署的类型以及需要考虑的选项。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

你还可以使用员工的手机作为无密码身份验证方法。 除密码外，你可能已使用 Microsoft Authenticator 应用作为便利的多重身份验证选项。 你还可以使用 Authenticator 应用作为无密码选项。

![通过 Microsoft Authenticator 应用登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator 应用可将任何 iOS 或 Android 手机变成强、无密码凭据。 用户可以通过以下方式登录到任何平台或浏览器：向其手机发送通知，将屏幕上显示的数字与其手机上的相应数字匹配，然后使用其生物识别（触摸或人脸）或 PIN 进行确认。 有关安装的详细信息，请参阅[下载并安装 Microsoft Authenticator 应用](../user-help/user-help-auth-app-download-install.md)。

使用 Authenticator 应用的无密码身份验证遵循与 Windows Hello 企业版相同的基本模式。 这会稍微复杂一些，因为需要识别用户，以便 Azure AD 可以找到正在使用的 Microsoft Authenticator 应用版本：

![概述用户通过 Microsoft Authenticator 应用登录所涉及的步骤的示意图](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 用户输入其用户名。
1. Azure AD 检测到用户具有强凭据并启动强凭据流。
1. 通过 iOS 设备上的 Apple Push Notification 服务 (APNS)，或通过 Android 设备上的 Firebase Cloud Messaging (FCM) 向应用发送通知。
1. 用户收到推送通知并打开应用。
1. 应用调用 Azure AD，并收到一个存在证据质询和 nonce。
1. 用户通过输入生物识别或 PIN 以解锁私钥来完成质询。
1. Nonce 使用私钥签名后发送回 Azure AD。
1. Azure AD 执行公钥/私钥验证并返回令牌。

若要开始使用无密码登录，请完成以下操作方法：

> [!div class="nextstepaction"]
> [使用 Authenticator 应用启用无密码签名](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO（线上快速身份验证）联盟可帮助推广开放式身份验证标准，并减少将密码作为用户身份验证方式的情况。 FIDO2 是采用了 Web 身份验证 (WebAuthn) 标准的最新标准。

FIDO2 安全密钥是无法钓鱼的基于标准的无密码身份验证方法，可采用任何外形规格。 线上快速身份验证 (FIDO) 是无密码身份验证的开放式标准。 通过 FIDO，用户和组织可以利用该标准使用外部安全密钥或内置于设备的平台密钥登录到其资源，而无需使用用户名或密码。

用户可以进行注册，然后在登录界面选择 FIDO2 安全密钥作为主要的身份验证方式。 这些 FIDO2 安全密钥通常是 USB 设备，但也可以使用蓝牙或 NFC。 使用处理身份验证的硬件设备，由于不使用可能被公开或猜到的密码，帐户的安全性会提高。

FIDO2 安全密钥可用于登录到其 Azure AD 或已建立混合 Azure AD 联接的 Windows 10 设备，并可单一登录到其云和本地资源。 用户还可以登录到受支持的浏览器。 对于安全敏感的企业而言，或者如果企业有些员工不愿意或有些场景无法使用其手机作为第二种验证方法，FIDO2 安全密钥是一个不错的选择。

我们提供了一个参考文档，其中[浏览器支持使用 Azure AD 进行 FIDO2 身份验证](fido2-compatibility.md)，还提供了最佳做法，适用于希望[在其开发的应用程序中支持 FIDO2 身份验证](../develop/support-fido2-authentication.md)的开发人员采用。

![使用安全密钥登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

用户使用 FIDO2 安全密钥登录时，将使用以下过程：

![概述用户使用 FIDO2 安全密钥登录所涉及的步骤的示意图](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 用户将 FIDO2 安全密钥插入到计算机中。
2. Windows 检测 FIDO2 安全密钥。
3. Windows 发送身份验证请求。
4. Azure AD 发回 nonce。
5. 用户完成其手势以解锁存储在 FIDO2 安全密钥的安全 enclave 中的私钥。
6. FIDO2 安全密钥用私钥对 nonce 进行签名。
7. 向 Azure AD 发送带签名 nonce 的主刷新令牌(PRT) 令牌请求。
8. Azure AD 使用 FIDO2 公钥验证已签名的 nonce。
9. Azure AD 返回 PRT 以启用对本地资源的访问。

尽管有很多密钥都是通过 FIDO 联盟认证的 FIDO2，但 Microsoft 还是需要由供应商实现的 FIDO2 客户端到身份验证器协议 (Client-to-Authenticator Protocol, CTAP) 的一些可选扩展规范，以确保最高的安全性和最佳体验。

安全密钥必须实现 FIDO2 CTAP 协议中的以下功能和扩展，才能与 Microsoft 兼容。 验证器供应商必须同时实现规范的 FIDO_2_0 和 FIDO_2_1 版本。有关详细信息，请参阅[客户端到身份验证器协议](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html)。

| # | 功能/扩展信任 | 为什么需要此功能或扩展？ |
| --- | --- | --- |
| 1 | 常驻/可发现密钥 | 此功能使安全密钥可移植，其中凭据存储在安全密钥中并且是可发现的，这使得无用户名流成为可能。 |
| 2 | 客户端 Pin | 利用此功能，你可以使用另一个因素来保护凭据，并将其应用于没有用户界面的安全密钥。<br>必须同时实现 [PIN 协议 1](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#pinProto1) 和 [PIN 协议 2](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#pinProto2)。 |
| 3 | hmac-secret | 此扩展可确保你可以在设备处于离线状态或处于飞行模式时登录到你的设备。 |
| 4 | 每个 RP 多个帐户 | 此功能可确保你可以在多个服务（如 Microsoft 帐户和 Azure Active Directory）上使用相同的安全密钥。 |
| 5 | 凭据管理    | 此功能允许用户在平台上管理安全密钥的凭据，适用于未内置此功能的安全密钥。<br>Authenticator 必须为此功能实现 [authenticatorCredentialManagement](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#authenticatorCredentialManagement) 和 [credentialMgmtPreview](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#prototypeAuthenticatorCredentialManagement) 命令。  |
| 6 | 生物识别注册           | 此功能允许用户在验证器上注册生物识别，适用于未内置此功能的安全密钥。<br> Authenticator 必须为此功能实现 [authenicatorBioEnrollment](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#authenticatorBioEnrollment) 和 [userVerificationMgmtPreview](https://fidoalliance.org/specs/fido-v2.1-ps-20210615/fido-client-to-authenticator-protocol-v2.1-ps-20210615.html#prototypeAuthenticatorBioEnrollment) 命令。 |
| 7 | pinUvAuthToken           | 此功能允许平台使用 PIN 或生物识别匹配获得身份验证令牌，这有助于在验证器上存在多个凭据时提供更好的用户体验。  |
| 8 | forcePinChange           | 此功能允许企业要求用户在远程部署中更改其 PIN。  |
| 9 | setMinPINLength          | 此功能允许企业为其用户自定义最小 PIN 长度。 Authenticator 必须实现 minPinLength 扩展，并且 maxRPIDsForSetMinPINLength 的值至少为 1。  |
| 10 | alwaysUV                | 此功能允许企业或用户始终要求用户进行验证才能使用此安全密钥。 验证器必须实现 toggleAlwaysUv 子命令。 由供应商决定 alwaysUV 的默认值。 目前，由于采用各种 RP 和 OS 版本的性质，基于生物识别的验证器的建议值为 true，而基于非生物识别的验证器值为 false。  |
| 11 | credBlob                | 此扩展允许网站在安全密钥中存储小型信息。 maxCredBlobLength 必须至少为 32 字节。  |
| 12 | largeBlob               | 此扩展允许网站在安全密钥中存储证书等大型信息。 maxSerializedLargeBlobArray 必须至少为 1024 字节。  |


### <a name="fido2-security-key-providers"></a>FIDO2 安全密钥提供商

以下提供商提供了 FIDO2 安全密钥，它们具有已知兼容无密码体验的不同外形规格。 建议你通过联系供应商以及 FIDO 联盟来评估这些密钥的安全属性。

| 提供程序                  |     生物识别     | USB | NFC | BLE | FIPS 认证 | 联系人                                                                                             |
|---------------------------|:-----------------:|:---:|:---:|:---:|:--------------:|-----------------------------------------------------------------------------------------------------|
| AuthenTrend               | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://authentrend.com/about-us/#pg-35-3                                                           |
| Ensurity                  | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.ensurity.com/contact                                                                    |
| Excelsecu                 | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://www.excelsecu.com/productdetail/esecufido2secu.html                                         |
| Feitian                   | ![y]              | ![y]| ![y]| ![y]| ![y]           | https://shop.ftsafe.us/pages/microsoft                                                              |
| GoTrustID Inc.            | ![n]              | ![y]| ![y]| ![y]| ![n]           | https://www.gotrustid.com/idem-key                                                                  |
| HID                       | ![n]              | ![y]| ![y]| ![n]| ![n]           | https://www.hidglobal.com/contact-us                                                                |
| Hypersecu                 | ![n]              | ![y]| ![n]| ![n]| ![n]           | https://www.hypersecu.com/hyperfido                                                                 |
| IDmelon Technologies Inc. | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://www.idmelon.com/#idmelon                                                                    |
| Kensington                | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.kensington.com/solutions/product-category/why-biometrics/                               |
| KONA I                    | ![y]              | ![n]| ![y]| ![y]| ![n]           | https://konai.com/business/security/fido                                                            |
| Nymi                      | ![y]              | ![n]| ![y]| ![n]| ![n]           | https://www.nymi.com/product                                                                      | 
| OneSpan Inc.              | ![y]              | ![n]| ![n]| ![y]| ![n]           | https://www.onespan.com/products/fido                                                               |
| Thales Group              | ![n]              | ![y]| ![y]| ![n]| ![n]           | https://cpl.thalesgroup.com/access-management/authenticators/fido-devices                           |
| Thetis                    | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://thetis.io/collections/fido2                                                                 |
| Token2 Switzerland        | ![y]              | ![y]| ![y]| ![n]| ![n]           | https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key               |
| TrustKey Solutions        | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.trustkeysolutions.com/security-keys/                                                    |
| VinCSS                    | ![n]              | ![y]| ![n]| ![n]| ![n]           | https://passwordless.vincss.net                                                                     |
| Yubico                    | ![n]              | ![y]| ![y]| ![n]| ![y]           | https://www.yubico.com/solutions/passwordless/                                                      |


<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png

> [!NOTE]
> 如果你购买并计划使用基于 NFC 的安全密钥，则需要为安全密钥提供支持的 NFC 读卡器。 Azure 未要求或限制使用 NFC 读卡器。 有关支持的 NFC 读卡器的列表，请与供应商联系以获取基于 NFC 的安全密钥。

如果你是供应商，并且希望你的设备列入此受支持设备列表，请查看有关如何[成为与 Microsoft 兼容的 FIDO2 安全密钥供应商](concept-fido2-hardware-vendor.md)的指导。

若要开始使用 FIDO2 安全密钥，请完成以下操作方法：

> [!div class="nextstepaction"]
> [使用 FIDO2 安全密钥启用无密码签名](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>支持的方案

请注意以下事项：

- 管理员可以为其租户启用无密码身份验证方法。

- 对于每种方法，管理员都可面向所有用户或选择其租户中的用户/组。

- 用户可以在其帐户门户中注册和管理这些无密码身份验证方法。

- 用户可以通过以下无密码身份验证方法登录：
   - Microsoft Authenticator 应用：适用于使用 Azure AD 身份验证的情况，包括跨所有浏览器、在 Windows 10 安装期间以及与任何操作系统上集成的移动应用使用。
   - 安全密钥：适用于 Windows 10 的锁屏界面和受支持的浏览器（如旧版和新版 Microsoft Edge）中的 Web。

- 用户可以使用无密码凭据访问他们在其中为来宾身份的租户中的资源，但仍可能需要在该资源租户中执行 MFA。 有关详细信息，请参阅[可能需要进行两次多重身份验证](../external-identities/current-limitations.md#possible-double-multi-factor-authentication)。  

- 用户不得在他们为来宾身份的租户中注册无密码凭据，正如他们不能在该租户中管理密码一样。  


## <a name="choose-a-passwordless-method"></a>选择无密码方法

这三个无密码选项之间的选择取决于公司的安全要求、平台要求和应用要求。

下面是在选择 Microsoft 无密码技术时要考虑的一些因素：

||**Windows Hello for Business**|**使用 Microsoft Authenticator 应用进行无密码登录**|**FIDO2 安全密钥**|
|:-|:-|:-|:-|
|**先决条件**| Windows 10 版本 1809 或更高版本<br>Azure Active Directory| Microsoft Authenticator 应用<br>手机（运行 Android 6.0 或更高版本的 iOS 和 Android 设备。）|Windows 10 版本 1903 或更高版本<br>Azure Active Directory|
|**模式**|平台|软件|硬件|
|**系统和设备**|带有内置受信任的平台模块 (TPM) 的电脑<br>PIN 和生物识别 |手机上的 PIN 和生物识别|与 Microsoft 兼容的 FIDO2 安全设备|
|**用户体验**|在 Windows 设备中使用 PIN 或生物识别（人脸、可变光圈或指纹）登录。<br>Windows Hello 身份验证已绑定到设备；用户需要设备和登录组件（如 PIN 或生物识别因素）才能访问公司资源。|使用带有指纹扫描、人脸或可变光圈识别或 PIN 的移动电话登录。<br>用户从他们的电脑或移动电话登录到工作帐户或个人帐户。|使用 FIDO2 安全设备（生物识别、PIN 和 NFC）登录<br>用户可以基于组织控制访问设备并使用设备（如 USB 安全密钥和启用了 NFC 的智能卡、密钥或可穿戴设备）基于 PIN 和生物识别进行身份验证。|
|**支持的方案**| Windows 设备的无密码体验。<br>适用于专用的工作电脑，可单一登录到设备和应用程序。|使用移动电话的无密码解决方案。<br>适用于从任何设备访问 Web 上的工作或个人应用程序。|使用生物识别、PIN 和 NFC 的辅助角色的无密码体验。<br>适用于共享电脑以及无法使用移动电话的情况（例如，咨询台人员、公共展台或医院团队限制使用移动电话的情况）|

使用下表选择支持你的需求和用户的方法。

|角色|场景|环境|无密码技术|
|:-|:-|:-|:-|
|**管理员**|安全访问设备以执行管理任务|已分配 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**管理员**|非 Windows 设备上的管理任务| 移动或非 Windows 设备|使用 Microsoft Authenticator 应用进行无密码登录|
|**信息工作者**|高效工作|已分配 Windows 10 设备|Windows Hello 企业版和/或 FIDO2 安全密钥|
|**信息工作者**|高效工作| 移动或非 Windows 设备|使用 Microsoft Authenticator 应用进行无密码登录|
|**一线工作者**|工厂、车间、零售或数据录入中的展台|共享 Windows 10 设备|FIDO2 安全密钥|

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure AD 中的无密码，请完成以下操作方法之一：

* [启用 FIDO2 安全密钥无密码登录](howto-authentication-passwordless-security-key.md)
* [使用 Authenticator 应用启用基于手机的无密码登录](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部链接

* [FIDO 联盟](https://fidoalliance.org/)
* [FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
