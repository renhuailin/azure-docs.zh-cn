---
title: 有关将 Okta 同步预配迁移到基于 Azure AD Connect 的同步的教程
titleSuffix: Active Directory
description: 在本教程中，你将了解如何将 Okta 同步预配迁移到基于 Azure AD Connect 的同步。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 588764e8eb1864dc702fa94b05fcd7bf32a57c79
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388069"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>教程：有关将 Okta 同步预配迁移到基于 Azure AD Connect 的同步

在本教程中，你将了解组织当前如何将用户预配从 Okta 迁移到 Azure Active Directory (Azure AD) 并将用户同步或通用同步迁移到 Azure AD Connect。 使用此功能可以进一步预配到 Azure AD 和 Office 365。

迁移同步平台并不是一项轻微的更改。 在从过渡模式删除 Azure AD Connect 或启用 Azure AD 云预配代理之前，应针对你自己的环境验证本文中所述过程的每个步骤。

## <a name="prerequisites"></a>先决条件

从 Okta 预配切换到 Azure AD 时，有两种选择。 可使用 Azure AD Connect 服务器或 Azure AD 云预配。 要了解两者之间的差异，请阅读 [Microsoft 的比较文章](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync)。

Azure AD 云配置将是使用通用或用户同步的 Okta 客户最熟悉的迁移路径。云预配代理为轻型代理。 它们可以安装在域控制器上或附近，如 Okta 目录同步代理。 不要将它们安装在同一台服务器上。

如果组织在同步用户时需要利用以下任一技术，请使用 Azure AD Connect 服务器：

- 设备同步：混合 Azure AD 加入或 Hello 企业版
- 直通身份验证
- 支持超过 15 万个对象
- 支持写回

>[!NOTE]
>安装 Azure AD Connect 或 Azure AD 云预配时，应考虑到所有先决条件。 要在继续安装之前详细了解，请参阅 [Azure AD Connect 的先决条件](../hybrid/how-to-connect-install-prerequisites.md)。
## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>确认由 Okta 同步的 ImmutableID 属性

ImmutableID 是核心属性，用于将已同步的对象关联到其本地的对应对象。 Okta 提取本地对象的 Active Directory objectGUID，并将其转换为 Base64 编码字符串。 然后，在默认情况下，它将该字符串戳记到 Azure AD 中的 ImmutableID 字段。

可以连接到 Azure AD PowerShell 并检查当前 ImmutableID 值。 如果你从未使用过 Azure AD PowerShell 模块，请先在管理 PowerShell 会话中运行 `Install-Module AzureAD`，然后再运行以下命令：

```Powershell
Import-module AzureAD
Connect-AzureAD
```

如果你已安装该模块，则可能会收到一条警告，指出将会更新到最新版本（如果当前版本已过时）。

安装该模块后，将其导入，然后按照以下步骤连接到 Azure AD 服务：

