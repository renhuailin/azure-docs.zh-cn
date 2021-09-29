---
title: 管理 Azure AD 多重身份验证的身份验证方法 - Azure Active Directory
description: 了解如何配置 Azure AD 多重身份验证的 Azure Active Directory 用户设置
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd69e65f5c280eaac8acb41138cf265be0af113
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763018"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>管理 Azure AD 多重身份验证的用户身份验证方法

Azure AD 中的用户有两组不同的联系人信息：  

- 公共配置文件联系人信息，在用户配置文件中进行管理，对组织成员可见。 对于从本地 Active Directory 同步的用户，此信息在本地 Windows Server Active Directory 域服务中进行管理。
- 身份验证方法，始终保密并仅用于身份验证，包括多重身份验证 (MFA)。 管理员可以在用户的身份验证方法边栏选项卡中管理这些方法，用户可以在 MyAccount 的“安全信息”页中管理其方法。

为用户管理 Azure AD 多重身份验证方法时，身份验证管理员可以： 

1. 为特定用户添加身份验证方法，包括用于 MFA 的电话号码。
1. 重置用户密码。
1. 要求用户重新注册 MFA。
1. 撤销现有 MFA 会话。
1. 删除用户的现有应用密码  

## <a name="add-authentication-methods-for-a-user"></a>为用户添加身份验证方法 

可以通过 Azure 门户或 Microsoft Graph 为用户添加身份验证方法。  

> [!NOTE]
> 出于安全原因，不应使用公共用户联系人信息字段来执行 MFA， 而应让用户应填写用于 MFA 的身份验证方法电话号码。  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="从 Azure 门户添加身份验证方法":::

若要通过 Azure 门户为用户添加身份验证方法，请执行以下操作：  

1. 登录到 **Azure 门户**。 
1. 浏览到“Azure Active Directory” > “用户” > “所有用户”  。 
1. 选择要为其添加身份验证方法的用户，然后选择“身份验证方法”。  
1. 在窗口顶部，选择“+ 添加身份验证方法”。
   1. 选择方法（电话号码或电子邮件）。 电子邮件可用于自助式密码重置，但不能用于身份验证。 添加电话号码时，请选择电话类型，然后输入格式有效的电话号码（例如“+1 4255551234”）。
   1. 选择“添加”。

> [!NOTE]
> 预览体验允许管理员为用户添加任何可用的身份验证方法，而原始体验仅允许更新电话和备用电话方法。

### <a name="manage-methods-using-powershell"></a>使用 PowerShell 来管理方法：  

使用以下命令安装 Microsoft.Graph.Identity.Signins PowerShell 模块。 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

列出特定用户的基于电话的身份验证方法。

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

为特定用户创建移动电话身份验证方法。

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

删除用户的特定电话方法

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

也可以使用 Microsoft Graph API 来管理身份验证方法。有关详细信息，可参阅文档：[Azure AD 身份验证方法 API 概述](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>管理用户身份验证选项

如果你被分配“身份验证管理员”角色，你可以要求用户重置其密码、重新注册 MFA，或者从其用户对象撤消现有的 MFA 会话。 若要管理用户设置，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择“Azure Active Directory” > “用户” > “所有用户”  。
1. 选择要在其上执行操作的用户，然后选择“身份验证方法”。 在窗口顶部，为用户选择以下选项之一：
   - “重置密码”会重置用户的密码并分配一个必须在下次登录时更改的临时密码。
   - “要求重新注册 MFA”在生效后，会请求用户在下次登录时设置一个新的 MFA 身份验证方法。
   
      > [!NOTE]
      > 如果管理员要求用户重新注册 MFA，则不会删除用户当前注册的身份验证方法。 用户重新注册 MFA 后，建议他们查看其安全信息并删除任何以前注册的不再可用的身份验证方法。
   
   - “撤消 MFA 会话”会清除用户的被系统记住的 MFA 会话，并要求用户下一次登录时执行 MFA。这是设备上的策略要求的。
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="从 Azure 门户管理身份验证方法":::

## <a name="delete-users-existing-app-passwords"></a>删除用户的现有应用密码

对于已定义应用密码的用户，管理员还可以选择删除这些密码，使这些应用程序中的旧身份验证失败。 如果需要向用户提供帮助或需要重置其身份验证方法，可能需要执行这些操作。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。 

若要删除用户的应用密码，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择 "Azure Active Directory"  > “用户” > “所有用户”  。
1. 选择“多重身份验证”。 可能需要向右滚动才能看到此菜单选项。 选择以下示例屏幕截图，以查看完整的 Azure 门户窗口和菜单位置：[![在 Azure AD 的“用户”窗口中选择“多重身份验证”。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 选中要管理的用户或用户旁的框。 右侧会显示快速步骤选项列表。
1. 选择“管理用户设置”，然后选中“删除所选用户生成的所有现有应用密码”复选框，如以下示例中所示 ：![删除所有现有的应用密码](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 选择“保存”，然后选择“关闭” 。

## <a name="next-steps"></a>后续步骤

本文介绍了如何配置单个用户设置。 若要配置整体 Azure AD 多重身份验证服务设置，请参阅[配置 Azure AD 多重身份验证设置](howto-mfa-mfasettings.md)。

如果用户需要帮助，请参阅 [Azure AD 多重身份验证的用户指南](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)。