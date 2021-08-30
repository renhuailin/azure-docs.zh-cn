---
title: 排查本地 Azure AD 密码保护问题
description: 了解如何为本地 Active Directory 域服务环境排查 Azure AD 密码保护问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3603bd100fe5d9b471d67896c98de4c0547c8ebb
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551247"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>故障排除：本地 Azure AD 密码保护

部署 Azure AD 密码保护后，可能需要进行故障排除。 本文详细介绍如何帮助你了解一些常见的故障排除步骤。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理无法在目录中找到代理

此问题的主要症状为 DC 代理管理事件日志中的 30017 事件。

此问题的常见原因是尚未注册代理。 如果已注册代理，可能会由于 AD 复制延迟而一直延迟到特定 DC 代理能够看到该代理为止。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理无法与代理通信

此问题的主要症状为 DC 代理管理事件日志中的 30018 事件。 此问题可能由多种原因造成：

1. DC 代理位于网络的隔离部分，该部分不允许与已注册的代理建立网络连接。 只要其他 DC 代理能够与该代理通信，从 Azure 下载密码策略，这个问题就有可能是良性的。 下载策略后，被隔离的 DC 将通过复制 sysvol 共享中的策略文件来获取这些策略。

1. 代理主机阻止访问 RPC 终结点映射程序终结点（端口 135）

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站规则，该规则允许访问端口 135。 如果之后删除或禁用此规则，DC 代理将无法与代理服务通信。 如果已禁用内置 Windows 防火墙，而使用另一个防火墙产品，则必须将该防火墙配置为允许访问端口 135。

1. 代理主机阻止访问代理服务侦听的 RPC 终结点（动态或静态）

   Azure AD 密码保护代理安装程序会自动创建 Windows 防火墙入站规则，该规则允许访问 Azure AD 密码保护代理服务侦听的任何入站端口。 如果之后删除或禁用此规则，DC 代理将无法与代理服务通信。 如果已禁用内置 Windows 防火墙，而使用另一个防火墙产品，则必须将该防火墙配置为允许访问 Azure AD 密码保护代理服务侦听的任何入站端口。 如果已将代理服务配置为侦听特定的静态 RPC 端口（使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet），则可以更具体地设置此配置。

1. 未将代理主机配置为允许域控制器登录到该主机。 可以通过“从网络访问此计算机”用户特权分配控制此行为。 必须为林中所有域中的所有域控制器授予此特权。 在较大型网络的强化工作中，通常会限制此设置。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>代理服务无法与 Azure 通信

1. 确保代理计算机与[部署要求](howto-password-ban-bad-on-premises-deploy.md)中列出的终结点建立连接。

