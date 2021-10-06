---
title: 启用按用户的多重身份验证 - Azure Active Directory
description: 了解如何通过更改用户状态来启用每用户 Azure AD 多重身份验证
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 066e0a48442825785bc2c413c0e7d40ca470f45d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353242"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>启用每用户 Azure AD 多重身份验证来保护登录事件

若要保护 Azure AD 中的用户登录事件，你可要求进行多重身份验证 (MFA)。 建议的方法是使用条件访问策略启用 Azure AD 多重身份验证，以保护用户。 条件访问是 Azure AD Premium P1 或 P2 的功能，使你可在某些情况下根据需要应用要求 MFA 的规则。 若要开始使用条件访问，请参阅[教程：使用 Azure AD 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)。

对于不带条件访问的 Azure AD 免费租户，你可以[使用安全默认值来保护用户](../fundamentals/concept-fundamentals-security-defaults.md)。 系统会根据需要提示用户进行 MFA，但你无法定义自己的规则来控制行为。

如果需要，你可转而使每个帐户进行每用户 Azure AD 多重身份验证。 逐个为用户启用此功能后，他们每次登录时都会执行多重身份验证（有一些例外情况，例如，当他们从受信任的 IP 地址登录时，或者开启了“记住受信任设备上的 MFA”功能时）。

如果 Azure AD 许可证不包括条件访问，并且你不想使用安全默认值，则建议更改[用户状态](#azure-ad-multi-factor-authentication-user-states)。 有关启用 MFA 的不同方法的详细信息，请参阅 [Azure AD 多重身份验证的功能和许可证](concept-mfa-licensing.md)。

> [!IMPORTANT]
>
> 本文详细介绍了如何查看和更改每用户 Azure AD 多重身份验证的状态。 如果你使用条件访问或安全默认值，则请勿使用以下步骤查看或启用用户帐户。
>
> 通过条件访问策略启用 Azure AD 多重身份验证不会更改用户的状态。 如果用户看似已被禁用，请不要担心。 条件访问不会更改状态。
>
> 如果你使用条件访问策略，请勿启用或强制执行每用户 Azure AD 多重身份验证。

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD 多重身份验证用户状态

用户的状态反映管理员是否已在每用户 Azure AD 多重身份验证中登记用户。 Azure AD 多重身份验证中的用户帐户具有以下三种不同状态：

| 状态 | 描述 | 旧式身份验证受影响 | 受影响的浏览器应用 | 新式身份验证受影响 |
|:---:| --- |:---:|:--:|:--:|
| 已禁用 | 未在每用户 Azure AD 多重身份验证中登记的用户的默认状态。 | 否 | 否 | 否 |
| 已启用 | 用户已在每用户 Azure AD 多重身份验证中登记，但仍可使用其密码进行旧式身份验证。 如果用户尚未注册 MFA 身份验证方法，则将在下次使用新式身份验证（例如通过 Web 浏览器）登录时收到注册提示。 | 否。 旧式身份验证继续工作，直到注册过程完成。 | 是的。 会话过期后，需要进行 Azure AD 多重身份验证注册。| 是的。 访问令牌过期后，需要进行 Azure AD 多重身份验证注册。 |
| 强制 | 用户已在每用户 Azure AD 多重身份验证中登记。 如果用户尚未注册身份验证方法，则将在下次使用新式身份验证（例如通过 Web 浏览器）登录时收到注册提示。 在“已启用”状态下完成注册的用户将自动更改为“已强制执行”状态 。 | 是的。 应用需要应用密码。 | 是的。 登录时需要执行 Azure AD 多重身份验证。 | 可以。 登录时需要执行 Azure AD 多重身份验证。 |

所有用户的初始状态均为“已禁用”。 在每用户 Azure AD 多重身份验证中登记用户后，其状态将更改为“已启用”。 当已启用的用户登录并完成注册过程后，用户的状态将更改为“强制”。 管理员可以为用户切换状态，包括从“已强制执行”到“已启用”或“已禁用”  。

> [!NOTE]
> 如果对某用户重新启用了每用户 MFA，且该用户不重新注册，其 MFA 状态在 MFA 管理 UI 中不会从“已启用”转换为“已强制执行” 。 管理员必须将用户的状态直接切换为“已强制执行”。

## <a name="view-the-status-for-a-user"></a>查看用户状态

若要查看和管理用户状态，请完成以下步骤以访问 Azure 门户页：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后选择“用户” > “所有用户” 。
1. 选择“多重身份验证”。 可能需要向右滚动才能看到此菜单选项。 选择以下示例屏幕截图，以查看完整的 Azure 门户窗口和菜单位置：[![在 Azure AD 的“用户”窗口中选择“多重身份验证”。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 此时会打开一个显示用户状态的新页，如以下示例中所示。
   ![显示 Azure AD 多重身份验证的示例用户状态信息的屏幕截图](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>更改用户状态

若要为用户更改每用户 Azure AD 多重身份验证状态，请完成以下步骤：

1. 使用前文[查看用户的状态](#view-the-status-for-a-user)中的步骤访问 Azure AD 多重身份验证“用户”页。
1. 找到要为其启用每用户 Azure AD 多重身份验证的用户。 可能需要在顶部将视图更改为“用户”。
   ![从用户选项卡中选择要更改其状态的用户](./media/howto-mfa-userstates/enable1.png)
1. 选中要更改其状态的用户的名称旁边的框。
1. 在右侧的“快速步骤”下，选择“启用”或“禁用”  。 在以下示例中，用户 John Smith 的名称旁边有一个勾选标记，表示将为该用户启用：![通过单击快速步骤菜单上的“启用”来启用选定用户](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > “已启用”的用户在注册 Azure AD 多重身份验证后会自动切换为“已强制”。 如果用户已经注册，或者用户不接受连接旧式身份验证协议时发生中断，请将用户状态手动更改为“已强制执行”。

1. 在打开的弹出窗口中确认你的选择。

启用用户后，通过电子邮件通知他们。 告诉用户显示了提示，要求他们在下次登录时注册。 此外，如果你的组织使用不支持新式身份验证的非浏览器应用，则他们需要创建应用密码。 有关详细信息，请参阅 [Azure AD 多重身份验证最终用户指南](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)，以帮助他们开始使用。

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从每用户 MFA 转换为基于条件访问的 MFA

如果使用每用户启用和强制执行的 Azure AD 多重身份验证启用用户，则以下 PowerShell 可帮助你转换为基于条件访问的 Azure AD 多重身份验证。

在 ISE 窗口中运行此 PowerShell，或另存为要在本地运行的 `.PS1` 文件。 操作只能使用 [MSOnline 模块](/powershell/module/msonline#msonline)完成。 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="next-steps"></a>后续步骤

若要配置 Azure 多重身份验证设置，请参阅[配置 Azure AD 多重身份验证设置](howto-mfa-mfasettings.md)。

若要管理 Azure AD 多重身份验证的用户设置，请参阅[管理 Azure AD 多重身份验证的用户设置](howto-mfa-userdevicesettings.md)。

若要了解提示用户执行 MFA 与否的原因，请参阅 [Azure AD 多重身份验证报告](howto-mfa-reporting.md)。