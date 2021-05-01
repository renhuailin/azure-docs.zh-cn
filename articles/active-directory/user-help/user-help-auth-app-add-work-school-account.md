---
title: 将工作或学校帐户添加到 Microsoft Authenticator 应用 - Azure AD
description: 将工作或学校帐户添加到 Microsoft Authenticator 应用，以便在使用双重验证时验证你的身份。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359094"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>将工作或学校帐户添加到 Microsoft Authenticator 应用

如果组织使用双重验证，可以设置工作或学校帐户，以使用 Microsoft Authenticator 应用作为验证方法之一。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

## <a name="add-your-work-or-school-account"></a>添加工作或学校帐户

通过下列其中一项操作将工作或学校帐户添加到 Microsoft Authenticator 应用：

- 使用工作或学校帐户凭据登录（预览版）
- 扫描 QR 码

### <a name="sign-in-with-your-credentials"></a>使用凭据登录

>[!Note]
>只有管理员使用 Authenticator 应用为其启用了手机登录的用户才可以使用此功能。

使用你的凭据登录到工作或学校帐户来添加帐户：

1. 打开 Microsoft Authenticator 应用并选择 + 按钮，然后点击“添加工作或学校帐户”。 选择“登录”。

1. 输入工作或学校帐户凭据。 如果拥有“临时访问密码”(TAP)，则可以使用该密码登录。 此时，可能会因为以下其中一种情形而被阻止继续后续操作：

   - 如果帐户的身份验证方法不足以获取强身份验证令牌，则将无法继续添加帐户。

   - 如果收到消息 `You might be signing in from a location that is restricted by your admin`，则会被阻止，需要管理员在[安全信息](https://mysignins.microsoft.com/security-info)中解除阻止。

   - 如果管理员未阻止你使用 Authenticator 应用进行手机登录，则可以进行设备注册来设置无密码手机登录和 Azure 多重身份验证 (MFA)。

1. 此时，系统可能会要求扫描组织提供的 QR 代码，以在应用中设置本地多重身份验证帐户。 仅当组织使用本地 MFA 服务器时，才需要进行此操作。

1. 在设备上，点击帐户，然后在全屏视图中验证帐户是否正确，以及是否有关联的六位数验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

## <a name="sign-in-with-a-qr-code"></a>使用 QR 码登录

若要通过扫描 QR 码来添加帐户，请进行以下操作：

1. 在计算机上，转到 **其他安全性验证** 页。

   >[!Note]
   >如果看不到“其他安全性验证”页，可能是管理员启用了安全信息（预览）体验。 如果是这种情况，应按照[设置安全信息以使用 Authenticator 应用](security-info-setup-auth-app.md)部分中的说明操作。 否则，请联系组织的技术支持来获得帮助。 有关安全信息的详细信息，请参阅[通过登录提示设置安全信息](security-info-setup-signin.md)。

1. 选中“Authenticator 应用”旁边的复选框，然后选择“配置”。 此时会显示“配置移动应用”页。

   ![提供 QR 码的屏幕](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. 打开 Microsoft Authenticator 应用，选择加号图标 ![选择 iOS 或 Android 设备上的加号图标](media/user-help-auth-app-add-work-school-account/plus-icon.png)，选择“添加帐户”，然后选择“工作或学校帐户”，最后选择“扫描 QR 码”。
   如果未在 Authenticator 应用中设置帐户，则会看到一个显示“添加帐户”的蓝色大按钮。

如果系统未提示使用相机来扫描 QR 码，请在手机的设置中确保 Authenticator 应用有权访问手机相机。

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