1. 确保将林和所有代理服务器注册到同一 Azure 租户。

   你可以通过运行 `Get-AzureADPasswordProtectionProxy` 和 `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlet 来检查此要求，然后比较每个返回项的 `AzureTenant` 属性。 为了正确操作，所有 DC 代理和代理服务器上报告的租户名称必须相同。

   如果确实存在 Azure 租户注册不匹配的情况，可以通过以下方式解决此问题：根据需要运行 `Register-AzureADPasswordProtectionProxy` 和/或 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet，确保对所有注册使用同一 Azure 租户中的凭据。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理无法加密或解密密码策略文件

Azure AD 密码保护极度依赖于 Microsoft 密钥分发服务提供的加密和解密功能。 加密或解密失败可以表现为各种症状，并且有几个潜在的原因。

1. 确保在域中所有 Windows Server 2012 及更高版本的域控制器上，KDS 服务已启用且正常运行。

   默认情况下，KDS 服务的服务启动模式配置为“手动”（触发启动）。 此配置意味着客户端第一次尝试使用该服务时，它将按需启动。 若要让 Azure AD 密码保护正常工作，可以使用此默认服务启动模式。

   如果 KDS 服务启动模式已配置为“已禁用”，则必须修复此配置，Azure AD 密码保护才能正常工作。

   若要对此问题进行简单测试，可通过服务管理 MMC 控制台或使用其他管理工具（例如，从命令提示符控制台运行“net start kdssvc”）来手动启动 KDS 服务。 KDS 服务应成功启动并保持运行状态。

   KDS 服务无法启动的最常见的根本原因是，Active Directory 域控制器对象位于默认域控制器 OU 之外。 此配置既不受 KDS 服务支持，也不是 Azure AD 密码保护施加的限制。 若要解决此问题，请将域控制器对象移到默认域控制器 OU 下的某个位置。

1. 不兼容的 KDS 加密缓冲区格式变化（从 Windows Server 2012 R2 到 Windows Server 2016）

   Windows Server 2016 引入了 KDS 安全修补程序，可修改 KDS 加密缓冲区的格式；这些缓冲区有时在 Windows Server 2012 和 Windows Server 2012 R2 上无法解密。 反过来可行 - 在 Windows Server 2012 和 Windows Server 2012 R2 上经过 KDS 加密的缓冲区在 Windows Server 2016 及更高版本上始终可以成功解密。 如果 Active Directory 域中的域控制器混合运行这些操作系统，偶尔可能会报告 Azure AD 密码保护解密失败。 鉴于安全修补程序的性质，并且由于不确定哪个域控制器上的哪个 Azure AD 密码保护 DC 代理将在给定时间加密数据，因此，无法准确预测这些失败发生的时间或症状。

   除了不在 Active Directory 域中混合运行这些不兼容的操作系统外，此问题没有其他解决方法。 换句话说，要么只运行 Windows Server 2012 和 Windows Server 2012 R2 域控制器，要么只运行 Windows Server 2016 及更高版本的域控制器。

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC 代理认为林尚未注册

此问题的症状为 DC Agent\Admin 通道中记录的 30016 事件，其中部分内容为：

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

导致出现此问题的可能原因有两个。

1. 林确实尚未注册。 若要解决此问题，请按照[部署要求](howto-password-ban-bad-on-premises-deploy.md)中所述运行 Register-AzureADPasswordProtectionForest 命令。
1. 林已注册，但是 DC 代理无法解密林注册数据。 这种情况与上面列出的 [DC 代理无法加密或解密密码策略文件](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files)中的问题 2 的根本原因相同。 可通过一种简单的方法来证实此推测：仅在 Windows Server 2012 或 Windows Server 2012R2 域控制器上运行的 DC 代理上看到此错误，而在 Windows Server 2016 及更高版本的域控制器上运行的 DC 代理一切正常。 解决方法也相同：将所有域控制器升级到 Windows Server 2016 或更高版本。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>弱密码已被接受，但不应接受

此问题可能有多种原因。

1. DC 代理运行的公共预览软件版本已过期。 请参阅[公共预览版 DC 代理软件已过期](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)。

1. DC 代理无法下载策略或无法解密现有策略。 请检查上述主题中的可能原因。

1. 密码策略强制模式仍设置为“审核”。 如果此配置已经生效，请使用 Azure AD 密码保护门户将其重新配置为“强制”。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 密码策略已被禁用。 如果此配置已经生效，请使用 Azure AD 密码保护门户将其重新配置为启用。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

1. 未在域中的所有域控制器上安装 DC 代理软件。 在这种情况下，很难确保远程 Windows 客户端在更改密码的操作中瞄准特定域控制器。 如果你认为已成功瞄准安装了 DC 代理软件的特定 DC，可以通过仔细检查 DC 代理管理事件日志来进行验证：无论结果如何，至少会有一个事件记录密码验证的结果。 如果密码已更改的用户没有对应的事件，则表示密码更改可能是由其他域控制器处理的。

   作为一种替代测试，请在直接登录安装了 DC 代理软件的 DC 时，尝试设置\更改密码。 对于生产 Active Directory 域，不建议使用此方法。

   尽管支持 DC 代理软件的增量部署，但受这些限制的影响，Microsoft 强烈建议尽快在域中的所有域控制器上安装 DC 代理软件。

1. 密码验证算法实际上可能按预期方式工作。 请参阅[如何评估密码](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 无法设置 DSRM 弱密码

Active Directory 将始终验证新的目录服务修复模式密码，确保它符合域的密码复杂性要求；此验证还会调用诸如 Azure AD 密码保护之类的密码筛选器 dll。 如果新的 DSRM 密码被拒绝，则会出现以下错误消息：

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

当 Azure AD 密码保护为 Active Directory DSRM 密码记录密码验证事件日志事件时，预计事件日志消息将不包含用户名。 出现这种行为是因为 DSRM 帐户是本地帐户，而不是实际 Active Directory 域的一部分。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>由于使用 DSRM 弱密码，域控制器副本升级失败

在 DC 升级过程中，新的目录服务修复模式密码将提交到域中的现有 DC 进行验证。 如果新的 DSRM 密码被拒绝，则会出现以下错误消息：

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

就像上面的问题一样，在这种情况下，任何 Azure AD 密码保护密码验证结果事件都会出现空用户名。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>域控制器降级因本地管理员弱密码而失败

支持降级仍在运行 DC 代理软件的域控制器。 但管理员应注意，在降级过程中，DC 代理软件会继续实施当前密码策略。 将像验证其他任何密码一样验证（降级操作过程中指定的）新本地管理员帐户密码。 Microsoft 建议在 DC 降级过程中为本地管理员帐户选择安全密码。

成功降级后，如果域控制器已重新启动并再次以普通成员服务器的形式运行，则 DC 代理软件将恢复为以被动模式运行。 然后，随时可将其卸载。

## <a name="booting-into-directory-services-repair-mode"></a>启动进入目录服务修复模式

如果域控制器启动进入目录服务修复模式，则无论当前处于活动状态的策略配置是什么，DC 代理密码筛选器 dll 都会检测此情况，并导致所有密码验证或实施活动被禁用。 DC 代理密码筛选器 dll 会在管理事件日志中记录 10023 警告事件，例如：

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>公开预览版 DC 代理软件已过期

在 Azure AD 密码保护公共预览期间，DC 代理软件进行了硬编码，以便在以下日期停止处理密码验证请求：

* 版本 1.2.65.0 将于 2019 年 9 月 1 日停止处理密码验证请求。
* 1\.2.25.0 及以前的版本已于 2019 年 7 月 1 日停止处理密码验证请求。

随着截止日期的临近，所有有时间限制的 DC 代理版本都会在启动时在 DC 代理管理事件日志中发出 10021 事件，如下所示：

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

截止日期过后，所有有时间限制的 DC 代理版本都会在启动时在 DC 代理管理事件日志中发出 10022 事件，如下所示：

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

由于仅在首次启动时检查了截止日期，因此可能在日历截止日期过后很久才会看到这些事件。 一旦识别到截止日期，除了自动批准所有密码外，不会对域控制器或较大环境产生任何负面影响。

> [!IMPORTANT]
> Microsoft 建议立即将过期的公共预览版 DC 代理升级到最新版本。

可通过一种简单的方法发现环境中需要升级的 DC 代理，即，运行 `Get-AzureADPasswordProtectionDCAgent` cmdlet，例如：

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

在本主题中，SoftwareVersion 字段显然是要查看的关键属性。 你还可以使用 PowerShell 筛选功能筛选出已达到或高于所需基准版本的 DC 代理，例如：

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

在任何版本中，Azure AD 密码保护代理软件均不受时间限制。 Microsoft 仍然建议 DC 和代理都应升级到发布的最新版本。 `Get-AzureADPasswordProtectionProxy` cmdlet 可用于查找需要升级的代理，类似于上面的 DC 代理示例。

有关具体升级过程的更多详细信息，请参阅[升级 DC 代理](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)和[升级代理服务](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)。

## <a name="emergency-remediation"></a>紧急补救

如果 DC 代理服务造成了问题，可以立即关闭 DC 代理服务。 DC 代理密码筛选器 dll 仍会尝试调用未运行的服务并记录警告事件（10012、10013），但在此期间会接受所有传入的密码。 然后，也可以通过 Windows 服务控制管理器并根据需要使用启动类型“Disabled”来配置 DC 代理服务。

另一种补救措施是在 Azure AD 密码保护门户中将“启用”模式设置为“否”。 下载更新的策略后，各 DC 代理服务将进入静默模式，在这种模式下，将按原样接受所有密码。 有关详细信息，请参阅[操作模式](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)。

## <a name="removal"></a>删除

如果决定卸载 Azure AD 密码保护软件并从域和林中清理所有相关状态，可使用以下步骤完成此任务：

> [!IMPORTANT]
> 必须按顺序执行这些步骤。 如果代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象。 如果 DC 代理服务的任何实例仍在运行，它会定期重新创建其 serviceConnectionPoint 对象和 sysvol 状态。

1. 从所有计算机中卸载代理软件。 完成此步骤后 **不需要** 重新启动。
2. 从所有域控制器中卸载 DC 代理软件。 完成此步骤后 **需要** 重新启动。
3. 在每个域命名上下文中手动删除所有代理服务连接点。 可使用以下 Active Directory PowerShell 命令发现这些对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   不要省略 $keywords 变量值末尾的星号（“*”）。

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

4. 在每个域命名上下文中手动删除所有 DC 代理连接点。 根据软件的部署范围，林中的每个域控制器可能存在其中的一个对象。 可使用以下 Active Directory PowerShell 命令发现该对象的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   然后，可通过管道将 `Get-ADObject` 命令找到的结果对象传送到 `Remove-ADObject`，或手动将其删除。

   不要省略 $keywords 变量值末尾的星号（“*”）。

5. 手动删除林级配置状态。 林配置状态保留在 Active Directory 配置命名上下文中的某个容器内。 可按如下所示发现和删除该状态：

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 通过手动删除以下文件夹及其所有内容，手动删除 sysvol 相关的所有状态：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   如果需要，也可以在给定域控制器的本地访问此路径；默认位置类似于以下路径：

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   如果在非默认位置配置了 sysvol 共享，则此路径不同。

## <a name="health-testing-with-powershell-cmdlets"></a>使用 PowerShell cmdlet 进行运行状况测试

AzureADPasswordProtection PowerShell 模块包括两个与运行状况相关的 cmdlet，这些 cmdlet 可对软件是否已安装且正常运行进行基本验证。 建议在设置新部署之后运行这些 cmdlet（此后定期运行），以及在调查问题时运行这些 cmdlet。

每个运行状况测试都会返回基本的“通过”或“失败”结果，以及失败时的可选消息。 如果无法确定失败的原因，请查找可能解释该失败的错误事件日志消息。 启用文本日志消息可能也很有用。 有关更多详细信息，请参阅[监视 Azure AD 密码保护](howto-password-ban-bad-on-premises-monitor.md)。

## <a name="proxy-health-testing"></a>代理运行状况测试

Test-AzureADPasswordProtectionProxyHealth cmdlet 支持两个可以单独运行的运行状况测试。 第三种模式允许运行所有不需要参数输入的测试。

### <a name="proxy-registration-verification"></a>代理注册验证

此测试验证代理是否已在 Azure 上正确注册，并且能够向 Azure 进行身份验证。 成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Passed
```

