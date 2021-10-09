---
title: 使用电子邮件作为备用登录 ID 登录到 Azure AD
description: 了解如何允许用户使用其电子邮件作为备用登录 ID 登录到 Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/07/2021
ms.author: justinha
author: calui
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 0e925e520b5136750dc7ef9151d7e5741176525c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595610"
---
# <a name="sign-in-to-azure-ad-with-email-as-an-alternate-login-id-preview"></a>使用电子邮件作为备用登录 ID 登录到 Azure AD（预览版）

> [!NOTE]
> 使用电子邮件作为备用登录 ID 登录到 Azure AD 是 Azure Active Directory 提供的一项公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

许多组织希望让用户使用与其本地目录环境相同的凭据登录到 Azure Active Directory (Azure AD)。 使用此方法（称为混合身份验证），用户只需记住一组凭据。

一些组织尚未转换为使用混合身份验证，原因如下：

* Azure AD 用户主体名称 (UPN) 会默认设置为与本地 UPN 相同的值。
* 更改 Azure AD UPN 会在本地和 Azure AD 环境之间创建错误匹配，从而导致某些应用程序和服务出现问题。
* 出于业务或合规性原因，组织不希望使用本地 UPN 登录到 Azure AD。

为帮助转换为混合身份验证，现可将 Azure AD 配置为允许用户使用其电子邮件作为备用登录 ID 登录。 例如，如果 Contoso 的产品名称已更改为 Fabrikam，则可以使用电子邮件作为备用登录 ID 登录，而不是继续使用旧 `balas@contoso.com` UPN 登录。 若要访问应用程序或服务，用户应使用其非 UPN 电子邮件（如 `balas@fabrikam.com`）登录到 Azure AD。

本文介绍如何启用电子邮件并将其用作备用登录 ID。

## <a name="before-you-begin"></a>准备阶段

以下是有关将电子邮件用作备用登录 ID 的需知信息：

