---
title: 导入 BACPAC 文件以在 Azure SQL 数据库中创建数据库
description: 在 Azure SQL 数据库或 Azure SQL 托管实例中，从 BACPAC 文件创建新数据库。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: rothja
ms.author: jroth
ms.reviewer: cawrites
ms.date: 10/29/2020
ms.openlocfilehash: 7f2709884f3ac8ce217ac88183a2491f9e931a25
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458874"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：将 BACPAC 文件导入 Azure SQL 数据库或 Azure SQL 托管实例中的数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

你可以使用 [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 文件将 SQL Server 数据库导入 Azure SQL 数据库或 SQL 托管实例。 可以从 Azure Blob 存储（仅限标准存储）中存储的 BACPAC 文件或从本地位置中的本地存储导入数据。 若要通过提供更多且更快的资源将导入速度最大化，请在导入过程中将数据库扩展到更高的服务层级和更大的计算大小。 然后，可以在导入成功后进行缩减。

> [!NOTE]
> 导入的数据库的兼容性级别基于源数据库的兼容性级别。

> [!IMPORTANT]
> 导入数据库后，可选择以当前兼容级别（对于 AdventureWorks2008R2 数据库是级别 100）或更高级别操作数据库。 有关在特定兼容级别操作数据库的影响和选项的详细信息，请参阅 [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)（更改数据库兼容级别）。 有关与兼容级别相关的其他数据库级别设置的信息，另请参阅 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)（更改数据库范围的配置）。

## <a name="using-azure-portal"></a>使用 Azure 门户