如果检测到错误，测试将返回“失败”结果和可选的错误消息。 下面是一个可能失败的示例：

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Failed No proxy certificates were found - please run the Register-AzureADPasswordProtectionProxy cmdlet to register the proxy.
```

### <a name="proxy-verification-of-end-to-end-azure-connectivity"></a>端到端 Azure 连接的代理验证

此测试是 -VerifyProxyRegistration 测试的超集。 它要求代理在 Azure 上正确注册，能够向 Azure 进行身份验证，最后还添加一项检查，确保可以向 Azure 成功发送消息，从而验证完整的端到端通信是否正常。

成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyAzureConnectivity

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyAzureConnectivity Passed
```

### <a name="proxy-verification-of-all-tests"></a>所有测试的代理验证

此模式允许批量运行 cmdlet 支持的所有不需要参数输入的测试。 成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -TestAll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyTLSConfiguration  Passed
VerifyProxyRegistration Passed
VerifyAzureConnectivity Passed
```

## <a name="dc-agent-health-testing"></a>DC 代理运行状况测试

Test-AzureADPasswordProtectionDCAgentHealth cmdlet 支持多个可以单独运行的运行状况测试。 第三种模式允许运行所有不需要参数输入的测试。

### <a name="basic-dc-agent-health-tests"></a>基本 DC 代理运行状况测试

以下测试都可以单独运行，并且不接受参数。 下表列出了每个测试的简要说明。

|DC 代理运行状况测试|说明|
| --- | :---: |
|-VerifyPasswordFilterDll|验证密码筛选器 dll 当前是否已加载并能够调用 DC 代理服务|
|-VerifyForestRegistration|验证林当前是否已注册|
|-VerifyEncryptionDecryption|使用 Microsoft KDS 服务验证基本加密和解密是否正常工作|
|-VerifyDomainIsUsingDFSR|验证当前域是否正在使用 DFSR 进行 sysvol 复制|
|-VerifyAzureConnectivity|使用任意可用代理验证与 Azure 的端到端通信是否正常|

下面是 -VerifyPasswordFilterDll 测试通过的示例；其他测试成功时会显示相似的内容：

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyPasswordFilterDll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyPasswordFilterDll Passed
```

