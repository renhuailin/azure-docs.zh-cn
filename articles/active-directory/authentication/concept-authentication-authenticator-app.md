---
title: Microsoft Authenticator 应用身份验证方法 - Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用 Microsoft Authenticator 应用来帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45663b7a2229de0600e60ec2e40c5b721440b997
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738932"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Azure Active Directory 中的身份验证方法 - Microsoft Authenticator 应用

Microsoft Authenticator 应用为 Azure AD 工作或学校帐户或 Microsoft 帐户提供附加的安全级别，并可用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594) 和 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)。 使用 Microsoft Authenticator 应用，用户可以在登录期间以无密码方式进行身份验证，或在自助式密码重置 (SSPR) 或 Azure AD 多重身份验证事件期间将这种方式作为附加的身份验证选项使用。

用户可能会通过移动应用收到通知，并在其中批准或拒绝，或使用 Authenticator 应用生成可在登录界面中输入的 OATH 验证码。 如果同时启用了通知和验证码，注册 Authenticator 应用的用户可以使用任一方法验证其身份。

若想在出现登录提示时使用 Authenticator 应用而不是用户名和密码的组合，请参阅[启用使用 Microsoft Authenticator 应用进行无密码登录](howto-authentication-passwordless-phone.md)。

> [!NOTE]
> 用户启用 SSPR 后不会得到用于注册其移动应用的选项。 而用户可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或通过 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的组合的安全信息注册过程注册他们的移动应用。

## <a name="passwordless-sign-in"></a>无密码登录

在 Microsoft Authenticator 应用中启用手机登录的用户在输入用户名后，看到的不是密码提示，而是一条消息，让他们在应用中点击一个数字。 如果选择了正确的数字，则登录过程完成。

![要求用户批准登录的浏览器登录示例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

此身份验证方法提供的安全级别高，并且无需用户在登录时提供密码。 

若要开始使用无密码登录，请参阅[启用使用 Microsoft Authenticator 应用进行无密码登录](howto-authentication-passwordless-phone.md)。

## <a name="notification-through-mobile-app"></a>通过移动应用发送通知

Authenticator 应用通过将通知推送到智能手机或平板电脑，可帮助防止对帐户进行未经授权的访问，以及停止欺诈性交易。 用户将查看通知，如果信息合法，则选择“验证”。 否则，可以选择“拒绝”。

![Web 浏览器提示示例的屏幕截图，其中提示用户处理 Authenticator 应用通知以完成登录过程](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 如果你的组织有员工在中国工作或要前往中国，则 Android 设备上的移动应用通知方法在该国家/地区不起作用，因为 Google Play 服务（包括推送通知）在该区域被阻止。 但 iOS 通知可以正常工作。 对于 Android 设备，应向这些用户提供备用身份验证方法。

## <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

Authenticator 应用可用作生成 OATH 验证码所需的软件令牌。 输入用户名和密码后，在登录界面中输入 Authenticator 应用提供的代码。 验证码提供了第二种形式的身份验证。

用户可以具有最多 5 个 OATH 硬件令牌或验证器应用程序（如配置为可随时使用的 Microsoft Authenticator 应用）的组合。

> [!WARNING]
> 对于自助密码重置，如果只需使用一种方法来执行重置，则验证码是可供用户用于确保最高级别安全性的唯一选项。
>
> 如果需要两种方法，则用户可以使用通知或验证码进行重置，此外还能使用其他任何已启用的方法。

## <a name="next-steps"></a>后续步骤

若要开始使用无密码登录，请参阅[启用使用 Microsoft Authenticator 应用进行无密码登录](howto-authentication-passwordless-phone.md)。

详细了解如何使用 [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview) 配置身份验证方法。