* Azure AD Free 版及更高版本提供该项功能。
* 此功能允许通过云身份验证的 Azure AD 用户使用已验证的域 ProxyAddresses 进行登录。
* 当用户使用非 UPN 电子邮件登录时，[ID 令牌](../develop/id-tokens.md)中的 `unique_name` 和 `preferred_username` 声明（若存在）将返回非 UPN 电子邮件的值。
* 此功能支持通过密码哈希同步 (PHS) 或直通身份验证 (PTA) 进行托管身份验证。
* 有两个选项可用于配置该项功能：
    * [主领域发现 (HRD) 策略](#enable-user-sign-in-with-an-email-address) - 使用此选项可为整个租户启用该功能。 需要全局管理员特权。
    * [分阶段推出策略](#enable-staged-rollout-to-test-user-sign-in-with-an-email-address) - 使用此选项可在特定 Azure AD 组测试该功能。 需要全局管理员特权。

## <a name="preview-limitations"></a>预览版限制

在当前预览状态下，用作备用登录 ID 的电子邮件包含以下限制：

* 用户体验 - 即使通过非 UPN 电子邮件登录，用户也可能会看到其 UPN。 可能会出现以下示例行为：
    * 当定向到使用 `login_hint=<non-UPN email>` 的 Azure AD 登录时，系统将提示用户使用 UPN 登录。
    * 当用户使用非 UPN 电子邮件登录并输入错误密码时，“输入密码”页将改为显示 UPN。
    * 在某些 Microsoft 网站和应用（例如 Microsoft Office）上，通常显示在右上角的“帐户管理器”控件可能会显示用户的 UPN，而不是用于登录的非 UPN 电子邮件。

* 支持的流 - 有些流当前与非 UPN 电子邮件尚不兼容，如下所示：
    * 标识保护不匹配可检测 *泄露凭据* 风险的非 UPN 电子邮件。 此风险检测使用 UPN 来匹配已泄漏的凭据。 有关详细信息，请参阅 [Azure AD 标识保护风险检测和修正][identity-protection]。
    * 不完全支持发送到非 UPN 电子邮件的 B2B 邀请。 接受发送到非 UPN 电子邮件的邀请后，资源租户终结点上的来宾用户可能无法使用非 UPN 电子邮件顺利登录。
    * 使用非 UPN 电子邮件登录的用户无法更改其密码。 Azure AD 自助式密码重置 (SSPR) 应会按预期执行。 在 SSPR 期间，如果用户通过备用电子邮件验证其身份，则用户可能会看到其 UPN。

* 不支持的方案 - 不支持以下场景。 使用非 UPN 电子邮件登录到：
    * [混合 Azure AD 加入设备](../devices/concept-azure-ad-join-hybrid.md)
    * [已加入 Azure AD 的设备](../devices/concept-azure-ad-join.md)
    * [Azure AD 注册设备](../devices/concept-azure-ad-register.md)
    * [无缝 SSO](../hybrid/how-to-connect-sso.md)
    * [使用资源所有者密码凭据 (ROPC) 的应用程序](../develop/v2-oauth-ropc.md)
    * 使用旧身份验证的应用程序，如 POP3 和 SMTP
    * Skype for Business
    * macOS 版 Microsoft Office
    * Web 上的 Microsoft Teams
    * OneDrive（登录流不涉及多重身份验证时）。
    * Microsoft 365 管理门户

* 不支持的应用 - 如果某些第三方应用程序假定 `unique_name` 或 `preferred_username` 声明不变或将始终匹配特定的用户属性（如 UPN），则这些应用程序可能无法按预期正常运作。

* 登录 - 对 HRD 策略中的功能配置所做的更改不会显式显示在审核日志中。 此外，登录日志中的“登录标识符类型”字段有时可能不正确，因此不应将其用于确定该功能是否已用于登录。

* 分阶段推出策略 - 仅当使用分阶段推出策略启用此功能时，以下限制才适用：
    * 对于包含在其他分阶段推出策略中的用户，该功能未按预期工作。
    * 分阶段推出策略的每项功能最多支持 10 个组使用。
    * 分阶段推出策略不支持嵌套组。
    * 分阶段推出策略不支持动态组。
    * 组内的联系人对象会阻止向分阶段推出策略添加组。

* 重复值 - 在租户中，仅云用户的 UPN 可能与另一个用户从本地目录同步的代理地址相同。 在此方案中，启用此功能后，仅云用户将无法使用其 UPN 登录。 有关此问题的更多内容，请参阅[故障排除](#troubleshoot)部分。

## <a name="overview-of-alternate-login-id-options"></a>备用登录 ID 选项概述
若要登录到 Azure AD，用户需输入唯一标识其帐户的名称。 以前只能使用 Azure AD UPN 作为登录标识符。

对于本地 UPN 是用户首选登录电子邮件的组织而言，此方法非常有用。 这些组织会将 Azure AD UPN 设置为与本地 UPN 完全相同的值，并且用户将具有一致的登录体验。

### <a name="alternate-login-id-for-ad-fs"></a>AD FS 的备用登录 ID

但是，有些组织不会将本地 UPN 用作登录标识符。 在本地环境中，可以将本地 AD DS 配置为允许使用备用登录 ID 进行登录。 最好不要选择将 Azure AD UPN 设置为与本地 UPN 相同的值，因为 Azure AD 会要求用户使用该值登录。

### <a name="alternate-login-id-in-azure-ad-connect"></a>Azure AD Connect 中的备用登录 ID

此问题的典型解决方法是将 Azure AD UPN 设置为用户希望使用其登录的电子邮件地址。 虽然这种方法是有效的，但在本地 AD 与 Azure AD 中使用 UPN 产生的结果并不相同，而且此配置与所有 Microsoft 365 工作负荷都不兼容。

### <a name="email-as-an-alternate-login-id"></a>将电子邮件用作备用登录 ID

另一种方法是将 Azure AD 和本地 UPN 同步到相同的值，然后将 Azure AD 配置为允许用户使用已验证的电子邮件登录到 Azure AD。 若要提供此功能，请在本地目录中用户的“ProxyAddresses”属性中定义一个或多个电子邮件地址。 然后，使用 Azure AD Connect 自动将“ProxyAddresses”同步到 Azure AD。


| 选项 | 说明 |
|---|---|
| [AD FS 的备用登录 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) | 允许 AD FS 用户使用备用属性（例如电子邮件）登录。 |
| [Azure AD Connect 中的备用登录 ID](../hybrid/plan-connect-userprincipalname.md#alternate-login-id) | 将备用属性（例如电子邮件）作为 Azure AD UPN 同步。 |
| 将电子邮件用作备用登录 ID | 允许 Azure AD 用户使用已验证的域 ProxyAddresses 登录。 |

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>将登录电子邮件地址同步到 Azure AD

传统 Active Directory 域服务 (AD DS) 或 Active Directory 联合身份验证服务 (AD FS) 的身份验证直接在网络上进行，并且由 AD DS 基础结构处理。 通过混合身份验证，用户可以改为直接登录到 Azure AD。

若要支持这种混合身份验证方法，请使用 [Azure AD Connect][azure-ad-connect] 将本地 AD DS 环境同步到 Azure AD，并将其配置为使用 PHS 或 PTA。 有关详细信息，请参阅[为 Azure AD 混合标识解决方案选择正确的身份验证方法][hybrid-auth-methods]。

在这两个配置选项中，用户将其用户名和密码提交到 Azure AD，Azure AD 验证凭据并颁发票证。 当用户登录到 Azure AD 时，组织无需再托管和管理 AD FS 基础结构。

Azure AD Connect 自动同步的用户属性之一是 ProxyAddresses。 如果用户在本地 AD DS 环境中定义了电子邮件地址，且该地址是 ProxyAddresses 属性的一部分，则该地址会自动同步到 Azure AD。 然后，可以将此电子邮件地址作为备用登录 ID 直接用于 Azure AD 登录过程。

> [!IMPORTANT]
> 仅租户的已验证域中的电子邮件会同步到 Azure AD。 每个 Azure AD 租户都有一个或多个已验证的域，你对这些域具有已验证的所有权，并且这些域唯一绑定到你的租户。
>
> 有关详细信息，请参阅[在 Azure AD 中添加和验证自定义域名][verify-domain]。

## <a name="enable-user-sign-in-with-an-email-address"></a>允许用户使用电子邮件地址登录

> [!NOTE]
> 此配置选项使用 HRD 策略。 有关详细信息，请参阅 [homeRealmDiscoveryPolicy 资源类型](/graph/api/resources/homeRealmDiscoveryPolicy?view=graph-rest-1.0&preserve-view=true)。

使用 Azure AD Connect 将应用了 ProxyAddresses 属性的用户同步到 Azure AD 之后，你需要启用该功能，以便用户能够使用电子邮件作为租户的备用登录 ID 进行登录。 此功能会告知 Azure AD 服务器不仅要检查 UPN 值的登录标识符，还要检查 ProxyAddresses 值的电子邮件地址。

在预览期间，当前只能使用 PowerShell 来启用使用电子邮件作为备用登录 ID 进行登录这一功能。 需要全局管理员权限，才能完成以下步骤：

1. 以管理员身份打开 PowerShell 会话，然后使用 [Install-Module][Install-Module] cmdlet 安装“AzureADPreview”模块：

    ```powershell
    Install-Module AzureADPreview
    ```

    如果出现提示，选择“Y”以安装 NuGet 或从不受信任的存储库进行安装。

1. 以全局管理员身份使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到你的 Azure AD 租户：

    ```powershell
    Connect-AzureAD
    ```

    该命令将返回有关你的帐户、环境和租户 ID 的信息。

1. 使用 [Get-AzureADPolicy][Get-AzureADPolicy] cmdlet 检查租户中是否存在 HomeRealmDiscoveryPolicy，如下所示：

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. 如果当前未配置任何策略，则该命令将不返回任何内容。 如果返回策略，请跳过此步骤，并转到下一步以更新现有策略。

    若要将 HomeRealmDiscoveryPolicy 策略添加到租户，请使用 [New-AzureADPolicy][New-AzureADPolicy] cmdlet，并将 AlternateIdLogin 属性设置为“"已启用": true”，如以下示例所示  ：

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    成功创建策略后，该命令将返回策略 ID，如以下示例输出所示：

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 如果已有配置的策略，请检查是否已启用 AlternateIdLogin  属性，如以下示例策略输出所示 ** ：

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    如果策略存在但 AlternateIdLogin 属性不存在或未启用，或者要保留的策略中存在其他属性，请使用 [Set-AzureADPolicy][Set-AzureADPolicy] cmdlet 更新现有策略。

    > [!IMPORTANT]
    > 更新策略时，请确保已包含任何旧设置和新 AlternateIdLogin 属性 ** 。

    以下示例添加了 AlternateIdLogin 属性，并保留了可能已设置的 AllowCloudPasswordValidation 属性 **  ** ：

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    确认更新后的策略显示了所做的更改且现已启用 AlternateIdLogin 属性：

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

应用策略后，最长可能需经过一小时，策略才能进行传播，用户才可使用其备用登录 ID 进行登录。

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>启用分阶段推出以测试用户使用电子邮件地址进行登录  

> [!NOTE]
>此配置选项使用分阶段推出策略。 有关详细信息，请参阅 [featureRolloutPolicy 资源类型](/graph/api/resources/featurerolloutpolicy?preserve-view=true&view=graph-rest-1.0)。

通过分阶段推出策略，租户管理员可以为特定 Azure AD 组启用相关功能。 建议租户管理员使用分阶段推出来测试用户使用电子邮件地址进行登录。 当管理员准备好将此功能部署到其整个租户时，其应当使用 [HRD 策略](#enable-user-sign-in-with-an-email-address)。  


需要全局管理员权限，才能完成以下步骤：

1. 以管理员身份打开 PowerShell 会话，然后使用 [Install-Module][Install-Module] cmdlet 安装“AzureADPreview”模块：

    ```powershell
    Install-Module AzureADPreview
    ```

    如果出现提示，选择“Y”以安装 NuGet 或从不受信任的存储库进行安装。

1. 以全局管理员身份使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到你的 Azure AD 租户：

    ```powershell
    Connect-AzureAD
    ```

    该命令将返回有关你的帐户、环境和租户 ID 的信息。

1. 使用以下 cmdlet 列出所有现有的分阶段推出策略：
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

1. 如果没有针对此功能的现有分阶段推出策略，请创建新的分阶段推出策略，并记下策略 ID：

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

1. 查找要添加到分阶段推出策略的组的 directoryObject ID。 请注意为“Id”参数返回的值，因为它将在下一步中使用。
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

1. 将组添加至分阶段推出策略，如以下示例所示。 将“-Id”参数中的值替换为步骤 4 中为策略 ID 返回的值，并将“-RefObjectId”参数中的值替换为步骤 5 中所述的“Id”。   最多可能需要经过一小时后，组中的用户才能使用电子邮件作为备用登录 ID 登录到 Azure AD。

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
对于添加到组的新成员，则可能需要最多 24 小时后才能使用电子邮件作为备用登录 ID 登录到 Azure AD。

### <a name="removing-groups"></a>删除组

若要从分阶段推出策略中删除组，请运行以下命令：

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>删除策略

若要删除分阶段推出策略，请先禁用该策略，然后将其从系统中删除：

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="test-user-sign-in-with-an-email-address"></a>测试用户是否能够通过电子邮件地址登录

若要测试用户能否通过电子邮件登录，请转到 [https://myprofile.microsoft.com][my-profile]，然后使用非 UPN 电子邮件（例如 `balas@fabrikam.com` ）进行登录。 此登录过程应与使用 UPN 登录非常相似。

## <a name="troubleshoot"></a>故障排除

如果用户在使用其电子邮件地址进行登录时遇到问题，请查看以下故障排除步骤：

1. 请确保自启用将电子邮件用作备用登录 ID 以来，至少已经过一小时。 如果用户最近已被添加到分阶段推出策略的相关组，请确保自将该用户添加到组以后，至少已经过 24 小时。
1. 如果使用 HRD 策略，请确认 Azure AD HomeRealmDiscoveryPolicy 的 AlternateIdLogin 定义属性已设置为 "Enabled": true，以及 IsOrganizationDefault *属性已设置为“属性设置为”* ：

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```
    如果使用分阶段推出策略，请确认 Azure AD FeatureRolloutPolicy 的 IsEnabled 属性已设置为“True”：

    ```powershell
    Get-AzureADMSFeatureRolloutPolicy
    ```
1. 请确保用户帐户已在 Azure AD ProxyAddresses 属性中设置电子邮件地址。

### <a name="conflicting-values-between-cloud-only-and-synced-users"></a>仅云用户与已同步用户之间的值发生冲突

在租户中，仅云用户的 UPN 值可能与另一个用户从本地目录同步的代理地址相同。 在此方案中，启用此功能后，仅云用户将无法使用其 UPN 登录。 以下提供检测此问题实例的相关步骤。

1. 以管理员身份打开 PowerShell 会话，然后使用 [Install-Module][Install-Module] cmdlet 安装“AzureADPreview”模块：

    ```powershell
    Install-Module AzureADPreview
    ```

    如果出现提示，选择“Y”以安装 NuGet 或从不受信任的存储库进行安装。

1. 以全局管理员身份使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到你的 Azure AD 租户：

    ```powershell
    Connect-AzureAD
    ```

1. 获取受影响的用户。

    ```powershell
    # Get all users
    $allUsers = Get-AzureADUser -All $true
    
    # Get list of proxy addresses from all synced users
    $syncedProxyAddresses = $allUsers |
        Where-Object {$_.ImmutableId} |
        Select-Object -ExpandProperty ProxyAddresses |
        ForEach-Object {$_ -Replace "smtp:", ""}
    
    # Get list of user principal names from all cloud-only users
    $cloudOnlyUserPrincipalNames = $allUsers |
        Where-Object {!$_.ImmutableId} |
        Select-Object -ExpandProperty UserPrincipalName
    
    # Get intersection of two lists
    $duplicateValues = $syncedProxyAddresses |
        Where-Object {$cloudOnlyUserPrincipalNames -Contains $_}
    ``` 

1. 输出受影响的用户：

    ```powershell
    # Output affected synced users
    $allUsers |
        Where-Object {$_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    
    # Output affected cloud-only users
    $allUsers |
        Where-Object {!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName} |
        Select-Object ObjectId, DisplayName, UserPrincipalName, ProxyAddresses, ImmutableId, UserType
    ```

1. 将受影响的用户输出到 CSV：

    ```powershell
    # Output affected users to CSV
    $allUsers |
        Where-Object {
            ($_.ImmutableId -And ($_.ProxyAddresses | Where-Object {($duplicateValues | ForEach-Object {"smtp:$_"}) -Contains $_}).Length -GT 0) -Or
            (!$_.ImmutableId -And $duplicateValues -Contains $_.UserPrincipalName)
        } |
        Select-Object ObjectId, DisplayName, UserPrincipalName, @{n="ProxyAddresses"; e={$_.ProxyAddresses -Join ','}}, @{n="IsSyncedUser"; e={$_.ImmutableId.Length -GT 0}}, UserType |
        Export-Csv -Path .\AffectedUsers.csv -NoTypeInformation
    ```

## <a name="next-steps"></a>后续步骤

若要了解有关混合标识（例如 Azure AD 应用代理或 Azure AD 域服务）的详细信息，请参阅[用于访问和管理本地工作负载的 Azure AD 混合标识][hybrid-overview]。

有关混合标识操作的详细信息，请参阅[密码哈希同步][phs-overview]或[传递身份验证][pta-overview]同步的工作原理。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
