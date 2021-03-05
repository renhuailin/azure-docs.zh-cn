---
title: 浏览器对 FIDO2 无密码 authentication 的支持 |Azure Active Directory
description: 浏览器和操作系统组合支持使用 Azure Active Directory 的应用的 FIDO2 无密码 authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e5eb77a27a7c74ad9738a0e1784066a6a859e43
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175362"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 无密码 authentication 的浏览器支持

Azure Active Directory 允许将 [FIDO2 安全密钥](./concept-authentication-passwordless.md#fido2-security-keys) 用作无密码设备。 Microsoft 帐户的 FIDO2 authentication 的可用性已 [在2018中公布](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)。 如公告中所述，必须实现某些可选功能，以及 FIDO2 CTAP 规范的扩展，以支持与 Microsoft 和 Azure Active Directory 帐户的安全身份验证。 下图显示了哪些浏览器和操作系统组合支持将 FIDO2 authentication 密钥与 Azure Active Directory 结合使用的无密码 authentication。

## <a name="supported-browsers"></a>支持的浏览器

此表显示了对 Azure Active Directory (Azure AD) 和 Microsoft 帐户 (MSA) 进行身份验证的支持。 Microsoft 帐户由使用者为 Xbox、Skype 或 Outlook.com 等服务创建。 支持的设备类型包括 **USB**、近乎现场通信 (**NFC**) 和蓝牙低能耗 (**BLE**) 。

| (OS) | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![适用于 AAD 帐户的 Chrome 支持 Windows 上的 USB。][y] | ![Chrome 在 Windows 上支持 AAD 帐户的 NFC。][y] | ![Chrome 支持 Windows 上的 BLE 用于 AAD 帐户。][y] | ![边缘支持 AAD 帐户的 Windows 上的 USB。][y] | ![边缘支持 Windows 上的 NFC 帐户的 NFC。][y] | ![边缘支持适用于 AAD 帐户的 Windows 上的 BLE。][y] | ![Firefox 支持 AAD 帐户的 Windows 上的 USB。][y] | ![Firefox 支持在 Windows 上启用用于 AAD 帐户的 NFC。][y] | ![Firefox 支持 Windows 上的 BLE 用于 AAD 帐户。][y] |
| **macOS**  | ![Chrome 支持 AAD 帐户的 macOS 上的 USB。][y] | ![Chrome 在 macOS 上不支持 AAD 帐户的 NFC。][n] | ![Chrome 不支持 AAD 帐户的 macOS 上的 BLE。][n] | ![边缘支持 AAD 帐户的 macOS 上的 USB。][y] | ![Edge 不支持 AAD 帐户的 macOS 上的 NFC。][n] | ![Edge 不支持 AAD 帐户的 macOS 上的 BLE。][n] | ![Firefox 不支持 AAD 帐户的 macOS 上的 USB。][n] | ![在 macOS 上，Firefox 不支持 AAD 帐户的 NFC。][n] | ![Firefox 不支持 AAD 帐户的 macOS 上的 BLE。][n] |
| **Linux**  | ![Chrome 支持在 Linux 上为 AAD 帐户启用 USB。][y] | ![Chrome 不支持在 Linux 上用于 AAD 帐户的 NFC。][n] | ![Chrome 不支持 Linux 上的 BLE 用于 AAD 帐户。][n] | ![Edge 不支持在 Linux 上为 AAD 帐户启用 USB。][n] | ![Edge 不支持 Linux 上的 NFC 帐户的 NFC。][n] | ![对于 AAD 帐户，Edge 不支持 Linux 上的 BLE。][n] | ![Firefox 不支持在 Linux 上为 AAD 帐户启用 USB。][n] | ![在 Linux 上，Firefox 不支持 AAD 帐户的 NFC。][n] | ![在 Linux 上，Firefox 不支持 AAD 帐户的 BLE。][n] |



## <a name="unsupported-browsers"></a>不受支持的浏览器

以下操作系统和浏览器组合不受支持，但正在调查未来的支持和测试。 如果你想要查看其他操作系统和浏览器支持，请使用页面底部的产品反馈工具提供反馈。

| 操作系统 | 浏览者 |
| ---- | ---- |
| iOS | Safari，无畏 |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>最低浏览器版本

下面是最低浏览器版本要求。 

| 浏览者 | 最低版本 |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 版本 1903<sup>1</sup> |
| Firefox | Chrome |
| ChromeOS | 66 |

<sup>1</sup>所有版本的新的基于 Chromium 的 Microsoft Edge 支持 Fido2。 1903中增加了对 Microsoft Edge 旧版本的支持。

## <a name="next-steps"></a>后续步骤
[启用无密码安全密钥登录 (预览) ](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