1. 在新式身份验证窗口中输入你的全局管理员凭据。

   ![显示 Import-Module 的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. 连接到租户后，验证 ImmutableID 值的设置。 所示的示例使用对应于 ImmutableID 的 objectGUID 的 Okta 默认值。

   ![显示对应于 ImmutableID 的 objectGUID 的 Okta 默认值的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. 可通过多种方式在本地手动确认 objectGUID 到 Base64 的转换。 要单独进行验证，请使用以下示例：

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![显示如何手动将 Okta objectGUID 更改为 ImmutableID 的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>objectGUID 的批量验证方法

在切换到 Azure AD Connect 之前，必须验证 Azure AD 中的 ImmutableID 是否与其本地值完全匹配。

该示例将抓取所有本地 Azure AD 用户，并将其已计算的 objectGUID 值和 ImmutableID 值列表导出到 CSV 文件。

1. 在本地域控制器上的 PowerShell 中运行这些命令：

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![显示本地域控制器命令的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. 在 Azure AD PowerShell 会话中运行这些命令，以收集已同步的值：

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![显示 Azure AD PowerShell 会话的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   完成两次导出后，确认每个用户的 ImmutableID 是否匹配。

   >[!IMPORTANT]
   >如果云中的 ImmutableID 与 objectGUID 值不匹配，则表示修改了 Okta 同步的默认值。你可能已选择另一个属性来确定 ImmutableID 值。 在转到下一部分之前，必须确定哪个源属性正在填充 ImmutableID 值。 在禁用 Okta 同步之前，请确保更新 Okta 正在同步的属性。
## <a name="install-azure-ad-connect-in-staging-mode"></a>在过渡模式下安装 Azure AD Connect

准备好源和目标列表后，可以安装 Azure AD Connect 服务器。 如果你已选择使用 Azure AD Connect 云预配，请跳过本部分。

1. 继续在所选的服务器上[下载并安装 Azure AD Connect](../hybrid/how-to-connect-install-custom.md)。 

1. 在“标识用户”页上的“选择应如何使用 Azure AD 标识用户”下，选中“选择特定的属性”选项  。 然后，如果你尚未修改 Okta 默认值，请选择“mS-DS-ConsistencyGUID”。

   >[!WARNING]
   >这是此页面上最关键的步骤。 在选择“下一步”之前，请确保为源定位点选择的属性是当前填充现有 Azure AD 用户的属性。 如果选择了错误的属性，则必须卸载再重新安装 Azure AD Connect，以重新选择此选项。
   ![显示 mS-DS-ConsistencyGuid 的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

1. 在“配置”页上，确保选中“启用过渡模式”复选框 。 然后选择“安装”。

   ![显示“启用过渡模式”复选框的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. 配置完成后，选择“退出”。

   退出过渡模式之前，请验证 ImmutableID 值是否正确匹配。

1. 以管理员身份打开“同步服务”。

   ![显示打开“同步服务”的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. 在“具有流更新的连接器”选项卡下，检查到 domain.onmicrosoft.com 连接器空间的完全同步是否显示了用户 。

   ![显示“具有流更新的连接器”选项卡的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. 验证导出中是否不存在挂起的删除操作。 选择“连接器”选项卡，然后突出显示 domain.onmicrosoft.com 连接器空间。 然后选择“搜索连接器空间”。

   ![显示搜索连接器空间操作的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. 在“搜索连接器空间”对话框中选择“范围”下拉列表，然后选择“挂起的导出”  。

   ![显示挂起的导出的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. 依次选择“删除”和“搜索” 。 如果所有对象都已正确匹配，则“删除”不会有任何匹配记录。 记录所有等待删除的对象及其本地值。

   ![显示已删除匹配记录的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. 清除“删除”，然后选择“添加”和“修改”并执行搜索  。 应会看到当前正在通过 Okta 同步到 Azure AD 的所有用户的 update 函数。 添加 Okta 当前未同步的、但存在于在安装 Azure AD Connect 期间选择的组织单位 (OU) 结构中的任何新对象。

   ![显示添加新对象的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. 双击更新内容会显示 Azure AD Connect 向 Azure AD 传达的信息。

1. 如果已存在于 Azure AD 中的用户有任何添加函数，则他们的本地帐户与云帐户不匹配。 AD Connect 已确定它将创建一个新对象，并记录任何不需要的新添加。 在退出过渡模式之前，请确保更正 Azure AD 中的 ImmutableID 值。

   在此示例中，Okta 将 Mail 属性戳记到用户的帐户，即使未正确填充本地值也是如此。 当 Azure AD Connect 接管 John Smith 的帐户时，将从其对象中删除 Mail 属性。

   验证更新内容是否仍包含 Azure AD 中所需的所有属性。 如果删除多个属性，可能需要在删除过渡模式之前手动填充这些本地 AD 值。

   ![显示填充本地添加值的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >在继续下一步之前，请确保所有用户属性都在正确同步，并按预期显示在“挂起的导出”选项卡上。 如果删除这些属性，请确保其 ImmutableID 值匹配，并且用户位于选择用于同步的某个 OU 中。
## <a name="install-azure-ad-cloud-sync-agents"></a>安装 Azure AD 云同步代理

准备好源和目标列表后，可以[安装并配置 Azure AD 云同步代理](../cloud-sync/tutorial-single-forest.md)。 如果你已选择使用 Azure AD Connect 服务器，请跳过此部分。

## <a name="disable-okta-provisioning-to-azure-ad"></a>禁用 Okta 到 Azure AD 的预配

验证 Azure AD Connect 安装并且挂起的导出正常进行后，可以禁用 Okta 到 Azure AD 的预配。

1. 转到 Okta 门户，选择“应用程序”，然后选择用于将用户预配到 Azure AD 的 Okta 应用。 打开“预配”选项卡，然后选择“集成”部分 。

   ![显示 Okta 中的“集成”部分的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. 选择“编辑”，清除“启用 API 集成”选项，然后选择“保存”。  

   ![显示在 Okta 中编辑启用 API 集成的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >如果有多个 Office 365 应用在处理到 Azure AD 的预配，请确保关闭所有这些应用。
## <a name="disable-staging-mode-in-azure-ad-connect"></a>在 Azure AD Connect 中禁用过渡模式

禁用 Okta 预配后，Azure AD Connect 服务器已准备好开始同步对象。 如果你已选择使用 Azure AD 云同步代理，请跳过此部分。

1. 再次从桌面运行安装向导，选择“配置”。

   ![显示 Azure AD Connect 服务器的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. 依次选择“配置过渡模式”和“下一步” 。 输入你的全局管理员凭据。

   ![显示“配置过渡模式”选项的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. 清除“启用过渡模式”选项，然后选择“下一步” 。

   ![显示清除“启用过渡模式”选项的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. 选择“配置”以继续。

   ![显示选择“配置”按钮的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. 配置完成后，以管理员身份打开“同步服务”。 查看 domain.onmicrosoft.com 连接器上的“导出”。 验证所有添加、更新和删除操作是否按预期完成。

   ![显示验证同步服务的屏幕截图。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

现已成功迁移到基于 Azure AD Connect 服务器的预配。 可通过重新运行安装向导来对 Azure AD Connect 的功能集进行更新和扩展。

## <a name="enable-cloud-sync-agents"></a>启用云同步代理

禁用 Okta 预配后，Azure AD 云同步代理已准备好开始同步对象。 返回到 [Azure AD 门户](https://aad.portal.azure.com/)。

1. 将“配置文件”修改为“已启用” 。

1. 返回到预配菜单，然后选择“日志”。

1. 评估预配连接器是否正确更新了本地对象。 云同步代理不会造成破坏。 如果未正确发生匹配，这些代理会使更新失败。

1. 如果用户不匹配，请进行必要的更新以绑定 ImmutableID 值。 然后重启云预配同步。

## <a name="next-steps"></a>后续步骤
有关从 Okta 迁移到 Azure AD 的详细信息，请参阅：

- [将应用程序从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [将 Okta 联合身份验证迁移到 Azure AD 托管身份验证](migrate-okta-federation-to-azure-active-directory.md)
- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