### <a name="dc-agent-verification-of-all-tests"></a>所有测试的 DC 代理验证

此模式允许批量运行 cmdlet 支持的所有不需要参数输入的测试。 成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -TestAll

DiagnosticName             Result AdditionalInfo
--------------             ------ --------------
VerifyPasswordFilterDll    Passed
VerifyForestRegistration   Passed
VerifyEncryptionDecryption Passed
VerifyDomainIsUsingDFSR    Passed
VerifyAzureConnectivity    Passed
```

### <a name="connectivity-testing-using-specific-proxy-servers"></a>使用特定代理服务器的连接测试

许多故障排除情况都涉及调查 DC 代理与代理之间的网络连接。 有两种运行状况测试可专门针对此类问题。 这些测试要求指定特定的代理服务器。

#### <a name="verifying-connectivity-between-a-dc-agent-and-a-specific-proxy"></a>验证 DC 代理与特定代理之间的连接

此测试验证从 DC 代理到代理的第一个通信分支上的连接性。 它验证代理是否收到调用，但不涉及与 Azure 的通信。 成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Passed
```

下面是一个失败情况示例，其中目标服务器上运行的代理服务已停止：

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Failed The RPC endpoint mapper on the specified proxy returned no results; please check that the proxy service is running on that server.
```

#### <a name="verifying-connectivity-between-a-dc-agent-and-azure-using-a-specific-proxy"></a>验证 DC 代理与 Azure 之间的连接（使用特定代理）

此测试使用特定代理验证 DC 代理与 Azure 之间的完整端到端连接。 成功的运行如下所示：

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyAzureConnectivityViaSpecificProxy bpl2.bpl.com

DiagnosticName                          Result AdditionalInfo
--------------                          ------ --------------
VerifyAzureConnectivityViaSpecificProxy Passed
```

## <a name="next-steps"></a>后续步骤

[Azure AD 密码保护的常见问题解答](howto-password-ban-bad-on-premises-faq.yml)

有关全局和自定义禁止密码列表的详细信息，请参阅[禁止错误的密码](concept-password-ban-bad.md)一文
