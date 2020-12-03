---
title: 用户定义的还原点
description: 如何创建 (以前的 SQL DW) 专用 SQL 池的还原点。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 097a3132208eee98b3f95291e414263e637bc265
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545581"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a> (以前的 SQL DW) 的专用 SQL 池的用户定义的还原点

在本文中，你将了解如何使用 PowerShell 和 Azure 门户在 Azure Synapse Analytics 中为专用 (SQL DW) 创建新的用户定义的还原点。

## <a name="create-user-defined-restore-points-through-powershell"></a>通过 PowerShell 创建用户定义的还原点

若要创建用户定义的还原点，请使用 [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 为数据仓库的即时复制创建还原点。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 查看所有现有还原点的列表。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>通过 Azure 门户创建用户定义的还原点

还可以通过 Azure 门户创建用户定义的还原点。

1. 登录到 [Azure 门户](https://portal.azure.com/)帐户。

2. 导航到要为其创建还原点 (以前的 SQL DW) 的专用 SQL 池。

3. 从左窗格中选择“概述”  ，选择“+ 新建还原点”  。 如果 "新还原点" 按钮未启用，请确保专用 SQL 仓库 (以前的 SQL DW) 未暂停。

    ![新建还原点](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 为用户定义的还原点指定名称，然后单击“应用”  。 用户定义的还原点的默认保留期为七天。

    ![还原点的名称](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>后续步骤

- [还原现有的专用 SQL 池 (以前的 SQL DW) ](sql-data-warehouse-restore-active-paused-dw.md)
- [还原已删除的专用 SQL 池 (以前的 SQL DW) ](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份专用 SQL 池还原 (以前的 SQL DW) ](sql-data-warehouse-restore-from-geo-backup.md)
