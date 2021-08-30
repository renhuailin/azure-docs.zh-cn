---
title: 删除 TDE 保护器（PowerShell 和 Azure CLI）
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: 了解如何针对使用 TDE（支持“创建自己的密钥”(BYOK)）的 Azure SQL 数据库或 Azure Synapse Analytics，响应可能已泄露的 TDE 保护器。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 6d3027afae6b1d4121582014bb2b525ba2fd2c44
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090252"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 删除透明数据加密 (TDE) 保护器
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


本主题介绍如何针对使用 TDE（支持“Azure Key Vault 中的客户托管密钥 - 创建自己的密钥 (BYOK)”）的 Azure SQL 数据库或 Azure Synapse Analytics，响应可能已泄露的 TDE 保护器。 若要详细了解 TDE 的 BYOK 支持，请参阅[概述页](transparent-data-encryption-byok-overview.md)。

> [!CAUTION]
> 本文概述的过程仅应在极端情况下或在测试环境中执行。 请仔细查看相关步骤，因为从 Azure Key Vault 中删除活跃使用的 TDE 保护器将导致数据库不可用。

如果怀疑某个密钥已泄露，以致某个服务或用户在未经授权的情况下访问该密钥，则最好是删除该密钥。

请记住，在 Key Vault 中删除了 TDE 保护器后，在长达 10 分钟的时间内，所有加密数据库将开始拒绝所有带有相应错误消息的连接，并将其状态更改为[无法访问](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)。

本操作指南介绍了在对入侵事件进行响应后使数据库不可访问的方法。

> [!NOTE]
> 本文适用于 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics（专用 SQL 池（以前称为 SQL DW））。 有关 Synapse 工作区内专用 SQL 池的透明数据加密的文档，请参阅 [Azure Synapse Analytics 加密](../../synapse-analytics/security/workspaces-encryption.md)。

## <a name="prerequisites"></a>先决条件

- 必须有一个 Azure 订阅，并且是该订阅的管理员
- 必须安装并运行 Azure PowerShell。
- 本操作指南假设已使用 Azure Key Vault 中的密钥作为 Azure SQL 数据库或 Azure Synapse 的 TDE 保护器。 有关详细信息，请参阅[支持 BYOK 的透明数据加密](transparent-data-encryption-byok-overview.md)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 有关 Az 模块安装说明，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 若要了解具体的 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器 (RM) 模块，但所有后续开发都针对 Az.Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

有关安装的信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="check-tde-protector-thumbprints"></a>检查 TDE 保护程序指纹

以下步骤概述了如何检查给定数据库的虚拟日志文件 (VLF) 仍在使用的 TDE 保护程序指纹。
可以通过运行以下命令来查找数据库的当前 TDE 保护程序的指纹：

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

下面的查询返回 VLF 和 TDE 保护程序各自使用的指纹。 每个不同的指纹引用 Azure Key Vault (AKV) 中的不同密钥：

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

或者，可以使用 PowerShell 或 Azure CLI：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令 **Get-AzureRmSqlServerKeyVaultKey** 提供查询中使用的 TDE 保护程序的指纹，因此你可以查看要在 AKV 中保留哪些密钥以及删除哪些密钥。 只能放心地从 Azure Key Vault 中删除数据库不再使用的密钥。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 命令 **az sql server key show** 提供查询中使用的 TDE 保护程序的指纹，因此你可以查看要在 AKV 中保留哪些密钥以及删除哪些密钥。 只能放心地从 Azure Key Vault 中删除数据库不再使用的密钥。

* * *

## <a name="keep-encrypted-resources-accessible"></a>使加密资源保持可访问

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [在 Key Vault 中创建新密钥](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. 使用 [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 和 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护器。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. 确保使用 [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 将服务器和所有副本更新到新的 TDE 保护器。

   > [!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中创建[新密钥的备份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. 使用 [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet 从密钥保管库中删除已泄露的密钥。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 将来若要使用 [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 将密钥还原到 Key Vault：

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需命令参考，请参阅 [Azure CLI keyvault](/cli/azure/keyvault/key)。

1. [在 Key Vault 中创建新密钥](/cli/azure/keyvault/key#az_keyvault_key_create)。 请务必在不包含可能已泄露的 TDE 保护器的另一个 Key Vault 中创建此新密钥，因为访问控制是在保管库级别预配的。

2. 将新密钥添加到服务器，并将其更新为服务器的新 TDE 保护器。

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 确保将服务器和所有副本更新到新的 TDE 保护器。

   > [!NOTE]
   > 将新 TDE 保护器传播到服务器中的所有数据库和辅助数据库可能需要几分钟时间。

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. 在 Key Vault 中创建新密钥的备份。

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. 从 Key Vault 中删除已泄露的密钥。

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 将来将密钥还原到 Key Vault。

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>使加密的资源不可访问

1. 删除可能已泄露的密钥所加密的数据库。

   数据库和日志文件会自动备份，因此，随时可对数据库执行时间点还原（只要提供密钥即可）。 必须在删除活动的 TDE 保护器之前删除数据库，以防止最近事务的数据丢失（最长 10 分钟的数据）。

2. 在 Key Vault 中备份 TDE 保护器的密钥材料。
3. 从 Key Vault 中删除可能已泄露的密钥

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>后续步骤

- 了解如何轮换服务器的 TDE 保护器以符合安全要求：[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-key-rotation.md)
- 开始使用 TDE 的自带密钥支持：[通过 PowerShell 并使用 Key Vault 中自己的密钥来启用 TDE](transparent-data-encryption-byok-configure.md)