观看此视频，了解如何在 Azure 门户中从 BACPAC 文件中导入，或继续阅读以下内容：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure 门户](https://portal.azure.com)仅支持在 Azure SQL 数据库中创建单个数据库，并且仅从存储在 Azure Blob 存储中的 BACPAC 文件中创建 。

若要将数据库从 BACPAC 文件迁移到 [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)中，请使用 SQL Server Management Studio 或 SQLPackage，当前不支持使用 Azure 门户或 Azure PowerShell。

> [!NOTE]
> 处理通过 Azure 门户或 PowerShell 提交的导入/导出请求的计算机需要存储 BACPAC 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 相同大小的数据库之间所需的磁盘空间差异很大，所需的磁盘空间最多可能是数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450GB 的本地磁盘空间。 因此，某些请求可能会失败，并显示错误 `There is not enough space on the disk`。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 我们建议使用 SqlPackage 导入/导出大于 150GB 的数据库以避免此问题。

1. 若要使用 Azure 门户从 BACPAC 文件导入新的单个数据库，请打开相应的服务器页面，然后在工具栏上选择“导入数据库”。  

   ![数据库 import1](./media/database-import/sql-server-import-database.png)

1. 选择存储帐户和 BACPAC 文件的容器，然后选择要从中导入的 BACPAC 文件。

1. 指定新数据库大小（通常与源数据库相同）并提供目标 SQL Server 凭据。 有关 Azure SQL 数据库中新数据库的可能值列表，请参阅[创建数据库](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)。

   ![数据库 import2](./media/database-import/sql-server-import-database-settings.png)

1. 单击 **“确定”** 。

1. 若要监视导入的进度，请打开数据库的服务器页，然后在“设置”下，选择“导入/导出历史记录”。 成功导入后，状态为“已完成”。

   ![数据库导入状态](./media/database-import/sql-server-import-database-history.png)

1. 若要验证数据库在服务器上是否处于活动状态，请选择“SQL 数据库”并验证新数据库是否为“联机”。

## <a name="using-sqlpackage"></a>使用 SqlPackage

若要使用 [SqlPackage](/sql/tools/sqlpackage) 命令行实用程序导入 SQL Server 数据库，请参阅[导入参数和属性](/sql/tools/sqlpackage#import-parameters-and-properties)。 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) 包括 SqlPackage。 还可以从 Microsoft 下载中心下载最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。 

在大多数生产环境中，建议使用 SqlPackage 而不是 Azure 门户来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 `BACPAC` 文件进行迁移的博客，请参阅[使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)。

基于 DTU 的预配模型支持为每个层选择数据库最大大小值。 导入数据库时，请[使用其中一种受支持的值](/sql/t-sql/statements/create-database-transact-sql)。 

以下 SqlPackage 命令可将 AdventureWorks2008R2 数据库从本地存储导入到名为 mynewserver20170403 的逻辑 SQL 服务器 。 它将创建名为 myMigratedDatabase 的新数据库，其中包含“高级”服务层级和 P6 服务目标。 根据你的环境更改这些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要从公司防火墙后连接到 Azure SQL 数据库，该防火墙必须打开端口 1433。 若要连接到 SQL 托管实例，必须有[点到站点连接](../managed-instance/point-to-site-p2s-configure.md)或快速路由连接。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>使用 PowerShell

> [!NOTE]
> [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)当前不支持使用 Azure PowerShell 从 BACPAC 文件将数据库迁移到实例数据库。 若要导入 SQL 托管实例，请使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过门户或 Powershell 提交的导入/导出请求的计算机需要存储 bacpac 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 所需的磁盘空间在具有相同大小的 DB 之间存在显著差异，并且最多可占数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450GB 的本地磁盘空间。 因此，某些请求可能会失败，出现“磁盘空间不足”错误。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 导入/导出大于 150GB 的数据库时，请使用 SqlPackage 来避免此问题。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器 (RM) 模块，但是所有未来的开发都是针对 Az.Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

使用 [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet 向 Azure 提交导入数据库请求。 根据数据库大小，导入操作可能需要一些时间才能完成。 基于 DTU 的预配模型支持为每个层选择数据库最大大小值。 导入数据库时，请[使用其中一种受支持的值](/sql/t-sql/statements/create-database-transact-sql)。 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

可以使用 [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 检查导入的进度。 如果在提交请求后立即运行此 cmdlet，通常会返回 `Status: InProgress`。 当看到 `Status: Succeeded` 时，导入已完成。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az-sql-db-import](/cli/azure/sql/db#az_sql_db_import) 命令向 Azure 提交导入数据库请求。 根据数据库大小，导入操作可能需要一些时间才能完成。 基于 DTU 的预配模型支持为每个层选择数据库最大大小值。 导入数据库时，请[使用其中一种受支持的值](/sql/t-sql/statements/create-database-transact-sql)。 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> 有关另一个脚本示例，请参阅[从 BACPAC 文件导入数据库](scripts/import-from-bacpac-powershell.md)。

## <a name="cancel-the-import-request"></a>取消导入请求

使用[数据库操作 - 取消 API](/rest/api/sql/databaseoperations/cancel) 或 Powershell [Stop-AzSqlDatabaseActivity 命令](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)，此处是 Powershell 命令的示例。

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>限制

- 不支持导入到弹性池中的数据库。 可以将数据导入到单一数据库，然后将数据库移到弹性池。
- 当“允许访问 Azure 服务”设置为“关闭”时，导入/导出服务无法正常工作。 不过，可通过以下方式解决此问题：在 Azure VM 中手动运行 sqlpackage.exe，或者直接在代码中使用 DACFx API 执行导出。
- 导入操作不支持在创建新数据库时指定备份存储冗余，而是会使用默认异地冗余备份存储冗余创建新数据库。 若要解决此问题，请首先使用 Azure 门户或 PowerShell 和所需的备份存储冗余创建一个空数据库，然后将 BACPAC 导入此空数据库。 

> [!NOTE]
> “Azure SQL 数据库可配置备份存储冗余”目前为公共预览版，且仅在东南亚 Azure 区域提供。

## <a name="import-using-wizards"></a>使用向导导入

还可以使用以下向导。

- [SQL Server Management Studio 中的导入数据层应用程序向导](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 导入和导出向导](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何在 Azure SQL 数据库中连接和查询数据库，请参阅[快速入门：Azure SQL 数据库：使用 SQL Server Management Studio 连接并查询数据](connect-query-ssms.md)。
- 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](migrate-to-database-from-sql-server.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](../../storage/blobs/security-recommendations.md)。