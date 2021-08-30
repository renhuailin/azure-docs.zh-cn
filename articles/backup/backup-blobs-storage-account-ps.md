---
title: 在使用 Azure PowerShell 的存储帐户中备份 Azure Blob
description: 了解如何在使用 Azure PowerShell 的存储帐户中备份 Azure Blob。
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: 0f3e7d03abbf4a5bfbb5d5cb533df04a17c71edf
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179781"
---
# <a name="back-up-all-azure-blobs-in-a-storage-account-using-azure-powershell"></a>在使用 Azure PowerShell 的存储帐户中备份所有 Azure Blob

本文介绍如何在使用 Azure PowerShell 的存储帐户中备份所有 [Azure Blob](./blob-backup-overview.md)。

本文将指导如何进行以下操作：

- 开始之前

- 创建备份保管库

- 创建备份策略

- 配置存储帐户中所有 Azure Blob 的备份

有关 Azure Blob 区域可用性、支持的方案和限制，请参阅[支持矩阵](blob-backup-support-matrix.md)。

> [!IMPORTANT]
> Az 5.9.0 版本中提供了对 Azure Blob 的支持。

## <a name="before-you-start"></a>开始之前

在开始之前，请参阅[先决条件](./blob-backup-configure-manage.md#before-you-start)和[支持矩阵](./blob-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是 Azure 中的一个存储实体，用于保存 Azure 备份支持的各种新型工作负荷（例如 Azure Database for PostgreSQL 服务器、Azure Blob 和 Azure Blob）的备份数据。 备份保管库便于组织备份数据，并最大限度降低管理开销。 备份保管库基于 Azure 的 Azure 资源管理器型号，提供增强功能帮助保护备份数据。

在创建备份保管库之前，请选择保管库数据的存储冗余。 然后继续使用该存储冗余和位置创建备份保管库。 在本文中，我们将在资源组 testBkpVaultRG 下，在 westus 区域创建备份保管库 TestBkpVault。 使用[New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.9.0&preserve-view=true)命令创建备份保管库。深入了解如何 [创建备份保管库](./backup-vault-overview.md#create-a-backup-vault)。

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore

New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

创建保管库后，让我们创建备份策略来保护 Azure Blob。

> [!IMPORTANT]
> 虽然会看到保管库的备份存储冗余，但冗余并不实际应用于 blob 的操作备份，因为该备份在本质上是本地的，不会有任何数据存储在备份保管库中。 此处的备份保管库是管理实体，可帮助你管理存储帐户中块 blob 的保护。

## <a name="create-a-backup-policy"></a>创建备份策略

> [!IMPORTANT]
> 在继续为 Azure Blob 创建策略和配置备份之前，请阅读[本节](blob-backup-configure-manage.md#before-you-start)。

若要了解 Azure Blob 备份的备份策略之内部组件，请使用 [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.9.0&preserve-view=true) 命令来检索策略模板。 此命令返回给定数据源类型的默认策略模板。 使用此策略模板创建新策略。

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureBlob
$policyDefn | fl


DatasourceType : {Microsoft.Storage/storageAccounts/blobServices}
ObjectType     : BackupPolicy
PolicyRule     : {Default}

$policyDefn.PolicyRule | fl

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api202101.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

策略模板仅包含生命周期（决定删除/复制/移动备份的时间）。 由于 Blob 的操作备份在本质上是连续的，因此无需日程安排即可执行备份。

```azurepowershell-interactive
$policyDefn.PolicyRule.Lifecycle | fl


DeleteAfterDuration        : P30D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

> [!NOTE]
> 对较长持续时间进行还原可能会导致还原操作需要较长时间才能完成。 此外，还原数据集所用的时间还取决于在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两个小时才能还原到过去 30 天的点。<br><br>对于具有此变化率的帐户，不建议在过去保留时间和还原超过 90 天。

一旦策略对象具有所有所需值，请使用 [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.9.0&preserve-view=true) 命令从策略对象创建新策略。

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name blobBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
blobBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$blobBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "blobBkpPolicy"
```

## <a name="configure-backup"></a>配置备份

创建保管库和策略后，用户需要考虑 2 个关键点来保护存储帐户中的所有 Azure Blob。

### <a name="key-entities-involved"></a>所涉关键实体

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>含有要保护的 Blob 的存储帐户

提取含有要保护的 Blob 的存储帐户的 Azure 资源管理器 ID。 用作存储帐户的标识符。 我们将在 blobrg 资源组下，在不同的订阅中使用名为“PSTestSA”的存储帐户示例。

```azurepowershell-interactive
$SAId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/PSTestSA"
```

#### <a name="backup-vault"></a>备份保管库

备份保管库需要存储帐户的权限才能对存储帐户中的 Blob 启用备份。 保管库的系统分配托管标识用于分配此类权限。

### <a name="assign-permissions"></a>分配权限

你需要通过 RBAC 向保管库（以保管库 MSI 表示）和相关存储帐户分配一些权限。 可以通过门户或 PowerShell 执行上述操作。 详细了解所有[相关权限](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)。

### <a name="prepare-the-request"></a>准备请求

一旦设置了所有相关的权限，就可以分 2 步执行备份配置。 首先，我们使用相关保管库、策略、使用 [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) 命令的存储帐户来准备相关请求。 然后，我们提交请求以保护使用 [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) 命令的存储帐户中的 Blob。

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureBlob -DatasourceLocation $TestBkpvault.Location -PolicyId $blobBkpPol[0].Id -DatasourceId $SAId 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances blobrg-PSTestSA-3df6ac08-9496-4839-8fb5-8b78e594f166
```

> [!IMPORTANT]
> 为 Blob 备份配置存储帐户后，一些功能会受到影响，例如更改源和删除锁。 [了解详细信息](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)。

## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell 还原 Azure Blob](restore-blobs-storage-account-ps.md)