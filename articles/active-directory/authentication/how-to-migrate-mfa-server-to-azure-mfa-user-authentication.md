---
title: 迁移到 Azure AD MFA 和 Azure AD 用户身份验证 - Azure Active Directory
description: 有关从本地 Azure MFA 服务器移到 Azure AD MFA 和 Azure AD 用户身份验证的分步指南
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730804"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>迁移到 Azure AD MFA 和 Azure AD 用户身份验证

多重身份验证 (MFA) 可以帮助保护基础结构和资产，使其免受恶意行动者的入侵。 Microsoft 的多重身份验证服务器（MFA 服务器）不再为新部署提供。 使用 MFA 服务器的客户应迁移到 Azure AD 多重身份验证 (Azure AD MFA)。 

可以通过多种做法将多重身份验证 (MFA) 从 MFA 服务器迁移到 Azure Active Directory (Azure AD)。 其中包括：

* 良好：仅将 [MFA 服务移到 Azure AD](how-to-migrate-mfa-server-to-azure-mfa.md)。 
* 更好：将 MFA 服务和用户身份验证移到 Azure AD，本文将介绍这种做法。
* 最好：将所有应用程序、MFA 服务和用户身份验证移到 Azure AD。 如果你打算迁移应用程序，请参阅本文的“将应用程序移到 Azure AD”部分。 

若要为组织选择适当的 MFA 迁移选项，请参阅[从 MFA 服务器迁移到 Azure Active Directory MFA](how-to-migrate-mfa-server-to-azure-mfa.md) 中的注意事项。 

下图显示了在 AD FS 上保留某些应用程序的情况下，迁移到 Azure AD MFA 和云身份验证的过程。 使用此过程可以根据组成员身份以迭代方式将用户从 MFA 服务器迁移到 Azure MFA。

本文的后续部分将解释每个步骤。

>[!NOTE]
>如果你打算在进行这种迁移的过程中将任何应用程序移到 Azure Active Directory，应在迁移 MFA 之前执行此操作。 如果要移动所有应用，则可以跳过介绍 MFA 迁移过程的部分。 请参阅本文末尾有关移动应用程序的部分。

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>迁移到 Azure AD 和用户身份验证的过程

