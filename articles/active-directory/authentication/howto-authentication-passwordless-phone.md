---
title: 使用 Microsoft Authenticator 应用进行无密码登录 - Azure Active Directory
description: 启用使用 Microsoft Authenticator 应用无密码登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/02/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a38361355c48021b6145552071593056a2a3caa6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124759731"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>启用使用 Microsoft Authenticator 应用的无密码登录 

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 Microsoft Authenticator 使用基于密钥的身份验证来启用与使用 PIN 或生物识别的设备绑定的用户凭据。 [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-identity-verification)使用类似的技术。

此身份验证技术可用于任何设备平台，包括移动设备。 此技术还可以和与 Microsoft 身份验证库集成的任何应用或网站配合使用。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="要求用户批准登录的浏览器登录示例。":::

启用从 Microsoft Authenticator 应用进行手机登录的用户会看到一条消息，要求他们点击其应用中的某个数字。 无需提供用户名或密码。 若要在应用中完成登录过程，用户接下来必须执行以下操作：

1. 匹配数字。
2. 选择“批准”。
3. 提供 PIN 或生物识别。

## <a name="prerequisites"></a>先决条件

若要使用 Microsoft Authenticator 应用进行无密码手机登录，必须满足以下先决条件：

- Azure AD 多重身份验证，允许将推送通知作为验证方法。 向智能手机或平板电脑推送通知可帮助 Authenticator 应用防止未经授权访问帐户并阻止欺诈交易。 Authenticator 应用在设置为发送推送通知时会自动生成代码，因此即使用户的设备没有连接，用户也有备用登录方法。 
  
  必须启用 Azure 多重身份验证连接器，以允许用户注册电话登录的推送通知。

  ![Azure 多重身份验证连接器已启用的屏幕截图。](media/howto-authentication-passwordless-phone/connector.png)

- 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。
- 必须在 Azure AD 租户内将安装 Microsoft Authenticator 应用的设备注册到单个用户。 

> [!NOTE]
> 如果已使用 Azure AD PowerShell 启用 Microsoft Authenticator 无密码登录，则会为整个目录启用此功能。 如果使用此新方法启用，则它会取代 PowerShell 策略。 建议通过新的“身份验证方法”菜单为租户中的所有用户启用，否则不在新策略中的用户将无法使用无密码登录。

## <a name="enable-passwordless-authentication-methods"></a>启用无密码身份验证方法

若要在 Azure AD 中使用无密码身份验证，请首先启用合并注册体验，然后为用户启用无密码方法。

### <a name="enable-the-combined-registration-experience"></a>启用合并注册体验

适用于无密码身份验证方法的注册功能依赖于合并注册功能。 若要让用户自行完成合并注册，请按照步骤[启用合并安全信息注册](howto-registration-mfa-sspr-combined.md)。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>启用无密码手机登录身份验证方法

通过 Azure AD，你可以选择在登录过程中可使用的身份验证方法。 之后用户注册他们想要使用的方法。 “Microsoft Authenticator”身份验证方法策略同时管理传统的推送 MFA 方法以及无密码身份验证方法。 

若要启用无密码手机登录验证方法，请完成以下步骤：

1. 使用“身份验证策略管理员”帐户登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后浏览到“安全” > “身份验证方法” > “策略”。
1. 在“Microsoft Authenticator”下，选择以下选项：
   1. “启用”-“是”或“否”
   1. “目标”-“所有用户”或“选择用户”
1. 默认情况下，每个添加的组或用户都能在无密码和推送通知模式（“任何”模式）下使用 Microsoft Authenticator。 若要更改此设置，请为每行：
   1. 浏览到“...” > “配置”。
   1. 对于“身份验证模式”- 选择“任何”或“无密码”。 选择“推送”可阻止使用无密码手机登录凭据。 
1. 若要应用新策略，请选择“保存”。

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Microsoft Authenticator 用户注册和管理

用户可以使用以下步骤自行注册 Azure AD 的无密码身份验证方法：

1. 浏览到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。
1. 登录，然后选择“添加方法”>“Authenticator 应用”并“添加”，以添加 Authenticator 应用。
1. 按照说明在设备上安装和配置 Microsoft Authenticator 应用。
1. 选择“完成”以完成 Authenticator 配置。
1. 在“Microsoft Authenticator”中，从注册帐户的下拉菜单中选择“启用手机登录”。
1. 按照应用中的说明完成帐户无密码手机登录注册。

组织可以指导其用户使用手机无密码登录。 若要获得有关配置 Microsoft Authenticator 应用并启用手机登录的更多帮助，请参阅[使用 Microsoft Authenticator 应用登录帐户](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)。

> [!NOTE]
> 策略不允许其使用手机登录的用户，无法再在 Microsoft Authenticator 应用内启用它。

## <a name="sign-in-with-passwordless-credential"></a>通过无密码凭据登录

完成以下所有操作后，用户可以开始使用无密码登录：

- 管理员已启用用户的租户。
- 用户已更新 Microsoft Authenticator 应用以启用手机登录。

用户首次启动手机登录过程时，需执行以下步骤：

1. 在登录页面输入用户名。
2. 选择“下一步”。
3. 如有必要，选择“其他登录方式”。
4. 选择“在我的 Microsoft Authenticator 应用上批准请求”。

之后，用户将看到一个数字。 应用会提示用户选择相应数字进行身份验证，而不是输入密码。

用户使用无密码手机登录后，应用会继续指导用户完成此方法。 但是，用户将看到用于选择其他方法的选项。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="使用 Microsoft Authenticator 应用的浏览器登录示例。":::

## <a name="known-issues"></a>已知问题

存在以下已知问题。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>未看到无密码手机登录的选项

在方案中，用户的无密码手机登录验证可能会挂起无答复。 但用户可能会尝试再次登录。 发生这种情况时，用户可能只看到输入密码的选项。

若要解决这个问题，可以使用以下步骤：

1. 打开 Microsoft Authenticator 应用。
2. 对所有通知提示做出响应。

之后用户可以继续使用无密码手机登录。

### <a name="federated-accounts"></a>联合帐户

当用户启用任何无密码凭据后，Azure AD 登录过程将停止使用 login\_hint。 因此，此过程不再将用户加速转到联合登录位置。

此逻辑通常会阻止将混合租户中的用户定向到 Active Directory 联合身份验证服务 (AD FS) 进行登录验证。 但是，用户仍可以选择单击“改为使用密码”。

### <a name="azure-mfa-server"></a>Azure MFA 服务器

可以通过本地 Azure MFA 服务器为最终用户启用多重身份验证 (MFA)。 用户仍可创建和使用单个无密码手机登录凭据。

如果用户尝试使用无密码手机登录凭据升级 Microsoft Authenticator 应用的多个安装（5 个以上），则此项更改可能导致错误。

### <a name="device-registration"></a>设备注册

在创建新的强凭据之前，需要满足先决条件。 一个先决条件是，必须在 Azure AD 租户内将安装 Microsoft Authenticator 应用的设备注册到单个用户。

目前，设备只能在单个租户中注册。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

> [!NOTE]
> 设备注册不同于设备管理或移动设备管理 (MDM)。 设备注册仅将设备 ID 和用户 ID 同时关联到 Azure AD 目录中。

## <a name="next-steps"></a>后续步骤

若要了解 Azure AD 身份验证和无密码方法，请参阅以下文章：

- [了解无密码身份验证的工作原理](concept-authentication-passwordless.md)
- [了解设备注册](../devices/overview.md)
- [了解 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)