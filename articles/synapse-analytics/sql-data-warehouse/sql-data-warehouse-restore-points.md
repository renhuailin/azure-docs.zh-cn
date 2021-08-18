---
title: 用户定义的还原点
description: 如何创建专用 SQL 池（以前称为 SQL DW）的还原点。
services: synapse-analytics
author: rothja
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: jroth
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 39f8c336fdddf58d6cfa9cd8162e974d3dbc7c2d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450940"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>专用 SQL 池（以前称为 SQL DW）的用户定义还原点

本文介绍如何使用 PowerShell 和 Azure 门户为 Azure Synapse Analytics 中的专用 SQL 池（以前称为 SQL DW）新建用户定义的还原点。

## <a name="create-user-defined-restore-points-through-powershell"></a>通过 PowerShell 创建用户定义的还原点

若要创建用户定义的还原点，请使用 [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 为数据仓库的即时复制创建还原点。

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label
```

6. 查看所有现有还原点的列表。

```powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>通过 Azure 门户创建用户定义的还原点

还可以通过 Azure 门户创建用户定义的还原点。

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。

2. 导航到需要为其创建还原点的专用 SQL 池（以前称为 SQL DW）。

3. 从左窗格中选择“概述”  ，选择“+ 新建还原点”  。 如果“新建还原点”按钮未启用，请确保专用 SQL 池（以前称为 SQL DW）未暂停。

    ![新建还原点](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 为用户定义的还原点指定名称，然后单击“应用”  。 用户定义的还原点的默认保留期为七天。

    ![还原点的名称](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>后续步骤

- [还原现有专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份专用 SQL 池（以前称为 SQL DW）进行还原](sql-data-warehouse-restore-from-geo-backup.md)
