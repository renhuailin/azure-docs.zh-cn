---
title: Azure AD Connect：版本发布历史记录 | Microsoft Docs
description: 本文列出 Azure AD Connect 和 Azure AD Sync 的所有版本。
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/26/2021
ms.subservice: hybrid
ms.author: billmath
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3336ead4259c12b2eb6f7f87d5e21fe39765e817
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968789"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect：版本发行历史记录
Azure Active Directory (Azure AD) 团队会定期更新 Azure AD Sync 的新特性和功能。 并非所有的新增内容都适用于所有受众。

本文旨在帮助你跟踪已发布的版本，并了解最新版本中的具体变化。

下表列出了相关主题：

主题 |  详细信息
--------- | --------- |
从 Azure AD Connect 升级的步骤 | [从旧版升级到最新版](how-to-upgrade-previous-version.md) Azure AD Connect 的不同方法。
所需的权限 | 有关应用更新时所需的权限，请参阅[帐户和权限](reference-connect-accounts-permissions.md#upgrade)。


>[!IMPORTANT]
> 2022 年 8 月 31 日，Azure Active Directory (Azure AD) Connect 的所有 1.x 版本都将停用，因为它们包含不再受支持的 SQL Server 2012 组件。 请在该日期之前升级到最新版的 Azure AD Connect（2.x 版本），或者[评估并切换到 Azure AD 云同步](https://docs.microsoft.com/azure/active-directory/cloud-sync/what-is-cloud-sync)。
> 
> 需要确保运行最新版本的 Azure AD Connect，才能获得最佳支持体验。 
> 
> 如果运行已停用的 Azure AD Connect 版本，它可能会意外停止工作，并且你可能无法获得最新的安全修补程序、性能改进、故障排除和诊断工具以及服务增强功能。 此外，如果需要支持，我们可能无法为你提供组织所需的服务级别。
> 
> 请转到此文，详细了解 [Azure Active Directory Connect V2.0](whatis-azure-ad-connect-v2.md)、V2.0 中的更改以及此更改对你的影响。
>
> 请参阅[本文](./how-to-upgrade-previous-version.md)，详细了解如何将 Azure AD Connect 升级到最新版本。
>
> 有关已停用版本的版本历史记录信息，请参阅 [Azure AD Connect 版本发行历史记录存档](reference-connect-version-history-archive.md)

>[!NOTE]
>发布新版 Azure AD Connect 的过程需要采取多个质量控制的步骤来确保服务操作的功能，在执行此过程时，新版本的版本号和发布状态会进行更新，以反映最新状态。
并非所有版本的 Azure AD Connect 都可用于自动升级。 版本状态将指示版本是否可用于自动升级或仅供下载。 如果在 Azure AD Connect 服务器上启用了自动升级，那么该服务器将自动升级到针对自动升级发布的最新版 Azure AD Connect。 请注意，并非所有 Azure AD Connect 配置都有资格进行自动升级。 

>为了阐明自动升级的使用，它旨在推送所有重要的更新和关键修复程序。 这不一定是最新版本，因为并非所有版本都要求/包括对严重安全问题的修复（仅示出许多示例中的一个）。 关键问题通常可通过自动升级所提供的新版本来解决。 如果没有此类问题，则不会使用自动升级来推送更新，一般情况下，如果使用的是最新的自动升级版本，那就不会有问题。
但是，如果想要了解所有最新的功能和更新，最好的办法就是查看此页，并根据需要安装它们。 

>请点击此链接阅读有关[自动升级](how-to-connect-install-automatic-upgrade.md)的详细信息


## <a name="download-links"></a>下载链接
如果使用的是 Windows Server 2016 或更高版本，则应使用 Azure AD Connect V2.0。 可使用[此链接](https://www.microsoft.com/en-us/download/details.aspx?id=47594)下载最新版的 Azure AD Connect 2.0。
如果仍在使用较旧版本的 Windows Server，则应使用 Azure AD Connect V1.6。 可使用[此链接](https://www.microsoft.com/download/details.aspx?id=103336)下载最新版的 Azure AD Connect 1.6

## <a name="20100"></a>2.0.10.0

>[!NOTE] 
>这是 Azure AD Connect 的修补程序更新版本。 此功能需要 Windows Server 2016 或更高版本。 此修补程序解决了同时存在于 Azure AD Connect 2.0 和 1.6 版本中的一个问题。 如果要在较旧的 Windows Server 上运行 Azure AD Connect，应改为安装 [1.6.13.0](#16130)。

### <a name="release-status"></a>版本状态
2021/8/19：发布仅供下载的版本，不可用于自动升级。

### <a name="bug-fixes"></a>Bug 修复

 - 修复了以下 bug：对域进行重命名时，密码哈希同步将失败，事件日志中的错误指示“指定的强制转换无效”。 这是来自早期版本的一个回归。

## <a name="16130"></a>1.6.13.0
>[!NOTE] 
>这是 Azure AD Connect 的修补程序更新版本。 此版本适用于在使用 Windows Server 2012 或 2012 R2 的服务器上运行 Azure AD Connect 的客户。

2021/8/19：发布仅供下载的版本，不可用于自动升级。

### <a name="bug-fixes"></a>Bug 修复

 - 修复了以下 bug：对域进行重命名时，密码哈希同步将失败，事件日志中的错误指示“指定的强制转换无效”。 这是来自早期版本的一个回归。

### <a name="functional-changes"></a>功能性更改
该版本中没有功能性更改

## <a name="2090"></a>2.0.9.0

### <a name="release-status"></a>版本状态
8/17/2021：发布仅供下载的版本，不可用于自动升级。

### <a name="bug-fixes"></a>Bug 修复
>[!NOTE] 
>这是 Azure AD Connect 的修补程序更新版本。 此功能需要 Windows Server 2016 或更高版本。 此版本解决了版本 2.0.8.0 中存在的一个问题，Azure AD Connect 版本 1.6 中不存在此问题

 - 我们修复了一个 bug，即在同步大量密码哈希同步事务时，事件日志条目长度会超出密码哈希同步事件条目允许的最大长度。 现在我们将较长的日志条目拆分为多个条目。

## <a name="2080"></a>2.0.8.0
>[!NOTE] 
>这是 Azure AD Connect 的安全更新版本。 此功能需要 Windows Server 2016 或更高版本。 如果使用较旧版本的 Windows Server，请使用[版本 1.6.11.3](#16113)。
>此版本解决了[此 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) 中所述的漏洞。 有关此漏洞的详细信息，请参阅 CVE。
>可使用[此链接](https://www.microsoft.com/en-us/download/details.aspx?id=47594)下载最新版的 Azure AD Connect 2.0。

### <a name="release-status"></a>版本状态
8/10/2021：发布仅供下载的版本，不可用于自动升级。 

### <a name="functional-changes"></a>功能性更改
该版本中没有功能性更改

## <a name="16113"></a>1.6.11.3 
>[!NOTE] 
>这是 Azure AD Connect 的安全更新版本。 此版本旨在供客户运行较旧版本的 Windows Server，并且目前无法将其服务器升级到 Windows Server 2016 或更高版本的客户使用。 不能使用此版本更新 Azure AD Connect V2.0 服务器。
>此版本解决了[此 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) 中所述的漏洞。 有关此漏洞的详细信息，请参阅 CVE。
>可使用[此链接](https://www.microsoft.com/download/details.aspx?id=103336)下载最新版的 Azure AD Connect 1.6

### <a name="release-status"></a>版本状态
8/10/2021：发布仅供下载的版本，不可用于自动升级。

### <a name="functional-changes"></a>功能性更改
该版本中没有功能性更改

## <a name="2030"></a>2.0.3.0
>[!NOTE] 
>这是 Azure AD Connect 的主要版本。 有关更多详细信息，请参阅 [Azure Active Directory V2.0 一文](whatis-azure-ad-connect-v2.md)。

### <a name="release-status"></a>版本状态
7/20/2021：发布仅供下载的版本，不可用于自动升级
### <a name="functional-changes"></a>功能性更改
 - 我们已将 SQL Server 的 LocalDB 组件升级到 SQL 2019。 
 - 此版本需要 Windows Server 2016 或更高版本，这是 SQL Server 2019 的要求。 请注意，不支持在 Azure AD Connect 服务器上就地升级 Windows 服务器，因此可能需要使用[交叉迁移](how-to-upgrade-previous-version.md#swing-migration)。
 -  在此版本中，我们强制使用 TLS 1.2。 如果对 Windows Server 启用了 TLS 1.2，则 AADConnect 会使用此协议。 如果在服务器上未启用 TLS 1.2，则你会在尝试安装 AADConnect 时看到错误消息，并且在启用 TLS 1.2 之前，安装不会继续。 请注意，你可以使用新的“Set-ADSyncToolsTls12”cmdlet 在服务器上启用 TLS 1.2。
 -  在此版本中，可以在安装 Azure AD Connect 时使用具有用户角色“混合标识管理员”的用户进行身份验证。 不再需要对此使用全局管理员角色。
 - 我们已将 Visual C++ 运行时库升级到版本 14（这是 SQL Server 2019 的先决条件）  
 -  此版本使用 MSAL 库进行身份验证，我们已删除较旧的 ADAL 库（将在 2022 年停用）。   
 -  按照 Windows 安全指南，我们不再对 AdminSDHolders 应用权限。 在 ADSyncConfig.psm1 模块中，我们已将参数“SkipAdminSdHolders”更改为“IncludeAdminSdHolders”。
 -  当过期密码为“未过期”时，现在会重新评估密码，无论密码本身是否已更改。 如果对于用户，密码设置为“下次登录时必须更改密码”，并清除了此标志（因而使密码“未过期”），则“未过期”状态和密码哈希会同步到 Azure AD，当用户尝试在 Azure AD 中登录时，他们可以使用未过期的密码。
若要将过期密码从 Active Directory 同步到 Azure Active Directory，请使用 Azure AD Connect 中的[同步临时密码](how-to-connect-password-hash-synchronization.md#synchronizing-temporary-passwords-and-force-password-change-on-next-logon)功能。 请注意，你需要启用密码写回才能使用此功能，因此，用户更新的密码也会写回到 Active Directory。
 - 我们已将两个新 cmdlet 添加到 ADSyncTools 模块，以便从 Windows Server 启用或检索 TLS 1.2 设置。 
   - Get-ADSyncToolsTls12
   - Set-ADSyncToolsTls12
   
可以使用这些 cmdlet 检索 TLS 1.2 启用状态，或根据需要进行设置。 请注意，必须在服务器上启用 TLS 1.2，AADConnect 的安装才能成功。

 -  我们通过几个新的和改进的 cmdlet 改造了 ADSyncTools。 [ADSyncTools 一文](reference-connect-adsynctools.md)包含有关这些 cmdlet 的更多详细信息。 
  新增或更新了以下 cmdlet 
    - Clear-ADSyncToolsMsDsConsistencyGuid  
    - ConvertFrom-ADSyncToolsAadDistinguishedName   
    - ConvertFrom-ADSyncToolsImmutableID    
    - ConvertTo-ADSyncToolsAadDistinguishedName 
    - ConvertTo-ADSyncToolsCloudAnchor  
    - ConvertTo-ADSyncToolsImmutableID  
    - Export-ADSyncToolsAadDisconnectors    
    - Export-ADSyncToolsObjects 
    - Export-ADSyncToolsRunHistory  
    - Get-ADSyncToolsAadObject  
    - Get-ADSyncToolsMsDsConsistencyGuid    
    - Import-ADSyncToolsObjects 
    - Import-ADSyncToolsRunHistory  
    - Remove-ADSyncToolsAadObject   
    - Search-ADSyncToolsADobject    
    - Set-ADSyncToolsMsDsConsistencyGuid    
    - Trace-ADSyncToolsADImport 
    - Trace-ADSyncToolsLdapQuery    
-   现在，我们使用 V2 终结点进行导入和导出，并修复了 Get-ADSyncAADConnectorExportApiVersion cmdlet 中的问题。 可在 [Azure AD Connect 同步 V2 终结点一文](how-to-connect-sync-endpoint-api-v2.md)中了解有关 V2 终结点的详细信息。
-   我们添加了以下新用户属性，以从本地 AD 同步到 Azure AD 
    - employeeType  
    - employeeHireDate  
-   此版本需要在 Windows Server 上安装 PowerShell 版本 5.0 或更高版本。 请注意，此版本是 Windows Server 2016 和更高版本的一部分。   
-   我们通过新 V2 终结点将组同步成员身份限制增加到了 250k。
-   我们将泛型 LDAP 连接器和泛型 SQL 连接器更新到了最新版本。 在下面阅读有关这些连接器的详细信息：
    - [泛型 LDAP 连接器参考文档](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap)
    - [泛型 SQL 连接器参考文档](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql)
-   在 M365 管理中心，每当对 Azure AD 进行导出活动时，我们现在都会报告 AADConnect 客户端版本。 这可确保 M365 管理中心始终具有最新 AADConnect 客户端版本，并且在使用过时版本时可以检测到

### <a name="bug-fixes"></a>Bug 修复
- 我们修复了一个辅助功能 bug，其中屏幕阅读器宣布“了解详细信息”链接的角色不正确。
-   我们修复了一个 bug，其中具有大优先级值（例如 387163089）的同步规则会导致升级失败。 我们更新了存储过程“mms_UpdateSyncRulePrecedence”，以便在递增值之前将优先级数字强制转换为整数。
-   我们修复了一个 bug，其中如果导入组写回配置，则不会在同步帐户上设置组写回权限。 如果对导入的配置启用了组写回，我们现在会设置组写回权限。
-   我们将 Azure AD Connect Health 代理版本更新到 3.1.110.0 以修复安装失败。
-   在配置目录扩展属性的导出配置中，我们遇到了非默认属性的问题。 在将这些配置导入新的服务器/安装时，属性包含列表会被目录扩展配置步骤所替代，因此在同步服务管理器中选择仅导入默认值和目录扩展属性后，安装中不会包含非默认属性，因此，如果用户希望导入的同步规则正常工作，则必须从同步服务管理器手动重新启用这些属性。 现在，我们会在配置目录扩展之前刷新 AAD 连接器，以保留属性包含列表中的现有属性。
-   我们修复了一个辅助功能问题，其中页眉的字体粗细设置为“细”。 对于页面标题，字体粗细现在设置为“粗体”，这适用于所有页面的页眉。
-   ADSyncSingleObjectSync.ps1 中的 Get-AdObject 函数重命名为 Get-AdDirectoryObject，以防止 AD cmdlet 出现歧义。
-   SQL 函数“mms_CheckSynchronizationRuleHasUniquePrecedence”允许不同连接器上的出站同步规则具有重复的优先级。 我们删除了允许存在重复规则优先级的条件。
-   我们修复了一个 bug，其中如果属性流数据为空（例如在导出删除操作中），则单对象同步 cmdlet 会失败  
-   我们修复了一个 bug，其中因为无法启动 ADSync 启动服务，安装会失败。 现在，我们先将同步服务帐户添加到本地内置用户组，然后再开始启动服务。
-   我们修复了一个辅助功能问题，其中 AAD Connect 向导上的活动选项卡在高对比度主题中未显示正确的颜色。 由于常规颜色代码配置中缺少条件，因此覆盖了选定颜色代码。
-   我们解决了一个问题，其中允许用户使用 UI 和 PowerShell 取消选择同步规则中使用的对象和属性。 如果尝试取消选择任何同步规则中使用的任何属性或对象，我们现在会显示友好的错误消息。
-   我们对“迁移设置代码”进行了一些更新，以便在较旧版本的 Azure AD Connect 上运行脚本时检查并修复后向兼容性问题。  
-   修复了一个 bug，其中当 PHS 尝试查找不完整的对象时，它用于解析 DC 的算法与最初用于提取密码的算法不同。 具体而言，它会忽略关联 DC 信息。 不完整的对象查找应使用相同逻辑来查找两个实例中的 DC。
-   我们修复了一个 bug，其中 AADConnect 无法使用 Microsoft Graph 读取应用程序代理项，因为直接基于 AAD 连接客户端 id 调用 Microsoft Graph 发生权限问题。为了修复此问题，我们删除了 Microsoft Graph 的依赖项，改为使用 AAD PowerShell 处理应用代理应用程序对象。
-   我们从“Out to AD - Group SOAInAAD Exchange”同步规则中删除了写回成员限制  
-   我们修复了一个 bug，其中在更改连接器帐户权限时，如果某个对象在自上次增量导入以来未更改的范围内，则增量导入不会导入它。 现在，我们会显示警告，提醒用户注意该问题。
-   我们修复了屏幕阅读器未阅读单选钮位置的辅助功能问题。 我们将位置文本添加到单选按钮辅助功能文本字段。
-   我们更新了直通身份验证代理捆绑包。 在 US Gov 中，较旧的捆绑包没有适用于 HIP 第一方应用程序的正确答复 URL。  
-   我们修复了一个 bug，其中在使用现有数据库全新安装 AADConnect 版本 1.6.X.X（默认为使用 DirSyncWebServices API V2）后，AAD 连接器导出上会出现“stopped-extension-dll-exception”。 以前将导出版本设置为 v2 只是为了进行升级，我们进行了更改，以便在全新安装时也进行设置。
-   “ADSyncPrep.psm1”模块不再进行使用，已从安装中删除。

### <a name="known-issues"></a>已知问题
-   AADConnect 向导将“导入同步设置”选项显示为“预览版”，而此功能已正式发布。
-   使用迁移设置脚本的输出安装产品时，某些 Active Directory 连接器可能会按不同顺序进行安装。 
-   Azure AD Connect 向导中的“用户登录”选项页提到“公司管理员”。 此术语不再使用，需要替换为“全局管理员”。
-   当“登录”选项已配置为使用 PingFederate 时，“导出设置”选项会受损。
-   尽管 Azure AD Connect 现在可以使用混合标识管理员角色进行部署，但配置自助服务密码重置仍需要具有全局管理员角色的用户。
-   在部署期间导入 AADConnect 配置以便与原始 AADConnect 配置之外的不同租户进行连接时，目录扩展属性配置不正确。

## <a name="1640"></a>1.6.4.0

>[!NOTE]
> Azure AD Connect 同步 V2 终结点 API 目前适用于以下 Azure 环境：
> - Azure 商业版
> - Azure 中国云
> - Azure 美国政府云
> - 此版本会在 Azure 德国云中不可用

### <a name="release-status"></a>版本状态
3/31/2021：发布仅供下载的版本，不可用于自动升级

### <a name="bug-fixes"></a>Bug 修复
- 此版本修正版本 1.6.2.4 中的一个 bug，即，升级到该版本后，未正确注册 Azure AD Connect Health 功能，该功能无法正常工作。 已部署版本 1.6.2.4 的客户需要使用此版本更新其 Azure AD Connect 服务器，这将正确注册 Health 功能。 

## <a name="1624"></a>1.6.2.4
>[!IMPORTANT]
> 2021 年 3 月 30 日更新：我们在此版本中发现一个问题。 安装此版本后，未注册 Health 服务。 建议不要安装此版本， 我们很快就会发布修补程序。
> 如果已安装此版本，你可使用[此文](./how-to-connect-health-agent-install.md#manually-register-azure-ad-connect-health-for-sync)中所示的 cmdlet 手动注册 Health 服务

>[!NOTE]
> - 此版本仅可供下载。
> - 升级到此版本将需要完全同步，因为同步规则发生了更改。
> - 此版本将 AADConnect 服务器默认设置为新的 V2 终结点。 请注意，此终结点在德国国家云中不受支持，如果需要在此环境中部署此版本，你需要遵循[这些说明](./how-to-connect-sync-endpoint-api-v2.md#rollback)来切换回 V1 终结点。 否则，会导致同步错误。

### <a name="release-status"></a>版本状态
3/19/2021：发布供下载的版本，不可用于自动升级

### <a name="functional-changes"></a>功能性更改

 - 更新了默认同步规则，以将写回组中的成员身份限制为 50k 成员。
   - 添加了新的默认同步规则，用于限制组写回（Out to AD - 组写回成员限制）和组同步到 Azure Active Directory（Out to ADD - 组补写成员限制）组中的成员身份计数。
   - 将成员属性添加到“Out to AD - Group SOAInAAD - Exchange”规则，以将写回组中的成员限制为 50 k
 - 更新了同步规则以支持组写回 v2 - 如果克隆了“In from AAD - Group SOAInAAD”规则并升级 AADConnect。
     - 默认情况下，会禁用更新的规则，因此 targetWritebackType 将为 null。
     - AADConnect 将写回所有云组（包括为写回启用的 Azure Active Directory 安全组）作为分发组。
   -如果克隆了“Out to AD - Group SOAInAAD”规则并升级 AADConnect。
     - 默认情况下，将禁用更新的规则。 但是，会启用添加的新同步规则“Out to AD - Group SOAInAAD - Exchange”。
     - 根据克隆的自定义同步规则的优先级，AADConnect 将遵循 Mail 和 Exchange 属性。
     - 如果克隆的自定义同步规则不遵循某些 Mail 和 Exchange 属性，则“新建 Exchange 同步规则”将添加这些属性。
     - 请注意，Group Writeback V2 目前以个人预览版提供，未正式发布。
 - 添加了对[选择性密码哈希同步](./how-to-connect-selective-password-hash-synchronization.md)的支持
 - 添加了新的[单对象同步 cmdlet](./how-to-connect-single-object-sync.md)。 使用此 cmdlet 对 Azure AD Connect 同步配置进行故障排除。 
 -  Azure AD Connect 现在支持用于配置服务的混合标识管理员角色。
 - 已将 AADConnectHealth 代理更新到 3.1.83.0
 - 新版本的 [ADSyncTools PowerShell 模块](./reference-connect-adsynctools.md)，其中包含多个新的或改进的 cmdlet。 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - 更新了令牌获取失败的错误日志记录。
 - 更新了配置页上的“了解更多”链接，以便更详细地了解链接的信息。
 - 已从旧的同步 UI 中的 CS 搜索页删除显式列
 - 已向组写回流中添加了其他 UI，以提示用户输入凭据，或者在前面的步骤中未提供凭据的情况下使用 ADSyncConfig 模块配置自己的权限。
 - 为 DC 上的 ADSync 服务帐户自动创建 MSA。 
 -  添加了在现有 cmdlet 中设置和获取 Azure Active Directory DirSync 功能组写回 V2 的功能：
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - 添加了2个 cmdlet 用于读取 AWS API 版本
    - Get-ADSyncAADConnectorImportApiVersion - 用于获取导入 AWS API 版本
    - Get-ADSyncAADConnectorExportApiVersion - 用于获取导出 AWS API 版本

 - 现在跟踪对同步规则所做的更改，以帮助对服务中的更改进行故障排除。 Cmdlet“ADSyncRuleAudit”将检索跟踪的更改。
 - 更新了 [ADSyncConfig PowerShell 模块](./how-to-connect-configure-ad-ds-connector-account.md#using-the-adsyncconfig-powershell-module)中的 Add-ADSyncADDSConnectorAccount cmdlet，以允许 ADSyncAdmin 组中的用户更改 AD DS 连接器帐户。 

### <a name="bug-fixes"></a>Bug 修复
 - 更新了禁用的前景色，以满足对白色背景的发光度要求。 为满足亮度要求而选择了禁用的页时，在导航树中添加了用于将前景文本颜色设置为白色的其他条件。
 - 增加 Set-ADSyncPasswordHashSyncPermissions cmdlet 粒度-更新了 PHS 权限脚本 (Set-ADSyncPasswordHashSyncPermissions)，使其包含可选的“ADobjectDN”参数。 
 - 易用性 bug 修复。 屏幕阅读器现在会将包含林列表的 UX 元素描述为“林列表”，而不是“Forest List 列表” 
 - 在 Azure AD Connect 向导中更新了某些项的屏幕阅读器输出。 更新了按钮悬停颜色以满足对比度要求。 更新了 Synchronization Service Manager 标题颜色以满足对比度要求。
 - 修复了从具有自定义扩展属性的导出配置中安装 AADConnect 的问题-添加了一个条件，以便在应用同步规则时跳过对目标架构中的扩展属性的检查。
 - 如果启用了组写回功能，则会在安装时添加适当的权限。
 - 解决导入时的重复默认同步规则优先级
 - 修复了在冲突对象的 V2 API 增量导入过程中导致暂存错误的问题，该冲突对象是通过运行健康状况门户修复的。
 - 修复了同步引擎中导致 CS 对象的链接状态不一致的问题
 - 已将导入计数器添加到 Get-ADSyncConnectorStatistics 输出。
 - 修复了 pass2 向导期间某些极端情况中不可达域取消选择（先前已选择）的问题。
 - 如果自定义规则具有重复优先级，修改的策略导入和导出失败 
 - 修复了域选择逻辑中的 bug。
 - 如果你使用 mS-DS-ConsistencyGuid 作为源定位点并克隆“In from AD - Group Join”规则，则会修复生成 1.5.18.0 的问题。
 - 新的 AADConnect 安装将使用存储在云中的导出删除阈值（如果有一个可用的并且没有传入的另一个）。
 - 修复了 AADConnect 不读取混合加入设备的 AD displayName 更改的问题

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>版本状态
07/29/2020：已发布供下载

### <a name="functional-changes"></a>功能性更改
这是 bug 修复版本。 该版本中没有功能性更改。

### <a name="fixed-issues"></a>已修复的问题

- 修复了在“Active Directory”中已经存在 AZUREADSSOACC 计算机账号的情况下管理员无法启用“无缝单一登录”的问题。
- 修复了在冲突对象的 V2 API 增量导入过程中导致暂存错误的问题，该冲突对象是通过运行健康状况门户修复的。
- 修复了导入/导出配置中将禁用的自定义规则导入为启用的问题。

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>版本状态
07/10/2020：已发布供下载

### <a name="functional-changes"></a>功能性更改
此版本包含用于将现有 Azure AD Connect 服务器的配置导出到 JSON 文件中的功能的公共预览版，在安装新的 Azure AD Connect 服务器以创建原始服务器的副本时，可以使用该文件。

可在[此文](./how-to-connect-import-export-config.md)中找到此新功能的详细说明

### <a name="fixed-issues"></a>已修复的问题
- 修复了在升级过程中会出现有关本地化版本上的本地数据库大小的错误警告的 bug。
- 修复了帐户名/域名交换的应用事件中出现错误的 bug。
- 修复了 Azure AD Connect 将无法在 DC 上安装的错误，显示错误为“找不到成员”。


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>版本状态
2020/05/07：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本修复了以下问题：仅选择孙级容器时，便会从向导 UI 中错误地选择未选的域。


>[!NOTE]
>此版本包含新的 Azure AD Connect 同步 V2 终结点 API。  这个新的 V2 终结点目前以公共预览版提供。  使用新的 V2 终结点 API 需要此版本或更高版本。  但是，单是安装此版本不会启用 V2 终结点。 除非启用 V2 终结点，否则将继续使用 V1 终结点。  需要按照 [Azure AD Connect 同步 V2 终结点 API（公共预览版）](how-to-connect-sync-endpoint-api-v2.md)下的步骤进行操作，才能为启用该终结点并选择加入公共预览版。  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>版本状态
2020/04/23：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
此修补程序版本修复了版本 1.5.20.0 中引入的一个问题：具有 MFA 的租户管理员无法启用 DSSO。

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>版本状态
2020/04/20：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
在已克隆 In from AD - Group Join 规则但尚未克隆 In from AD - Group Common 规则的情况下，则此修补程序版本会修复版本 1.5.20.0 中的一个问题 。

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>版本状态
2020/04/09：已发布，供下载

### <a name="fixed-issues"></a>修复的问题
- 在已启用组筛选功能并使用 mS-DS-ConsistencyGuid 作为源定位点的情况下，此修补程序版本会修复版本 1.5.18.0 中的一个问题。
- 修复了 ADSyncConfig PowerShell 模块中的一个问题，其中调用所有 Set-ADSync* Permissions cmdlet 中使用的 DSACLS 命令将导致以下某个错误：
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> 如果已克隆 In from AD - Group Join 同步规则但未克隆 In from AD - Group Common 同步规则，并计划升级，请在升级过程中完成以下步骤 ：
> 1. 在升级过程中，取消选中“在配置完成后启动同步流程”选项。
> 2. 编辑克隆的联接同步规则并添加以下两个转换：
>     - 将直接流 `objectGUID` 设置为 `sourceAnchorBinary`。
>     - 将表达式流 `ConvertToBase64([objectGUID])` 设置为 `sourceAnchor`。     
> 3. 使用 `Set-ADSyncScheduler -SyncCycleEnabled $true` 启用计划程序。



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>版本状态
2020/04/02：已发布，供下载

### <a name="functional-changes-adsyncautoupgrade"></a>功能性更改 ADSyncAutoUpgrade 

- 添加了对组对象的 mS-DS-ConsistencyGuid 功能的支持。 这样，你便可以在林之间移动组，或在 AD 组 objectID 已更改的情况下将 AD 中的组重新连接到的 Azure AD，例如在灾难后重新生成 AD 服务器时。 有关详细信息，请参阅[在林之间移动组](how-to-connect-migrate-groups.md)。
- 所有同步组上会自动设置 mS-DS-ConsistencyGuid 属性，并且无需执行任何操作即可启用此功能。 
- 删除了 Get-ADSyncRunProfile，因为不会再使用它。 
- 更改了尝试使用 AD DS 连接器帐户的企业管理员帐户或域管理员帐户时显示的警告，以提供更多上下文。 
- 添加了新的 cmdlet，以从已删除旧 CSDelete.exe 工具的连接器空间中删除对象，并将该工具替换为新的 Remove-ADSyncCSObject cmdlet。 Remove-ADSyncCSObject cmdlet 将 CsObject 作为输入。 可使用 Get-ADSyncCSObject cmdlet 检索此对象。

>[!NOTE]
>旧的 CSDelete.exe 工具已删除，并已替换为新的 Remove-ADSyncCSObject cmdlet 

### <a name="fixed-issues"></a>修复的问题

- 修复了以下问题：在禁用 Azure AD Connect 向导后重新运行该功能时，组写回林/OU 选择器中存在 bug。 
- 引入了一个新的错误页，如果缺少所需的 DCOM 注册表值，该页会显示新的帮助链接。 信息还会写入日志文件。 
- 修复了与创建 Azure Active Directory 同步帐户有关的问题：启用目录扩展或 PHS 可能会失败，因为在尝试使用之前未在所有服务副本之间传播该帐户。 
- 修复了同步错误压缩实用程序中的 bug：未正确处理代理项字符。 
- 修复了自动升级中的 bug：将服务器置于计划程序已挂起状态。 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>版本状态
2019/12/9：已发布，供下载。 无法通过自动升级获得。
### <a name="new-features-and-improvements"></a>新增功能和改进
- 为 Azure AD 域服务更新了密码哈希同步，以正确解释 Kerberos 哈希中的填充。  在从 Azure AD 向 Azure AD 域服务同步密码期间，这将提升其性能。
- 添加了对身份验证代理和服务总线之间的可靠会话的支持。
- 为身份验证代理和云服务之间的 WebSocket 连接添加了 DNS 缓存。
- 新增了从云中针对特定代理测试代理连接性的功能。

### <a name="fixed-issues"></a>修复的问题
- 版本 1.4.18.0 有一个 bug：在运行 ps 时，用于 DSSO 的 PowerShell cmdlet 使用的是登录窗口凭据，而不是提供的管理员凭据。 因此，无法通过 Azure AD Connect 用户界面在多个林中启用 DSSO。 
- 已进行修复，从而通过 Azure AD Connect 用户界面在所有林中同时启用 DSSO

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>版本状态
2019/11/08：已发布，供下载。 无法通过自动升级获得。

>[!IMPORTANT]
>由于该版本 Azure AD Connect 的内部架构发生了更改，因此，如果使用 MSOnline PowerShell 管理 AD FS 信任关系配置设置，则必须将 MSOnline PowerShell 模块更新为版本 1.1.183.57 或更高版本

### <a name="fixed-issues"></a>修复的问题

此版本修复了现有已建立混合 Azure AD 联接的设备方面的问题。 此版本包含新的设备同步规则，可修正此问题。
请注意，此规则更改可能会从 Azure AD 删除过时的设备。 不必担心，因为在条件访问授权期间 Azure AD 不会使用这些设备对象。 对于某些客户，通过此规则更改删除的设备数量可能会超出删除阈值。 如果看到 Azure AD 中设备对象的删除数量超过了导出删除阈值，建议允许删除。 [如何在删除数量超出删除阈值时允许其流动](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>版本状态
2019/9/28：已发布，用于自动升级以选择租户。 不可供下载。

此版本修复了一个 bug：某些服务器从先前版本自动升级到 1.4.18.0，并遇到了自助式密码重置 (SSPR) 和密码写回问题。

### <a name="fixed-issues"></a>修复的问题

在某些情况下，自动升级到版本 1.4.18.0 的服务器不会在升级完成后重新启用自助式密码重置和密码写回。 此自动升级版本修复了该问题，并重新启用了自助式密码重置和密码写回。

我们修复了同步错误压缩实用程序中的 bug：未正确处理代理项字符。

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>我们正在调查一个事件：某些客户在升级到这一版本的 Azure AD Connect 后，遇到了现有已建立混合 Azure AD 联接的设备方面的问题。 我们建议已部署混合 Azure AD 联接的客户推迟升级为这一版本，一直到完全了解这些问题的根本原因并缓解这些问题为止。 我们将尽快提供详细信息。

>[!IMPORTANT]
>对于这一版本的 Azure AD Connect，某些客户可能会看到其部分或所有 Windows 设备从 Azure AD 中消失。 不必担心，因为在条件访问授权期间 Azure AD 不会使用这些设备标识。 有关详细信息，请参阅[了解 Azure AD Connect 1.4.xx.x 设备消失](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)


### <a name="release-status"></a>版本状态
2019/9/25：已发布，仅用于自动升级。

### <a name="new-features-and-improvements"></a>新增功能和改进
- 新疑难解答工具有助于对“用户不同步”、“组不同步”或“组成员不同步”的情况进行故障排除。
- 在 Azure AD Connect 故障排除脚本中添加对国家云的支持。
- 客户应接到关于以下信息的通知：MIIS_Service 已弃用的 WMI 终结点现已删除。 现在，任何 WMI 操作都应通过 PS cmdlet 完成。
- 通过重置对 AZUREADSSOACC 对象的约束委派提高了安全性。
- 添加/编辑同步规则时，如果规则中使用的任何属性都在连接器架构中，但未添加到连接器，那么这些属性都会自动添加到连接器。 对于规则影响的对象类型也是如此。 如果在连接器中添加了任何内容，则会在下一个同步周期中将连接器标记为完全导入。
- 新的 Azure AD Connect 部署中不再支持使用企业管理员或域管理员作为连接器帐户。 使用企业管理员或域管理员作为连接器帐户的当前 Azure AD Connect 部署将不受此版本的影响。
- 在同步管理器中，创建/编辑/删除规则时会运行完全同步。 进行任何规则更改时，都将显示一个弹出窗口，通知用户是否要运行完全导入或完全同步。
- 向“连接器”>“属性”>“连接”页中添加了密码错误的缓解步骤。
- 在连接器属性页上为同步服务管理器添加了弃用警告。 此警告通知用户应通过 Azure AD Connect 向导进行更改。
- 为用户密码策略相关问题新增了错误消息。
- 防止按域和 OU 筛选器对组筛选进行错误配置。 当所输入组的域/OU 已筛选出来时，组筛选将显示错误，并且会在问题解决前阻止用户继续执行操作。
- 用户无法再在 Synchronization Service Manager UI 中为 Active Directory 域服务或 Windows Azure Active Directory 创建连接器。
- 在 Synchronization Service Manager 中修复了自定义 UI 控件的辅助功能。
- 为 Azure AD Connect 中的所有登录方法启用了六项联合身份验证管理任务。  （以前，只有“更新 AD FS TLS/SSL 证书”任务可用于所有登录。）
- 在将登录方法从联合身份验证更改为 PHS 或 PTA 时添加了警告，提醒所有 Azure AD 域和用户都将转换为托管身份验证。
- 从“重置 Azure AD 和 AD FS 信任”任务中删除了令牌签名证书，并添加了单独的子任务来更新这些证书。
- 添加了名为“管理证书”的新联合身份验证管理任务，其中包含用于更新 AD FS 场的 TLS 或令牌签名证书的子任务。
- 添加了名为“指定主服务器”的新联合管理子任务，该任务允许管理员为 AD FS 场指定新的主服务器。
- 添加了名为“管理服务器”的新联合身份验证管理任务，其中包含部署 AD FS 服务器、部署 Web 应用程序代理服务器和指定主服务器的子任务。
- 添加了名为“查看联合身份验证配置”的新联合身份验证管理任务，其中显示了当前的 AD FS 设置。  （由于这项添加内容，AD FS 设置已从“查看解决方案”页中删除。）

### <a name="fixed-issues"></a>修复的问题
- 解决了以下场景中出现的同步错误问题：接管其相应联系人对象的用户对象存在自引用（例如，用户是他们自己的管理员）。
- “帮助”弹出窗口现在显示在键盘焦点上。
- 对于自动升级，如果在 6 小时后有任何存在冲突的应用运行，请将其终止，然后继续升级。
- 选择目录扩展时，将客户可为每个对象选择的属性数限制为 100。 这将防止在导出过程中发生错误，因为 Azure 每个对象最多具有 100 个扩展属性。
- 修复了一个 bug，使 AD 连接性脚本更加可靠。
- 修复了一个 bug，使利用现有命名管道 WCF 服务在计算机上的 Azure AD Connect 安装更加可靠。
- 改进了有关组策略的诊断和疑难解答，这些策略不允许在最初安装时启动 ADSync 服务。
- 修复了以下 bug：Windows 计算机的显示名称写入错误。
- 修复了以下 bug：Windows 计算机的操作系统类型写入错误。
- 修复了以下 bug：非 Windows 10 计算机意外进行同步。 请注意，此更改的影响是，现在将删除之前同步的非 Windows 10 计算机。 这不会影响任何功能，因为 Windows 计算机的同步仅用于混合 Azure AD 域加入，这仅适用于 Windows 10 设备。
- 向 ADSync PowerShell 模块添加了多个新的（内部）cmdlet。


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>将 Azure AD Connect 从早期版本升级到 1.3.21.0 时存在一个已知问题：即使 Azure AD Connect 已成功升级，Microsoft 365 门户也不反映更新的版本。
>
> 若要解决此问题，需要导入“AdSync”模块，然后在 Azure AD Connect 服务器上运行 `Set-ADSyncDirSyncConfiguration` PowerShell cmdlet。  可以使用以下步骤：
>
>1. 在管理员模式下开启 PowerShell。
>2. 运行 `Import-Module "ADSync"`。
>3. 运行 `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`。
 
### <a name="release-status"></a>版本状态 

2019/05/14：已发布，供下载

### <a name="fixed-issues"></a>修复的问题 

- 修复了 Microsoft Azure Active Directory Connect 版本 1.3.20.0 中存在的特权提升漏洞。  在某些情况下，此漏洞可能允许攻击者利用特权帐户来执行两个 PowerShell cmdlet，并执行特权操作。  此安全更新程序通过禁用这些 cmdlet 解决了此问题。 有关详细信息，请参阅[安全更新](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)。


## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