![迁移到 Azure AD 和用户身份验证的过程。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>准备组和条件访问

三项 MFA 迁移功能会使用组。
* 使用分阶段推出，以迭代方式将用户移到 Azure AD MFA。
使用在 Azure AD 中创建的组，也称为仅限云的组。 对于将用户移到 MFA，以及对于条件访问策略，你都可以使用 Azure AD 安全组或 Microsoft 365 组。  有关详细信息，请参阅有关创建 Azure AD 安全组的主题，以及面向管理员的 Microsoft 365 组概述。
  >[!IMPORTANT]
  >分阶段推出过程不支持嵌套组和动态组。 不要将此类组用于分阶段推出工作。
* 条件访问策略。 可以使用 Azure AD 或本地组进行条件访问。
* 使用声明规则调用 AD FS 应用程序的 Azure AD MFA。
仅当你在 AD FS 上有应用程序时，这种方法才适用。
这必须是本地 Active Directory 安全组。 一旦使用 Azure AD MFA 作为额外的身份验证方法，即可指定用户组在每个信赖方信任上使用该方法。 例如，可以针对已迁移的用户调用 Azure AD MFA，针对尚未迁移的用户调用 MFA 服务器。 这种做法在测试和迁移过程中都很有帮助。 

>[!NOTE] 
>建议不要重复使用已用于安全性的组。 使用某个安全组通过条件访问策略保护一组重要应用时，该组应该只用于这种目的。

### <a name="configure-conditional-access-policies"></a>配置条件访问策略

如果你已使用条件访问来确定何时提示用户执行 MFA，则不需要对策略进行任何更改。 将用户迁移到云身份验证后，他们将开始使用由现有条件访问策略定义的 Azure AD MFA。 他们不再会重定向到 AD FS 和 MFA 服务器。

如果联合域的 SupportsMFA 设置为 false，则你可能正在使用声明规则在 AD FS 上强制执行 MFA。 在这种情况下，需要分析 Azure AD 信赖方信任上的声明规则，并创建支持相同安全目标的条件访问策略。

如果需要配置条件访问策略，需要在启用分阶段推出之前执行此操作。 有关更多信息，请参见以下资源：
* [计划条件访问部署](../conditional-access/plan-conditional-access.md)
* [常见条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>准备 AD FS 

如果 AD FS 中没有任何需要 MFA 的应用程序，则可以跳过本部分并转到“准备分阶段推出”部分。

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>将 AD FS 服务器场升级到 2019 和 FBL 4

在 AD FS 2019 中，Microsoft 发布了新功能用来为信赖方（例如某个应用程序）指定附加的身份验证方法。 实现这种功能的方式是使用组成员身份来确定身份验证提供程序。 通过指定附加的身份验证方法，可以过渡到 Azure AD MFA，同时在过渡期间保持其他身份验证不变。 

有关详细信息，请参阅[使用 WID 数据库升级到 Windows Server 2016 中的 AD FS](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server)。 该文章介绍了如何将场升级到 AD FS 2019，以及如何将 FBL 升级到 4。

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>配置声明规则以调用 Azure AD MFA

指定 Azure AD MFA 作为附加身份验证方法后，可以分配要使用 Azure AD MFA 的用户组。 为此，可以配置声明规则（也称为“信赖方信任”）。 使用组可以控制全局调用的或者由应用程序调用的身份验证提供程序。 例如，可以针对注册了组合安全性信息或者已迁移其电话号码的用户调用 Azure AD MFA，而针对其他用户，则调用 MFA 服务器。 

>[!NOTE]
>声明规则需要本地安全组。 

#### <a name="back-up-existing-rules"></a>备份现有规则 

在配置新的声明规则之前，请备份现有规则。 在执行清理步骤的过程中，需要还原这些现有规则。 

根据你的配置，可能还需要复制现有规则，并追加要为迁移创建的新规则。

若要查看现有的全局规则，请运行：  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

若要查看现有的信赖方信任，请运行以下命令并将 RPTrustName 替换为信赖方信任声明规则的名称： 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>访问控制策略

>[!NOTE]
>无法配置访问控制策略，因此将基于组成员身份调用特定的身份验证提供程序。 

若要从访问控制策略过渡到附加身份验证规则，请针对每个使用 MFA 服务器身份验证提供程序的信赖方信任运行以下命令：

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

此命令会将逻辑从当前的访问控制策略移入到附加身份验证规则。

#### <a name="set-up-the-group-and-find-the-sid"></a>设置组并查找 SID

需要设置一个特定的组，用于放置要为其调用 Azure AD MFA 的用户。 需要查找该组的安全标识符 (SID)。
若要查找组 SID，请使用以下命令，其中包含你的组名 `Get-ADGroup “GroupName”`

![用于获取组 SID 的 PowerShell 命令。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>设置声明规则以调用 Azure MFA

以下 PowerShell cmdlet 针对组内未在企业网络上的用户调用 Azure AD MFA。 必须将“YourGroupSid”替换为运行上述 cmdlet 后找到的 SID。

请务必查看[如何在 AD FS 2019 中选择附加身份验证提供程序](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019)。 

>[!IMPORTANT] 
>在继续操作之前，请备份现有声明规则。

##### <a name="set-global-claims-rule"></a>设置全局声明规则 

运行以下命令并将 RPTrustName 替换为信赖方信任声明规则的名称： 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

该命令将返回信赖方信任的当前附加身份验证规则。  
需将以下规则追加到当前声明规则：

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

以下示例假设当前声明规则已配置为当用户从你的网络外部进行连接时提示他们执行 MFA。 此示例包括需要追加的附加规则。

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

##### <a name="set-per-application-claims-rule"></a>设置每个应用程序的声明规则

此示例修改特定信赖方信任（应用程序）的声明规则。 其中包括需要追加的附加规则。

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>将 Azure AD MFA 配置为 AD FS 中的身份验证提供程序

若要为 AD FS 配置 Azure AD MFA，必须配置每个 AD FS 服务器。 如果场中有多个 AD FS 服务器，则你可以使用 Azure AD PowerShell 远程配置这些服务器。

有关此过程的分步指导，请参阅[配置 AD FS 服务器](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

配置服务器后，可以添加 Azure AD MFA 作为附加身份验证方法。 

![展示如何将 Azure AD MFA 添加为附加身份验证方法的屏幕截图。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>准备分阶段推出 

现在你已准备好启用分阶段推出功能。 分阶段推出可帮助你以迭代方式将用户转移到 PHS 或 PTA。 

* 请务必查看[支持的方案](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios)。 
* 首先需要完成 [PHS 准备工作](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync)或 [PTA 准备工作](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication)。 我们建议转移到 PHS。 
* 接下来，完成[无缝 SSO 准备工作](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)。 
* 为所选的身份验证方法[启用云身份验证分阶段推出](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant)。 
* 添加已为分阶段推出而创建的组。 请记住，将以迭代方式在组中添加用户，并且这些组不能是动态组或嵌套组。 

## <a name="register-users-for-azure-mfa"></a>为用户注册 Azure MFA

可通过两种方式为用户注册 Azure MFA： 

* 注册组合安全性（MFA 和自助式密码重置） 
* 从 MFA 服务器迁移电话号码

Microsoft Authenticator 应用可用作无密码登录方法，并且在使用上述任一方法时，它可以用作 MFA 的第二因素。

### <a name="register-for-combined-security-registration-recommended"></a>注册组合安全性（建议）

建议让用户注册组合安全性信息，以便在单一位置注册 MFA 和 SSPR 的身份验证方法和设备。 尽管可将数据从 MFA 服务器迁移到 Azure AD MFA，但此过程存在以下难题：

* 只能迁移电话号码。
* 需要重新注册 Authenticator 应用。
* 可能会迁移过时的数据。

Microsoft 提供了通信模板，你可以将这些模板提供给用户，以指导他们完成组合注册过程。 这些模板包括电子邮件、海报、桌面立牌和其他各种资产的模板。 用户可在 `https://aka.ms/mysecurityinfo` 上注册其信息，在此过程中他们会转到组合安全性注册屏幕。 

我们建议[使用条件访问保护安全性注册过程](../conditional-access/howto-conditional-access-policy-registration.md)，这需要从受信任的设备或位置进行注册。 有关跟踪注册状态的信息，请参阅 [Azure Active Directory 的身份验证方法活动](howto-authentication-methods-activity.md)。
> [!NOTE]
> 对于必须从不受信任的位置或设备注册其组合安全性信息的用户，可为其颁发临时访问密码，或者暂时将其从策略中排除。

### <a name="migrate-phone-numbers-from-mfa-server"></a>从 MFA 服务器迁移电话号码

尽管可以迁移用户的已注册 MFA 电话号码和硬件令牌，但无法迁移设备注册，例如用户的 Microsoft Authenticator 应用设置。 迁移电话号码可能导致迁移过时的号码，并很有可能会使用户保持使用基于电话的 MFA，而不是设置更安全的方法，例如[使用 Microsoft Authenticator 应用进行无密码登录](howto-authentication-passwordless-phone.md)。 因此，无论你选择哪种迁移路径，我们都建议让所有用户注册[组合安全性信息](howto-registration-mfa-sspr-combined.md)。 使用组合安全性信息，用户还可以注册自助式密码重置。

如果无法让用户注册其组合安全性信息，可以从 MFA 服务器中将用户连同其电话号码一起导出，然后将电话号码导入 Azure AD。 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>从 MFA 服务器导出用户电话号码 

1. 在 MFA 服务器上打开多重身份验证服务器管理控制台。 
1. 选择“文件” > “导出用户” 。
3)  保存 CSV 文件。 默认名称为 Multi-Factor Authentication Users.csv。

#### <a name="interpret-and-format-the-csv-file"></a>解释和格式化 .csv 文件

该 .csv 文件包含迁移过程中不需要用到的许多字段，在将电话号码导入 Azure AD 之前，需要编辑和格式化该文件。 

打开该 .csv 文件时，相关的列包括“用户名”、“主要电话”、“主要国家/地区代码”、“备用国家/地区代码”、“备用电话”和“备用分机”。 必须根据需要解释此数据并设置其格式。

#### <a name="tips-to-avoid-errors-during-import"></a>有关避免导入过程中出错的提示

* 在使用“身份验证方法”API 将电话号码导入 Azure AD 之前，需要修改 CSV 文件。 
* 建议将 .csv 文件简化为三列：UPN、PhoneType 和 PhoneNumber。 

  ![CSV 示例的屏幕截图。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* 确保导出的 MFA 服务器用户名与 Azure AD UserPrincipalName 匹配。 如果不匹配，请更新 CSV 文件中的用户名，使之与 Azure AD 中的对应值相匹配，否则将找不到相应的用户。

用户可能已在 Azure AD 中注册了电话号码。 使用“身份验证方法”API 导入电话号码时，必须确定是要覆盖现有电话号码，还是将导入的号码添加为备用电话号码。

以下 PowerShell cmdlet 采用你提供的 CSV 文件，并使用“身份验证方法”API 将导出的电话号码添加为每个 UPN 的电话号码。 必须将“myPhones”替换为 CSV 文件的名称。


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

有关管理用户身份验证方法的详细信息，请参阅[管理 Azure AD 多重身份验证的身份验证方法](howto-mfa-userdevicesettings.md)。

### <a name="add-users-to-the-appropriate-groups"></a>将用户添加到相应的组 

* 如果创建了新的条件访问策略，请将相应的用户添加到这些组。 
* 如果为声明规则创建了本地安全组，请将相应的用户添加到这些组。 
* 请仅在已将用户添加到相应的条件访问规则后，才将用户添加到为分阶段推出创建的组。 完成后，用户将开始使用你选择的 Azure 身份验证方法（PHS 或 PTA），并在需要执行多重身份验证时使用 Azure AD MFA。

> [!IMPORTANT] 
> 分阶段推出过程不支持嵌套组和动态组。 不要使用这种类型的组。 

建议不要重复使用已用于安全性的组。 因此，如果你正在使用某个安全组通过条件访问策略保护一组重要应用，该组应该只用于这种目的。

## <a name="monitoring"></a>监视

可以使用许多 [Azure Monitor 工作簿](../reports-monitoring/howto-use-azure-monitor-workbooks.md)以及使用情况和见解报告来监视部署。 可以在 Azure AD 导航窗格中的“监视”下找到这些工作簿。 

### <a name="monitoring-staged-rollout"></a>监视分阶段推出

在“[工作簿](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks)”部分，选择“公共模板”。 在“混合身份验证”部分下，选择“分阶段推出中的组、用户和登录”工作簿 。

此工作簿可用于监视以下各项： 
* 已添加到分阶段推出的用户和组。
* 已从分阶段推出中删除的用户和组。
* 分阶段推出中用户的登录失败情况，以及失败的原因。

### <a name="monitoring-azure-mfa-registration"></a>监视 Azure MFA 注册
可以使用[身份验证方法使用情况和见解报告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity)来监视 Azure MFA 注册。 可以在 Azure AD 中找到此报告。 依次选择“监视”、“使用情况和见解” 。 

![展示如何查找“使用情况和见解”报告的屏幕截图。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

在“使用情况和见解”中，选择“身份验证方法”。 

可以在“注册”选项卡上找到详细的 Azure MFA 注册信息。可以选择“已注册 Azure 多重身份验证的用户”超链接向下钻取，以查看已注册用户的列表。

![“注册”选项卡的屏幕截图。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>监视应用登录运行状况

使用“应用登录运行状况”工作簿或应用程序活动使用报告监视已转移到 Azure AD 的应用程序。

* “应用登录运行状况”工作簿。 有关使用此工作簿的详细指导，请参阅[监视应用程序登录运行状况以实现复原](../fundamentals/monitor-sign-in-health-for-resilience.md)。
* Azure AD 应用程序活动使用情况报告。 使用此 [报告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity) 可以查看各个应用程序的成功和失败登录，以及向下钻取和查看特定应用程序的登录活动。 

## <a name="clean-up-tasks"></a>清理任务

将所有用户移到 Azure AD 云身份验证和 Azure MFA 后，应准备好解除 MFA 服务器。 在删除服务器之前，建议查看 MFA 服务器日志，确保没有任何用户或应用程序正在使用该服务器。

### <a name="convert-your-domains-to-managed-authentication"></a>将域转换为托管身份验证

现在应[将 Azure AD 中的联合域转换为托管域](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed)，并删除分阶段推出配置。 这可以确保新用户在未被添加到迁移组的情况下就能使用云身份验证。

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>还原 AD FS 上的声明规则并删除 MFA 服务器身份验证提供程序

按照[配置声明规则以调用 Azure AD MFA](#configure-claims-rules-to-invoke-azure-ad-mfa) 中所述的步骤还原到已备份的声明规则，并删除所有 AzureMFAServerAuthentication 声明规则。 

例如，删除规则中的以下内容： 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>禁用用作 AD FS 中的身份验证提供程序的 MFA 服务器

此项更改可确保只将 Azure MFA 用作身份验证提供程序。

1. 打开 AD FS 管理控制台。
1. 在“服务”下，右键单击“身份验证方法”并选择“编辑多重身份验证方法”  。 
1. 清除“Azure 多重身份验证服务器”复选框。 

### <a name="decommission-the-mfa-server"></a>解除 MFA 服务器

遵循企业服务器解除过程删除环境中的 MFA 服务器。

解除 MFA 服务器时可能的注意事项包括： 

* 在删除服务器之前，建议查看 MFA 服务器日志，确保没有任何用户或应用程序正在使用该服务器。
* 通过服务器上的控制面板卸载多重身份验证服务器。
* （可选）在先备份留下的日志和数据目录之后将其清理掉。 
* 卸载多重身份验证 Web 服务器 SDK（如果适用），包括 inetpub\wwwroot\MultiFactorAuthWebServiceSdk 和/或 MultiFactorAuth 目录中留下的任何文件。
* 对于版本低于 8.0.x 的 MFA 服务器，可能还需要删除多重身份验证手机应用 Web 服务。

## <a name="move-application-authentication-to-azure-active-directory"></a>将应用程序身份验证移到 Azure Active Directory

如果将所有应用程序身份验证连同 MFA 和用户身份验证一起迁移，则可以消除本地基础结构的很大一部分，从而降低成本和风险。 如果移动所有应用程序身份验证，则可以跳过[准备 AD FS](#prepare-ad-fs) 阶段并简化 MFA 迁移。

下图显示了移动所有应用程序身份验证的过程。

![将应用程序迁移到 Azure AD MFA 的过程。](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

如果在迁移之前无法移动所有应用程序，请在开始之前移动你能够迁移的应用程序。
有关将应用程序迁移到 Azure 的详细信息，请参阅[用于将应用程序迁移到 Azure Active Directory 的资源](../manage-apps/migration-resources.md)。

## <a name="next-steps"></a>后续步骤

- [从 Microsoft MFA 服务器迁移到 Azure 多重身份验证（概述）](how-to-migrate-mfa-server-to-azure-mfa.md)
- [将应用程序从 Windows Active Directory 迁移到 Azure Active Directory](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [规划云身份验证策略](../fundamentals/active-directory-deployment-plans.md)