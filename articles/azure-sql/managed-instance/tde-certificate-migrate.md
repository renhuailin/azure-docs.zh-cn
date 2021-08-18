---
title: 迁移 TDE 证书 - 托管实例
description: 将用于通过透明数据加密保护数据库加密密钥的证书迁移到 Azure SQL 托管实例
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma, jovanpop
ms.date: 06/01/2021
ms.openlocfilehash: f168dca243ae09ccdf39eb7630b350cd2b928628
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733336"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>将 TDE 保护的数据库的证书迁移到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

使用本机还原选项将[透明数据加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) 保护的数据库迁移到 Azure SQL 托管实例时，需在还原数据库之前迁移 SQL Server 实例中的相应证书。 本文引导你完成将证书手动迁移到 Azure SQL 托管实例的过程：

> [!div class="checklist"]
>
> * 将证书导出到个人信息交换 (.pfx) 文件
> * 将文件中的证书提取成 base-64 字符串
> * 使用 PowerShell cmdlet 上传证书

有关使用完全托管服务无缝迁移 TDE 保护的数据库和相应证书的替代选项，请参阅[如何使用 Azure 数据库迁移服务将本地数据库迁移到 Azure SQL 托管实例](../../dms/tutorial-sql-server-to-managed-instance.md)。

> [!IMPORTANT]
> 迁移的证书仅用于还原 TDE 保护的数据库。 还原后不久，迁移的证书即会替换为不同的保护程序，可能是服务托管证书，也可能是密钥保管库中的非对称密钥，具体取决于在实例上设置的 TDE 类型。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要符合以下先决条件：

* 已在本地服务器上，或者有权访问导出为文件的证书的计算机上，安装了 [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) 命令行工具。 Pvk2Pfx 工具是[企业 Windows 驱动程序工具包](/windows-hardware/drivers/download-the-wdk)（一个自包含性命令行环境）的一部分。
* 已安装 [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) 5.0 或更高版本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

确保具有以下内容：

* [已安装并更新](/powershell/azure/install-az-ps) Azure PowerShell 模块。
* [Az.Sql 模块](https://www.powershellgallery.com/packages/Az.Sql)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 托管实例的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRM 模块中的命令参数大体上是相同的。

在 PowerShell 中运行以下命令，以安装/更新模块：

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>将 TDE 证书导出为 .pfx 文件

可直接从源 SQL Server 实例导出证书，或者从证书存储导出（如果证书保存在证书存储中）。

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>从源 SQL Server 实例导出证书

在 SQL Server Management Studio 中使用以下步骤导出证书，并将其转换为 .pfx 格式。 以下各个步骤使用通用名称 TDE_Cert 和 full_path 作为证书名称（文件名）和路径 。 应将它们替换为实际名称。

1. 在 SSMS 中打开新的查询窗口，并连接到源 SQL Server 实例。

1. 使用以下脚本列出 TDE 保护的数据库，并获取用于对要迁移的数据库进行保护加密的证书的名称：

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE 证书列表](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. 执行以下脚本，将证书导出到一对文件（.cer 和 .pvk），并保存公钥和私钥信息：

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![备份 TDE 证书](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. 在 PowerShell 控制台中使用 Pvk2Pfx 工具，将证书信息从一对新建文件复制到 .pfx 文件：

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>从证书存储中导出证书

如果证书保存在 SQL Server 本地计算机证书存储中，可使用以下步骤导出该证书：

1. 打开 PowerShell 控制台并执行以下命令，打开 Microsoft 管理控制台的“证书”管理单元：

   ```cmd
   certlm
   ```

2. 在“证书”MMC 管理单元中，展开路径“个人”>“证书”以查看证书列表。

3. 右键单击证书，然后单击“导出”。

4. 遵循向导将证书和私钥导出为 .pfx 格式。

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>使用 Azure PowerShell cmdlet 将证书上传到 Azure SQL 托管实例

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 在 PowerShell 中开始准备步骤：

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. 完成所有准备步骤后，运行以下命令将 base-64 编码的证书上传到目标托管实例：

   ```azurepowershell
   # If you are using PowerShell 6.0 or higher, run this command:
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   # If you are using PowerShell 5.x, uncomment and run this command instead of the one above:
   # $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先需要使用 .pfx 文件[设置 Azure 密钥保管库](../../key-vault/general/manage-with-cli2.md)。

1. 在 PowerShell 中开始准备步骤：

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. 完成所有准备步骤后，运行以下命令将 base-64 编码的证书上传到目标托管实例：

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

现在，该证书可在指定的托管实例中使用，并且可以成功还原相应的 TDE 保护数据库的备份。

## <a name="next-steps"></a>后续步骤

本文已介绍如何将用于通过透明数据加密保护数据库加密密钥的证书从本地或 IaaS SQL Server 实例迁移到 Azure SQL 托管实例。

请参阅[将数据库备份还原到 Azure SQL 托管实例](restore-sample-database-quickstart.md)，了解如何将数据库备份还原到 Azure SQL 托管实例。
