---
title: 单一区域驻留
description: 如何在 Azure Synapse Analytics 中为专用 SQL 池（之前称为 SQL DW）配置单一区域驻留
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9fd0860fba93f2aa45616707c791aef498fb06e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580686"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中为专用 SQL 池（之前称为 SQL DW）配置单一区域驻留

在本文中，你将了解如何在本地冗余存储上预配数据库，以确保你的数据保留在你的地理边界内。 本文仅适用于驻留在 Azure 区域对位于国家/地区外的区域的专用 SQL 池。 遵循本文中的步骤会让大多数数据和所有备份都不会复制到[配对区域](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 请注意，某些元数据（例如逻辑服务器名称、逻辑数据库名称和资源组名称）在默认情况下仍会复制到配对区域，该设定无法更改。 

若要实现单一区域驻留，请在预配 SQL 池时对异地冗余选项选择“否”，从而预配专用 SQL 池（之前称为 SQL DW）到本地冗余存储。 如果已预配 SQL 池，并且它驻留在异地冗余存储中，则可以还原 SQL 池，并在还原过程中对异地冗余存储选择“否”。 下文详细介绍了这两种方案。 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>通过 Azure 门户在本地冗余存储上预配专用 SQL 池（之前称为 SQL DW）

按照以下步骤来在本地冗余存储上预配专用 SQL 池（之前称为 SQL DW）：

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。
1. 搜索“专用 SQL 池(以前称为 SQL DW)”。

   ![新建 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. 单击“添加”，然后在“基本信息”选项卡中填写所需的信息*。

1. 如果你的服务器位于区域（如东南亚）内，并且其中的区域对位于不同的地理位置，则可以选择退出异地冗余。 

   ![数据驻留](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. 选择“下一步：网络”以配置公共或专用终结点。 

1. 选择“下一步：其他设置”以从备份、示例还原或创建空白数据库。  

1. 选择“下一步：标记”以配置标记以便对 SQL 池进行分类。 

1. 选择“下一步: 审阅 + 创建”。 确保已选择正确的存储冗余以确保数据驻留。 

    ![创建单一区域池](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. 选择“创建”。  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>通过 PowerShell 在本地冗余存储上预配专用 SQL 池（之前称为 SQL DW）
若要通过 PowerShell 新建专用 SQL 池，请使用 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 在本地冗余存储上新建数据库。
1. 确保已在本地冗余存储中成功创建数据库。  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>通过 Azure 门户在本地冗余存储上还原专用 SQL 池（之前称为 SQL DW）
1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到要从其进行还原的专用 SQL 池。

1. 在“概览”边栏选项卡顶部，选择“还原”。

1. 选择要从中还原的还原点。 

1. 对“异地冗余”选择“否”。 

   ![通过门户还原](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>通过 PowerShell 在本地冗余存储上还原专用 SQL 池（之前称为 SQL DW）
若要通过 PowerShell 新建专用 SQL 池，请使用 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 列出专用 SQL 池（之前称为 SQL DW）的还原点。
1. 使用 RestorePointCreationDate 选取所需的还原点。
1. 使用 Restore-AzSqlDatabase PowerShell cmdlet 将 BackupStorageRedundnacy 指定为“本地”，从而将专用 SQL 池（之前称为 SQL DW）还原到所需还原点。 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>后续步骤

- [还原现有专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-deleted-dw.md)
