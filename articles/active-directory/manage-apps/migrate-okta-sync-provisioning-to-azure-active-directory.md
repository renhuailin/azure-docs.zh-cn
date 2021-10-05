---
title: 有关将 Okta 同步预配迁移到基于 Azure AD Connect 的同步的教程
titleSuffix: Active Directory
description: 了解如何将 Okta 同步预配迁移到基于 Azure AD Connect 的同步
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: d70d3f32093fc2e509d351e0ce194bac1d7490b8
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084144"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization"></a>教程：将 Okta 同步预配迁移到基于 Azure Active Directory Connect 的同步

本文将引导当前使用从 Okta 到 Azure Active Directory (Azure AD) 的用户预配的组织将用户同步或通用同步迁移到 Azure AD Connect。 使用此方法可以进一步预配到 Azure AD 和 Office 365。

迁移同步平台并不是一项轻微的更改。 在从过渡模式删除 Azure AD Connect 或启用 Azure AD 云预配代理之前，应针对你自己的环境验证本文中所述过程的每个步骤。

## <a name="prerequisites"></a>先决条件

从 Okta 预配切换到 Azure AD 时，客户有两种选择：Azure AD Connect 服务器或 Azure AD 云预配。 建议阅读 [Microsoft 提供的完整比较文章](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync)，了解这两个产品之间的差异。

Azure AD 云预配对于使用通用或用户同步的 Okta 客户而言是最熟悉的迁移路径。云预配代理是轻型的，可以安装在 Okta 目录同步代理等域控制器上或其附近。 不建议将这些代理安装在同一台服务器上。

如果组织在同步用户时需要利用以下任一技术，则应选择 Azure AD Connect 服务器。

- 设备同步 - 混合 Azure AD 加入或 Hello 企业版

- 直通身份验证

- 支持超过 15 万个对象

- 支持写回

>[!NOTE]
>安装 Azure AD Connect 或 Azure AD 云预配时，应考虑到所有先决条件。 在安装之前，请参阅[此文了解详细信息](../hybrid/how-to-connect-install-prerequisites.md)。  

## <a name="step-1---confirm-immutableid-attribute-synchronized-by-okta"></a>步骤 1 - 确认 Okta 同步的 ImmutableID 属性

ImmutableID 是核心属性，用于将已同步的对象关联到其本地的对应对象。 Okta 提取本地对象的 Active Directory objectGUID，并将其转换为 Base64 编码字符串。 然后，在默认情况下，将该字符串戳记到 Azure AD 中的 ImmutableID 字段。

可以连接到 Azure AD PowerShell 并检查当前 ImmutableID 值。 如果你从未使用过 Azure AD PowerShell 模块，请先在管理 PowerShell 会话中运行 `Install-Module AzureAD`，然后再运行以下命令。

```Powershell
Import-module AzureAD
Connect-AzureAD
```

如果你已安装该模块，则可能会收到一条警告，指出将会更新到最新版本（如果当前版本已过时）。

安装该模块后，将其导入，然后按照以下步骤连接到 Azure AD 服务：

