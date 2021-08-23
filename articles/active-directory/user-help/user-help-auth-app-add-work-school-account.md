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
ms.date: 08/09/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 276590a295b59465184a9afa2f7ce5efea7bb8eb
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228700"
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
>现在可以登录 Microsoft Authenticator 应用并添加工作或学校帐户。

使用你的凭据登录到工作或学校帐户来添加帐户：

1. 打开 Microsoft Authenticator 应用并选择 + 按钮，然后点击“添加工作或学校帐户”。 选择“登录”。

1. 输入工作或学校帐户凭据。 如果拥有“临时访问密码”(TAP)，则可以使用该密码登录。 此时，可能会因为以下其中一种情形而被阻止继续后续操作：

   - 如果帐户的身份验证方法不足以获取强身份验证令牌，则无法继续添加帐户。

   - 如果你收到消息“`You might be signing in from a location that is restricted by your admin`”，则说明管理员尚未为你启用此功能，并且可能设置了安全信息注册条件访问策略。 若要使用此身份验证方法，请与工作或学校帐户的管理员联系。

1. 如果管理员允许你通过 Authenticator 应用来使用手机登录，则可以通过设备注册来设置无密码手机登录和 Azure AD 多重身份验证。 但是，无论是否启用了手机登录，你仍然能够设置多重身份验证。

1. 此时，系统可能会要求你扫描组织提供的 QR 码，以在应用中设置本地多重身份验证帐户。 仅当组织使用本地 MFA 服务器时，才需要进行此操作。

1. 在设备上，点击帐户，然后在全屏视图中验证帐户是否正确。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

## <a name="sign-in-with-a-qr-code"></a>使用 QR 码登录

若要通过扫描 QR 码来添加帐户，请进行以下操作：

1. 在计算机上，转到 **其他安全性验证** 页。

   >[!Note]
   >如果看不到“其他安全性验证”页，可能是管理员启用了安全信息（预览）体验。 如果是这种情况，应按照[设置安全信息以使用 Authenticator 应用](security-info-setup-auth-app.md)部分中的说明操作。 否则，请联系组织的技术支持来获得帮助。 有关安全信息的详细信息，请参阅[通过登录提示设置安全信息](security-info-setup-signin.md)。

1. 选中“Authenticator 应用”旁边的复选框，然后选择“配置”。 此时会显示“配置移动应用”页。

   ![提供 QR 码的屏幕](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. 打开 Microsoft Authenticator 应用，选择加号图标 ![选择 iOS 或 Android 设备上的加号图标](media/user-help-auth-app-add-work-school-account/plus-icon.png)，选择“添加帐户”，然后选择“工作或学校帐户”，最后选择“扫描 QR 码”。
   如果未在 Authenticator 应用中设置帐户，则会看到一个显示“添加帐户”的蓝色大按钮。

如果系统未提示使用相机来扫描 QR 码，请在手机的设置中确保 Authenticator 应用有权访问手机相机。 使用 QR 码添加帐户后，可以设置手机登录。 如果你收到消息“你可能正在从受管理员限制的位置登录”，则说明管理员尚未为你启用此功能，并且可能设置了安全信息注册条件访问策略。 若要使用此身份验证方法，请与工作或学校帐户的管理员联系。 如果管理员允许你通过 Authenticator 应用来使用手机登录，则可以通过设备注册来设置无密码手机登录和 Azure AD 多重身份验证。

>[!Note]
> 对于美国政府组织，添加手机登录帐户的唯一方法就是使用[使用凭据登录](#sign-in-with-your-credentials)选项添加该帐户，而不是从基于 QR 码的帐户升级。

## <a name="sign-in-on-a-remote-computer"></a>在远程计算机上登录

许多应用允许在其他设备（例如电脑）上通过输入代码进行身份验证。 如果要在远程计算机上登录并安装 Microsoft Authenticator 应用，请执行以下操作：

1. 打开 Microsoft Authenticator 应用并选择 + 按钮，然后依次单击“添加工作或学校帐户”和“登录”&gt;&gt;  。
1. 选择“从另一台设备登录”。
1. 在远程屏幕上，打开[“登录帐户”页面](https://microsoft.com/devicelogin)，然后输入 Authenticator 应用中显示的代码。
1. 在远程屏幕上，使用工作或学校帐户凭据登录。 如果拥有“临时访问密码”(TAP)，则可以使用该密码登录。
1. 在远程屏幕上完成身份验证后，返回到 Authenticator 应用，完成设置。

 ## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
