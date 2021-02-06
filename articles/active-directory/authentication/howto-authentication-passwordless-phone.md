---
title: 无密码登录 Microsoft Authenticator 应用-Azure Active Directory
description: 使用 Microsoft Authenticator 应用 () 预览启用无密码登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62dea67f139857befc7ca24b8b8a105241b5c949
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626149"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>使用 Microsoft Authenticator 应用 (预览版启用无密码登录) 

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备（设备使用 PIN 或生物识别）的用户凭据。 [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 使用类似的技术。

此身份验证技术可用于任何设备平台，包括 mobile。 此技术还可用于与 Microsoft 身份验证库集成的任何应用或网站。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="浏览器登录示例：请求用户批准登录。":::

在 Microsoft Authenticator 应用程序中启用手机登录的用户将看到一条消息，要求他们点击其应用中的某个数字。 不需要用户名或密码。 若要完成应用中的登录过程，用户必须先执行以下操作：

1. 匹配数字。
2. 选择“批准”。
3. 提供其 PIN 或生物识别。

## <a name="prerequisites"></a>先决条件

若要将无密码 phone 登录与 Microsoft Authenticator 应用一起使用，必须满足以下先决条件：

- Azure AD 多重身份验证，允许将推送通知作为验证方法。
- 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。

> [!NOTE]
> 如果你使用 Azure AD PowerShell 启用 Microsoft Authenticator 无密码登录预览，则会为你的整个目录启用此功能。 如果使用此新方法启用，则它将取代 PowerShell 策略。 建议你通过新的 *身份验证方法* 菜单为租户中的所有用户启用，否则不在新策略中的用户将无法在没有密码的情况下进行登录。

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

若要在 Azure AD 中使用无密码 authentication，首先要启用合并的注册体验，并使用户能够使用密码更少的方法。

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合注册功能。 若要让用户自行完成组合注册，请按照步骤 [启用组合的安全信息注册](howto-registration-mfa-sspr-combined.md)。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>启用无密码手机登录身份验证方法

Azure AD 允许选择在登录过程中可使用的身份验证方法。 然后，用户注册他们想要使用的方法。

若要为无密码 phone 登录启用身份验证方法，请完成以下步骤：

1. 使用 *全局管理员* 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 *Azure Active Directory*，然后浏览到 **安全**  >  **身份验证方法**  >  **策略**。
1. 在 **Microsoft Authenticator (预览 ")** 下，选择以下选项：
   1. **启用** -是或否
   1. **目标** -所有用户或选择用户
1. 默认情况下，每个添加的组或用户都处于启用状态，以在无密码和推送通知模式下使用 Microsoft Authenticator)  ( "任何" 模式。 若要更改此项，请为每行：
   1. 浏览到 **...**  > **配置**。
   1. 对于 **身份验证模式** -Any、无密码或 Push
1. 若要应用新策略，请选择 " **保存**"。

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Microsoft Authenticator 的用户注册和管理

用户通过执行以下步骤，注册 Azure AD 的无密码 authentication 方法：

1. 浏览到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。
1. 登录，然后通过选择 " **添加方法 > 验证器应用**" 添加验证器应用，然后选择 " **添加**"。
1. 按照说明在设备上安装和配置 Microsoft Authenticator 应用。
1. 选择 **"完成"** 以完成身份验证器配置。
1. 在 **Microsoft Authenticator** 中，从注册帐户的下拉菜单中选择 " **启用手机登录** "。
1. 按照应用中的说明完成注册无密码 phone 登录的帐户。

组织可以指导其用户使用手机登录，无需使用密码。 有关配置 Microsoft Authenticator 应用并启用手机登录的更多帮助，请参阅 [使用 Microsoft Authenticator 应用登录到帐户](../user-help/user-help-auth-app-sign-in.md)。

> [!NOTE]
> 策略不允许使用手机登录的用户无法再在 Microsoft Authenticator 应用中启用它。

## <a name="sign-in-with-passwordless-credential"></a>用无密码凭据登录

完成以下所有操作后，用户可以开始使用无密码登录：

- 管理员已启用用户的租户。
- 用户已更新她的 Microsoft Authenticator 应用程序以启用手机登录。

用户首次启动电话登录过程时，用户将执行以下步骤：

1. 在登录页面输入用户名。
2. 选择 " **下一步**"。
3. 如有必要，选择 **其他登录方式**。
4. 选择 **"在我的 Microsoft Authenticator 应用上批准请求"**。

然后，用户将看到一个数字。 应用会提示用户进行身份验证，方法是选择适当的数字，而不是输入密码。

用户使用无密码手机登录后，该应用将继续指导用户完成此方法。 但是，用户将看到用于选择其他方法的选项。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="使用 Microsoft Authenticator 应用程序进行浏览器登录的示例。":::

## <a name="known-issues"></a>已知问题

当前预览体验存在以下已知问题。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>无密码手机登录的 "不查看" 选项

在一个方案中，用户可以有一个挂起的无人参与的无密码手机登录验证。 但用户可能会尝试再次登录。 发生这种情况时，用户可能只看到输入密码的选项。

若要解决这种情况，可以使用以下步骤：

1. 打开 Microsoft Authenticator 应用。
2. 响应任何通知提示。

然后，用户可以继续使用无密码手机登录。

### <a name="federated-accounts"></a>联合帐户

当用户启用了任何无密码凭据后，Azure AD 登录过程将停止使用登录 \_ 提示。 因此，该过程不再向用户加速联合登录位置。

此逻辑通常会阻止混合租户中的用户被定向到 Active Directory 联合身份验证服务 (AD FS) 进行登录验证。 但是，用户仍保留单击 " **使用密码**" 的选项。

### <a name="azure-mfa-server"></a>Azure MFA 服务器

可以通过本地 Azure MFA 服务器 (MFA) 为最终用户启用多重身份验证。 用户仍可创建和使用单个无密码手机登录凭据。

如果用户尝试使用无密码 phone 登录凭据升级 Microsoft Authenticator 应用 (5 +) 的多个安装，则此更改可能会导致错误。

### <a name="device-registration"></a>设备注册

在创建此新的强凭据之前，需要满足先决条件。 一个先决条件是，安装 Microsoft Authenticator 应用的设备必须在 Azure AD 租户内注册到单个用户。

目前，只能在单个租户中注册设备。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

> [!NOTE]
> 设备注册与设备管理或移动设备管理 (MDM) 不同。 设备注册仅将设备 ID 和用户 ID 同时关联到 Azure AD 目录中。

## <a name="next-steps"></a>后续步骤

若要了解 Azure AD 身份验证和无密码方法，请参阅以下文章：

- [了解无密码 authentication 的工作原理](concept-authentication-passwordless.md)
- [了解设备注册](../devices/overview.md#getting-devices-in-azure-ad)
- [了解 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)