1. 在新式身份验证窗口中输入你的全局管理员凭据。

   ![该插图显示导入模块](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

2. 连接到租户后，验证 ImmutableID 的设置。 所示的示例使用对应于 ImmutableID 的 objectGUID 的 Okta 默认值。

   ![该插图显示对应于 ImmutableID 的 objectGUID 的 Okta 默认值](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

3. 可通过多种方式在本地手动确认 objectGUID 到 Base64 的转换，要单独进行验证，请使用以下示例：

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'

   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![该插图显示如何手动将 Okta objectGUID 更改为 ImmutableID](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="step-2---mass-validation-methods-for-objectguid"></a>步骤 2 - objectGUID 的批量验证方法

在切换到 Azure AD Connect 之前，必须验证 Azure AD 中的 ImmutableID 是否与其本地值完全匹配。

该示例将抓取所有本地 AD 用户，并将其已计算的 objectGUID 和 ImmutableID 列表导出到 CSV 文件。

1. 在本地域控制器上的 PowerShell 中运行这些命令。 

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![该插图显示本地域控制器命令](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

2. 在 Azure AD PowerShell 会话中运行这些命令，以收集已同步的值：

   ```powershell

   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![该插图显示 azure ad powershell 会话](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   完成两次导出后，确认每个用户的 ImmutableID 是否匹配。

   >[!IMPORTANT]
   >如果云中的 ImmutableID 与 objectGUID 值不匹配，则表示修改了 Okta 同步的默认值。你可能已选择另一个属性来确定 ImmutableID。 在转到下一部分之前，必须确定哪个源属性正在填充 ImmutableID。      在禁用 Okta 同步之前，请确保更新 Okta 正在同步的属性。

## <a name="step-3---install-azure-ad-connect-in-staging-mode"></a>步骤 3 - 在过渡模式下安装 Azure AD Connect

准备好源和目标列表后，可以开始安装 Azure AD Connect 服务器了。 如果你已选择使用 Azure AD Connect 云预配，请跳过本部分。

1. 继续在所选的服务器上[下载并安装 Azure AD Connect](../hybrid/how-to-connect-install-custom.md)。 

2. 在“标识用户”页上的“选择应如何使用 Azure AD 标识用户”下，选中“选择特定的属性”对应的单选按钮  。 然后，如果你尚未修改 Okta 默认值，请选择“mS-DS-ConsistencyGUID”。

   >[!WARNING]
   >这是在此页上选择“下一步”之前最重要的步骤。 确保为源定位点选择的属性是当前填充现有 Azure AD 用户的属性。 如果选择了错误的属性，则必须卸载再重新安装 Azure AD Connect，以重新选择此选项。

   ![该插图显示一致性 guid](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

3. 在“配置”页上，确保选中“启用过渡模式”对应的复选框，然后选择“安装”  。

   ![该插图显示“启用过渡模式”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

4. 配置完成后，选择“退出”。

在退出过渡模式之前，必须验证是否正确匹配了 ImmutableID。

1. 以管理员身份打开同步服务。

   ![该插图显示打开同步服务](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

2. 首先在“具有流更新的连接器”选项卡下，检查到 domain.onmicrosoft.com 连接器空间的完全同步是否显示了用户。

   ![该插图显示“具有流更新的连接器”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

3. 接下来，验证导出中是否不存在挂起的删除操作。 选择“连接器”选项卡，然后突出显示 domain.onmicrosoft.com 连接器空间。 然后选择“搜索连接器空间”。

   ![该插图显示“搜索连接器空间”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

4. 在“连接器空间”搜索中选择“范围”下拉列表，然后选择“挂起的导出”。

   ![该插图显示“挂起的导出”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

5. 依次选择“删除”和“搜索”，如果所有对象都已正确匹配，则“删除”不会有任何匹配记录 。 记录所有等待删除的对象及其本地值。

   ![该插图显示已删除的匹配记录](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

6. 接下来，取消选中“删除”，然后选择“添加和修改”并执行搜索 。 应会看到当前正在通过 Okta 同步到 Azure AD 的所有用户的 update 函数。 添加 Okta 当前未同步的、但存在于在安装 Azure AD Connect 期间选择的组织单位 (OU) 结构中的任何新对象。

   ![该插图显示添加新对象](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

7. 双击更新内容会显示 Azure AD Connect 向 Azure AD 传达的信息。

8. 如果 Azure AD 中已存在的用户具有任何 Add 函数，其本地帐户与其云帐户不匹配，并且 AD Connect 已确定它将创建新对象，请记录任何意外的新添加内容。 在退出过渡模式之前，请确保更正 Azure AD 中的 ImmutableID 值。

   在此示例中，Okta 一直在将 Mail 属性戳记到用户的帐户，即使未正确填充本地值也是如此。 当 Azure AD Connect 接管 John Smith 的帐户时，将从其对象中删除 Mail 属性。

   验证更新内容是否仍包含 Azure AD 中所需的所有属性。 如果删除多个属性，可能需要在删除过渡模式之前手动填充这些本地 AD 值。

   ![该插图显示填充本地 AD 值](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >在继续下一步之前，请确保所有用户属性都在正确同步，并按预期显示在“挂起的导出”选项卡中。 如果删除这些属性，请确保其 ImmutableID 匹配，并且用户位于选择用于同步的某个 OU 中。

## <a name="step-4---install-azure-ad-cloud-sync-agents"></a>步骤 4 - 安装 Azure AD 云同步代理

准备好源和目标列表后，可以开始[安装并配置 Azure AD 云同步代理](../cloud-sync/tutorial-single-forest.md)了。 如果你已选择使用 Azure AD Connect 服务器，请跳过本部分。

## <a name="step-5---disable-okta-provisioning-to-azure-ad"></a>步骤 5 - 禁用 Okta 到 Azure AD 的预配

验证 Azure AD Connect 安装并且挂起的导出正常进行后，可以禁用 Okta 到 Azure AD 的预配。

1. 导航到 Okta 门户，选择“应用程序”，然后选择用于将用户预配到 Azure AD 的 Okta 应用。 打开“预配”选项卡和“集成”部分。

   ![该插图显示 Okta 中的“集成”部分](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

2. 选择“编辑”，取消选中“启用 API 集成”选项，然后选择“保存”  。

   ![该插图显示在 Okta 中编辑“启用 API 集成”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >如果有多个 Office 365 应用正在处理到 Azure AD 的预配，请确保关闭所有这些应用。

## <a name="step-6---disable-staging-mode-in-azure-ad-connect"></a>步骤 6 - 在 Azure AD Connect 中禁用过渡模式

禁用 Okta 预配后，Azure AD Connect 服务器已准备好开始同步对象。 如果你已选择使用 Azure AD 云同步代理，请跳过本部分。

1. 再次从桌面运行安装向导，选择“配置”。

   ![该插图显示 Azure AD Connect 服务器](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

2. 依次选择“配置过渡模式”和“下一步”，然后输入你的全局管理员凭据 。

   ![该插图显示“配置过渡模式”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

3. 取消选中“启用过渡模式”，然后选择“下一步”。

   ![该插图显示取消选中“启用过渡模式”](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

4. 选择“配置”以继续。

   ![该插图显示已准备好进行配置](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

5. 配置完成后，以管理员身份打开“同步服务”。 查看 domain.onmicrosoft.com 连接器上的“导出”。 验证是否按预期方式完成了所有添加、更新和删除操作。

   ![该插图显示验证同步服务](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

现已成功迁移到基于Azure AD Connect 服务器的预配。 可以通过重新运行安装向导来对 Azure AD Connect 的功能集进行更新和扩展。

## <a name="step-7---enable-cloud-sync-agents"></a>步骤 7 - 启用云同步代理

禁用 Okta 预配后，Azure AD 云同步代理已准备好开始同步对象。请返回到 [Azure AD 门户](https://aad.portal.azure.com/)。

1. 将“配置文件”修改为“已启用” 。

2. 启用后，返回到预配菜单并选择“日志”。

3. 评估预配连接器是否正确更新了本地对象。 云同步代理不会造成破坏。 如果未正确发生匹配，这些代理会使更新失败。

4. 如果用户不匹配，请进行必要的更新以绑定 immutableID，然后重启云预配同步。

## <a name="next-steps"></a>后续步骤

- [将应用程序从 Okta 迁移到 Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [将 Okta 联合身份验证迁移到 Azure AD 托管身份验证](migrate-okta-federation-to-azure-active-directory.md)

- [将 Okta 登录策略迁移到 Azure AD 条件访问](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
