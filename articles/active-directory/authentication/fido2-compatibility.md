---
title: 浏览器对 FIDO2 无密码身份验证的支持 | Azure Active Directory
description: 浏览器和操作系统组合可以为使用 Azure Active Directory 的应用提供对 FIDO2 无密码身份验证的支持
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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632095"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>浏览器对 FIDO2 无密码身份验证的支持

Azure Active Directory 允许将 [FIDO2 安全密钥](./concept-authentication-passwordless.md#fido2-security-keys)用作无密码设备。 [2018 年已宣布](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)可在 Microsoft 帐户中使用 FIDO2 身份验证。 如公告中所述，必须实现某些可选功能以及 FIDO2 CTAP 规范的扩展才能在 Microsoft 和 Azure Active Directory 帐户中支持安全身份验证。 下图显示了哪个浏览器和操作系统组合支持结合使用 Azure Active Directory 和 FIDO2 身份验证密钥进行无密码身份验证。

## <a name="supported-browsers"></a>支持的浏览器

下表显示了对在 Azure Active Directory (Azure AD) 和 Microsoft 帐户 (MSA) 中进行身份验证的支持。 Microsoft 帐户是使用者为 Xbox、Skype 或 Outlook.com 等服务创建的。 支持的设备类型包括 USB、近场通信 (NFC) 和蓝牙低功耗 (BLE)  。

| (OS) | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome 支持 AAD 帐户的 Windows 上的 USB。][y] | ![Chrome 支持 AAD 帐户的 Windows 上的 NFC。][y] | ![Chrome 支持 AAD 帐户的 Windows 上的 BLE。][y] | ![Edge 支持 AAD 帐户的 Windows 上的 USB。][y] | ![Edge 支持 AAD 帐户的 Windows 上的 NFC。][y] | ![Edge 支持 AAD 帐户的 Windows 上的 BLE。][y] | ![Firefox 支持 AAD 帐户的 Windows 上的 USB。][y] | ![Firefox 支持 AAD 帐户的 Windows 上的 NFC。][y] | ![Firefox 支持 AAD 帐户的 Windows 上的 BLE。][y] |
| **macOS**  | ![Chrome 支持 AAD 帐户的 macOS 上的 USB。][y] | ![Chrome 不支持 AAD 帐户的 macOS 上的 NFC。][n] | ![Chrome 不支持 AAD 帐户的 macOS 上的 BLE。][n] | ![Edge 支持 AAD 帐户的 macOS 上的 USB。][y] | ![Edge 不支持 AAD 帐户的 macOS 上的 NFC。][n] | ![Edge 不支持 AAD 帐户的 macOS 上的 BLE。][n] | ![Firefox 不支持 AAD 帐户的 macOS 上的 USB。][n] | ![Firefox 不支持 AAD 帐户的 macOS 上的 NFC。][n] | ![Firefox 不支持 AAD 帐户的 macOS 上的 BLE。][n] |
| **Linux**  | ![Chrome 支持 AAD 帐户的 Linux 上的 USB。][y] | ![Chrome 不支持 AAD 帐户的 Linux 上的 NFC。][n] | ![Chrome 不支持 AAD 帐户的 Linux 上的 BLE。][n] | ![Edge 不支持 AAD 帐户的 Linux 上的 USB。][n] | ![Edge 不支持 AAD 帐户的 Linux 上的 NFC。][n] | ![Edge 不支持 AAD 帐户的 Linux 上的 BLE。][n] | ![Firefox 不支持 AAD 帐户的 Linux 上的 USB。][n] | ![Firefox 不支持 AAD 帐户的 Linux 上的 NFC。][n] | ![Firefox 不支持 AAD 帐户的 Linux 上的 BLE。][n] |



## <a name="unsupported-browsers"></a>不支持的浏览器

不支持以下操作系统和浏览器组合，但正在研究将来的支持和测试。 如果需要其他操作系统和浏览器支持，请使用页面底部的产品反馈工具提供反馈。

| 操作系统 | 浏览者 |
| ---- | ---- |
| iOS | Safari、Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>最低浏览器版本

以下是最低浏览器版本要求。 

| 浏览者 | 最低版本 |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 版本 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup>基于新 Chromium 的 Microsoft Edge 的所有版本均支持 Fido2。 1903 版中添加了对 Microsoft Edge 旧版的支持。

## <a name="next-steps"></a>后续步骤
[启用无密码安全密钥登录（预览版）](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
