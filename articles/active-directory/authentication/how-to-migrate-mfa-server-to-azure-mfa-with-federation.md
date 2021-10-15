---
title: 通过联合身份验证迁移到 Azure AD MFA - Azure Active Directory
description: 通过联合身份验证从本地 Azure MFA 服务器迁移到 Azure AD MFA 的分步指南
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5d92d16f5ac9fcd8aa69ce9fd71f4844a77d28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352716"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>通过联合身份验证迁移到 Azure AD MFA

将多重身份验证 (MFA) 解决方案迁移到 Azure Active Directory (Azure AD) 是迈向云之旅的第一步。 将来还可以考虑迁移到 Azure AD 进行用户身份验证。 有关详细信息，请参阅通过云身份验证迁移到 Azure AD MFA 的过程。

若要通过联合身份验证迁移到 Azure AD MFA，请在 AD FS 上安装 Azure MFA 身份验证提供程序。 Azure AD 信赖方信任和其他信赖方信任配置为对已迁移的用户使用 Azure MFA。

下图显示了此迁移的过程。

![显示此过程的各个步骤的流程图。 这些步骤以相同的顺序与本文档中的标题保持一致](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>创建迁移组

若要创建新的条件访问策略，需要将这些策略分配给组。 为此，你可以使用现有的 Azure AD 安全组或 Microsoft 365 组。 也可以创建或同步新组。

你还需要一个 Azure AD 安全组，以便以迭代方式将用户迁移到 Azure AD MFA。 在声明规则中使用这些组。

不要重复使用安全组。 如果你正在使用某个安全组通过条件访问策略保护一组重要应用，该组就只能用于这个目的。

## <a name="prepare-ad-fs"></a>准备 AD FS

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>将 AD FS 服务器场升级到 2019，将 FBL 升级到 4

在 AD FS 2019 中，可以为信赖方（例如应用程序）指定附加身份验证方法。 使用组成员身份来确定身份验证提供程序。 通过指定附加身份验证方法，可以过渡到 Azure AD MFA，同时在过渡期间保持其他身份验证不变。 有关详细信息，请参阅[使用 WID 数据库升级到 Windows Server 2016 中的 AD FS](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)。 该文章介绍了如何将场升级到 AD FS 2019，以及如何将 FBL 升级到 4。

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>配置声明规则以调用 Azure AD MFA

指定 Azure AD MFA 作为附加身份验证方法后，可以分配要使用它的用户组。 为此，可以配置声明规则（也称为信赖方信任）。 使用组可以控制全局调用的或者由应用程序调用的身份验证提供程序。 例如，可以针对注册了组合式安全信息或者已迁移其电话号码的用户调用 Azure AD MFA，而针对其他用户，则调用 MFA 服务器。

> [!NOTE]
> 声明规则需要本地安全组。 更改声明规则之前，请对其进行备份。


#### <a name="back-up-existing-rules"></a>备份现有规则

在配置新的声明规则之前，请备份现有规则。 你需要在清理过程中还原这些规则。 

根据你的配置，可能还需要复制现有规则，并追加为迁移创建的新规则。  

若要查看现有的全局规则，请运行：  

```powershell
Get-AdfsAdditionalAuthenticationRule
```

若要查看现有的信赖方信任，请运行以下命令并将 RPTrustName 替换为信赖方信任声明规则的名称： 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules 
```

#### <a name="access-control-policies"></a>访问控制策略

> [!NOTE]
> 无法将访问控制策略配置为根据组成员身份调用特定的身份验证提供程序。 

 
若要从访问控制策略过渡到附加身份验证规则，请针对每个使用 MFA 服务器身份验证提供程序的信赖方信任运行以下命令：


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

此命令会将逻辑从当前的访问控制策略移入附加身份验证规则。


#### <a name="set-up-the-group-and-find-the-sid"></a>设置组并查找 SID

需要设置一个特定的组，用于放置要对其调用 Azure AD MFA 的用户。 需要该组的安全标识符 (SID)。

若要查找组 SID，请结合组名使用以下命令

`Get-ADGroup "GroupName"`

![显示 Get-ADGroup 脚本结果的屏幕截图图像。](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>设置声明规则以调用 Azure MFA

以下 PowerShell cmdlet 针对组中的、不在企业网络上的用户调用 Azure AD MFA。 将“YourGroupSid”替换为运行上述 cmdlet 后找到的 SID。

请务必查看[如何在 AD FS 2019 中选择附加身份验证提供程序](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server)。 

 > [!IMPORTANT]
> 备份现有声明规则

 

#### <a name="set-global-claims-rule"></a>设置全局声明规则 

运行以下 Azure Powershell cmdlet： 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules
```

 

该命令将返回信赖方信任的当前附加身份验证规则。 将以下规则追加到当前声明规则：

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```

以下示例假设当前声明规则已配置为，当用户从你的网络外部进行连接时，提示他们执行 MFA。 此示例包括需要追加的附加规则。

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


#### <a name="set-per-application-claims-rule"></a>设置每个应用程序的声明规则

此示例将修改特定信赖方信任（应用程序）上的声明规则，并提供必须追加的信息。

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>将 Azure AD MFA 配置为 AD FS 中的身份验证提供程序

若要为 AD FS 配置 Azure AD MFA，必须配置每个 AD FS 服务器。 如果场中有多个 AD FS 服务器，则可以使用 Azure AD PowerShell 远程配置这些服务器。

有关此过程的分步说明，请参阅[使用 AD FS 将 Azure AD MFA 配置为身份验证提供程序](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)一文中的[配置 AD FS 服务器](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)。

配置服务器后，可以添加 Azure AD MFA 作为附加身份验证方法。 

![显示选中了“Azure MFA”和“Azure 多重身份验证服务器”的“编辑身份验证方法”屏幕的屏幕截图](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>准备 Azure AD 并实施

### <a name="ensure-supportsmfa-is-set-to-true"></a>确保将 SupportsMFA 设置为 True

对于联合域，可以通过 Azure AD 条件访问或本地联合身份验证提供程序强制执行 MFA。 Azure AD 中的每个联合域都有一个 SupportsMFA 标志。 当 SupportsMFA 标志设置为 True 时，Azure AD 将用户重定向到 AD FS 或其他联合身份验证提供程序上的 MFA。 例如，如果用户正在访问某个应用程序，该应用程序已配置有需要 MFA 的条件访问策略，则该用户将被重定向到 AD FS。 通过将 Azure AD MFA 添加为 AD FS 中的身份验证方法，可以在配置完成后调用 Azure AD MFA。

如果 SupportsMFA 标志设置为 False，你可能没有使用 Azure MFA；你可能正在使用 AD FS 信赖方的声明规则来调用 MFA。

你可以使用以下 [Windows PowerShell](/powershell/module/msonline/get-msoldomainfederationsettings) cmdlet 检查 SupportsMFA 标志的状态：

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

如果联合域的 SupportsMFA 标志设置为 false 或为空，请使用以下 Windows PowerShell cmdlet 将其设置为 true：

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

此配置允许在 AD FS 上决定是使用 MFA 服务器还是 Azure MFA。

### <a name="configure-conditional-access-policies-if-needed"></a>根据需要配置条件访问策略

如果使用条件访问来确定何时提示用户进行 MFA，则不需要更改策略。

如果联合域的 SupportsMFA 设置为 false，请分析 Azure AD 信赖方信任的声明规则，并创建支持相同安全目标的条件访问策略。

创建条件访问策略以实施与 AD FS 相同的控制后，可以在 Azure AD 信赖方上备份并删除声明规则自定义项。

有关更多信息，请参见以下资源：

* [计划条件访问部署](../conditional-access/plan-conditional-access.md)

* [常见条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>为用户注册 Azure MFA

可通过两种方式为用户注册 Azure MFA： 

* 注册组合安全性（MFA 和自助式密码重置） 

* 从 MFA 服务器迁移电话号码

Microsoft Authenticator 应用可以在无密码模式下使用。 它还可以在任一注册方法中用作 MFA 的第二个因素。

### <a name="register-for-combined-security-registration-recommended"></a>注册组合式安全注册（建议）

让用户注册组合式安全信息，以便在单个位置为身份验证方法和设备注册 MFA 和 SSPR。 尽管可将数据从 MFA 服务器迁移到 Azure AD MFA，但此过程存在以下难题：

* 只能迁移电话号码。

* 需要重新注册 Authenticator 应用。

* 可能会迁移过时的数据。

Microsoft 提供了通信模板，你可以将这些模板提供给用户，以指导他们完成组合注册过程。 这些模板包括电子邮件、海报、桌面立牌和其他资产的模板。 用户可在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 上注册信息，在此过程中他们会转到组合式安全注册屏幕。 

建议[使用条件访问保护安全注册过程](../conditional-access/howto-conditional-access-policy-registration.md)，条件访问要求从受信任的设备或位置进行注册。

> [!NOTE]
> 对于必须从不受信任的位置或设备注册其组合式安全信息的用户，可为其颁发临时访问密码，或者暂时将其从策略中排除。

### <a name="migrate-phone-numbers-from-mfa-server"></a>从 MFA 服务器迁移电话号码

迁移电话号码可能导致迁移过时的号码，并很有可能会使用户保持使用基于电话的 MFA，而不是设置更安全的方法，例如[使用 Microsoft Authenticator 应用进行无密码登录](howto-authentication-passwordless-phone.md)。 你无法迁移设备注册，例如其 Microsoft Authenticator 应用设置。 建议让所有用户都注册[组合式安全信息](howto-registration-mfa-sspr-combined.md)。 组合式安全信息还为用户注册自助式密码重置。

如果无法让用户注册组合式安全信息，可以从 MFA 服务器中将用户及其电话号码一起导出，然后将电话号码导入 Azure AD。
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>从 MFA 服务器导出用户电话号码 

1. 在 MFA 服务器上打开多重身份验证服务器管理控制台。 

1. 选择“文件”，然后选择“导出用户”。

1. 保存 CSV 文件。 默认名称为 Multi-Factor Authentication Users.csv

#### <a name="interpret-and-format-the-csv--file"></a>解释和格式化 .csv 文件

.csv 文件包含许多不需要迁移的字段。 在将电话号码导入 Azure AD 之前，对其进行编辑和格式化。 

打开 .csv 文件时，要关注的列包括：

* 用户名
* 主要电话
* 主要国家/地区代码
* 备份国家/地区代码
* 备份电话号码
* 备份分机号

你需要对数据进行解释、清理和格式化。

#### <a name="tips-to-avoid-errors-during-import"></a>有关避免导入过程中出错的提示

* 在使用身份验证方法 API 将电话号码导入 Azure AD 之前，请修改 CSV 文件。 

* 将 .csv 文件简化为三列：UPN、PhoneType 和 PhoneNumber。 

* 确保导出的 MFA 服务器用户名与 Azure AD UserPrincipalName 匹配。 如果不匹配，请更新 CSV 文件中的用户名，使之与 Azure AD 中的 UserPrincipalName 相匹配，否则将找不到相应的用户。

用户可能已在 Azure AD 中注册了电话号码。 使用身份验证方法 API 导入电话号码时，必须确定是要覆盖现有电话号码，还是将导入的号码添加为备用电话号码。

以下 PowerShell cmdlet 采用你提供的 CSV 文件，并使用身份验证方法 API 将导出的电话号码添加为每个 UPN 的电话号码。 将“myPhones”替换为 CSV 文件的名称。

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>将用户添加到相应的组

* 如果创建了新的条件访问策略，请将相应的用户添加到这些组。 

* 如果为声明规则创建了本地安全组，请将相应的用户添加到这些组。

建议不要重复使用安全组。 因此，如果你正在使用某个安全组通过条件访问策略保护一组重要应用，该组就只能用于这个目的。

## <a name="monitoring"></a>监视

可以使用[身份验证方法使用情况和见解报告](https://portal.azure.com/)来监视 Azure MFA 注册。 可以在 Azure AD 中找到此报告。 依次选择“监视”、“使用情况和见解” 。 

在“使用情况和见解”中，选择“身份验证方法”。 

可以在“注册”选项卡上找到详细的 Azure MFA 注册信息。可以选择“可以进行 Azure 多重身份验证的用户”超链接向下钻取，以查看已注册用户的列表。

![显示注册到 MFA 的用户的“身份验证方法 | 活动”屏幕的图像](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>清理步骤

完成到 Azure MFA 的迁移并准备好解除 MFA 服务器授权后，请执行以下三项操作： 

1. 将 AD FS 上的声明规则还原到迁移前的配置，并删除 MFA 服务器身份验证提供程序。

1. 删除用作 AD FS 中的身份验证提供程序的 MFA 服务器。 这将确保所有用户都使用 Azure MFA，因为它将是唯一启用的附加身份验证方法。 

1. 解除 MFA 服务器授权。

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>还原 AD FS 上的声明规则并删除 MFA 服务器身份验证提供程序

按照“配置声明规则以调用 Azure AD MFA”中所述的步骤还原到已备份的声明规则，并删除所有 AzureMFAServerAuthentication 声明规则。 

例如，删除规则中的以下内容： 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
&quot;**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");'
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>禁用用作 AD FS 中的身份验证提供程序的 MFA 服务器

此项更改可确保只将 Azure MFA 用作身份验证提供程序。

1. 打开 AD FS 管理控制台。

1. 在“服务”下，右键单击“身份验证方法”并选择“编辑多重身份验证方法”  。 

1. 取消选中“Azure 多重身份验证服务器”旁边的框。 

### <a name="decommission-the-mfa-server"></a>解除 MFA 服务器授权

按照企业服务器解除授权过程删除环境中的 MFA 服务器。

解除 MFA 服务器授权时，可能的注意事项包括： 

* 在删除服务器之前查看 MFA 服务器日志，确保没有任何用户或应用程序正在使用该服务器。

* 通过多重身份验证服务器上的控制面板卸载该服务器

* 对于留下的日志和数据目录，可以选择先备份后清理。 

* 卸载多重身份验证 Web 服务器 SDK（如果适用），包括 inetpub\wwwroot\MultiFactorAuthWebServiceSdk 和/或 MultiFactorAuth 目录中留下的任何文件

* 对于 8.0 之前的 MFA 服务器版本，可能还需要删除多重身份验证手机应用 Web 服务

## <a name="next-steps"></a>后续步骤

- [部署密码哈希同步](../hybrid/whatis-phs.md)
- [详细了解条件访问](../conditional-access/overview.md)
- [将应用程序迁移到 Azure AD](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
