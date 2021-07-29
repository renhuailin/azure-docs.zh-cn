---
title: 复制数据库
description: 在相同或不同的服务器上创建 Azure SQL 数据库中现有数据库的事务一致性副本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma
ms.date: 03/10/2021
ms.openlocfilehash: 325a2feb0cf29a03a88249e2d0ac3a22f685d498
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110694546"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>复制 Azure SQL 数据库中数据库的事务一致性副本

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

通过 Azure SQL 数据库，可以以多种方式在相同或不同的服务器上创建现有[数据库](single-database-overview.md)的副本。 可以使用 Azure 门户、PowerShell、Azure CLI 或 T-SQL 复制数据库。

## <a name="overview"></a>概述

数据库副本是源数据库在发起复制请求后的某个时间点的事务一致快照。 可以为副本选择同一服务器或其他服务器。 此外，你还可以选择保留源数据库的备份冗余、服务层和计算大小，或在相同或不同服务层中使用不同的备份存储冗余和/或计算大小。 在完成该复制后，副本将成为能够完全行使功能的独立数据库。 复制的数据库中的登录名、用户和权限独立于源数据库进行管理。 副本使用异地复制技术创建。 副本种子设定完成后，异地复制链接会自动终止。 使用异地复制的所有要求都适用于数据库复制操作。 有关详细信息，请参阅[活动异地复制概述](active-geo-replication-overview.md)。

> [!NOTE]
> Azure SQL 数据库可配置备份存储冗余目前在巴西南部提供公共预览版，且仅在 Azure 东南亚地区正式发布。 在预览中，如果源数据库是使用本地冗余或区域冗余备份存储冗余创建的，则不支持将数据库副本复制到其他 Azure 区域中的服务器。 

## <a name="logins-in-the-database-copy"></a>数据库副本中的登录名

将某个数据库复制到同一服务器时，可以在这两个数据库上使用相同的登录名。 用于复制该数据库的安全主体将成为新数据库上的数据库所有者。

将数据库复制到其他服务器时，由目标服务器上的复制操作启动的安全主体将成为新数据库的所有者。

无论目标服务器如何，所有数据库用户、其权限及安全标识符 (SID) 都会复制到数据库副本中。 使用[包含的数据库用户](logins-create-manage.md)进行数据访问可以确保复制的数据库具有相同的用户凭据，这样在复制完成后，你可以使用相同的凭据立即进行访问。

如果使用服务器级登录名进行数据访问并将数据库复制到其他服务器，则基于登录名的访问可能无法工作。 出现这种情况的原因可能是目标服务器上不存在登录名，或者其密码和安全标识符 (SID) 不同。 要了解如何在将数据库复制到其他服务器时管理登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](active-geo-replication-security-configure.md)。 在复制到其他服务器操作成功后，但在重新映射其他用户之前，只有与数据库所有者关联的登录名或服务器管理员才能登录到复制的数据库。 若要在复制操作完成后解析登录名并建立数据访问权限，请参阅[解析登录名](#resolve-logins)。

## <a name="copy-using-the-azure-portal"></a>使用 Azure 门户复制

要使用 Azure 门户复制数据库，请打开数据库页，并单击“复制”。

   ![数据库复制](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>使用 PowerShell 或 Azure CLI 进行复制

若要复制数据库，请使用以下示例。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell，请使用 [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet。

> [!IMPORTANT]
> PowerShell Azure 资源管理器 (RM) 模块仍受 Azure SQL 数据库支持，但所有未来的开发都是针对 Az.Sql 模块的。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

数据库复制是一个异步操作，但在接受请求后会立即创建目标数据库。 如果需要取消仍在进行的复制操作，请使用 [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 删除目标数据库。

有关完整的示例 PowerShell 脚本，请参阅[将数据库复制到新的服务器](scripts/copy-database-to-new-server-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

数据库复制是一个异步操作，但在接受请求后会立即创建目标数据库。 如果需要取消仍在进行的复制操作，请使用 [az sql db delete](/cli/azure/sql/db#az_sql_db_delete) 命令删除目标数据库。

* * *

## <a name="copy-using-transact-sql"></a>使用 Transact-SQL 进行复制

使用服务器管理员登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器管理员的登录名必须是 `dbmanager` 角色的成员。 有关登录名和链接到服务器的详细信息，请参阅[管理登录名](logins-create-manage.md)。

使用 [CREATE DATABASE ...AS COPY OF](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) 语句开始复制源数据库。 T-SQL 语句将继续运行，直到数据库复制操作完成。

> [!NOTE]
> 终止 T-SQL 语句不会终止数据库复制操作。 若要终止该操作，请删除目标数据库。
>

> [!IMPORTANT]
> 使用 T-SQL CREATE DATABASE 时选择备份存储冗余…尚不支持 AS COPY OF 命令。 

### <a name="copy-to-the-same-server"></a>复制到同一服务器

使用服务器管理员登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器管理员的登录名必须是 `dbmanager` 角色的成员。

此命令将 Database1 复制到同一服务器上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>复制到弹性池

使用服务器管理员登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器管理员的登录名必须是 `dbmanager` 角色的成员。

此命令将 Database1 复制到名为 pool1 的弹性池中名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

Database1 可以是单一数据库或共用数据库。 支持在不同层级的池之间进行复制，但有些跨层复制不会成功。 例如，可以将单一数据库或弹性标准数据库复制到常规用途的池中，但无法将标准弹性数据库复制到高级池中。 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>复制到其他服务器

登录到要在其中创建新数据库的目标服务器的 master 数据库。 所用登录名的名称和密码应该与源服务器上源数据库的数据库所有者的名称和密码相同。 目标服务器上的登录名还必须是 `dbmanager` 角色的成员，或者是服务器管理员登录名。

此命令将 server1 上的 Database1 复制到 server2 上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 必须将两台服务器的防火墙都配置为允许来自发出 T-SQL CREATE DATABASE ...AS COPY OF 命令的客户端 IP 的入站连接。

### <a name="copy-to-a-different-subscription"></a>复制到其他订阅

可以按[将 SQL 数据库复制到其他服务器](#copy-to-a-different-server)部分中的步骤操作，使用 T-SQL 将数据库复制到其他订阅中的服务器。 确保所用登录名的名称和密码与源数据库的数据库所有者的名称和密码相同。 此外，登录名在源服务器和目标服务器上都必须是 `dbmanager` 角色的成员或者是服务器管理员。

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> [Azure 门户](https://portal.azure.com)、PowerShell 和 Azure CLI 不支持将数据库复制到其他订阅。

> [!TIP]
> 使用 T-SQL 的数据库复制操作支持从不同 Azure 租户中的订阅复制数据库。 仅当使用 SQL 身份验证登录名来登录目标服务器时才支持此项。
> 当源或目标逻辑服务器上的 [Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/support-for-azure-ad-user-creation-on-behalf-of-azure-ad/ba-p/2346849) 身份验证处于活动（已启用）状态时，不支持在其他 Azure 租户的逻辑服务器上创建数据库副本。

## <a name="monitor-the-progress-of-the-copying-operation"></a>监视复制操作的进度

可以通过查询 [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)、[sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图来监视复制过程。 在复制过程中，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **COPYING**。

* 如果复制失败，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **SUSPECT**。 对新数据库执行 DROP 语句并稍后重试。
* 如果复制成功，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **ONLINE**。 复制已完成并且新数据库是一个常规数据库，可独立于源数据库进行更改。

> [!NOTE]
> 如果决定在复制过程中取消复制，请对新数据库执行 [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) 语句。

> [!IMPORTANT]
> 如果需要使用比源小得多的服务目标创建副本，那么目标数据库可能没有足够的资源来完成种子设定过程，这可能会导致复制操作失败。 在这种情况下，请使用异地还原请求在不同服务器和/或不同区域中创建副本。 有关详细信息，请参阅[使用数据库备份恢复 Azure SQL 数据库](recovery-using-backups.md#geo-restore)。

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>管理数据库副本的 Azure RBAC 角色和权限

若要创建数据库副本，需要具有以下角色

* “订阅所有者”或
* “SQL Server 参与者”角色或
* 对源和目标数据库具有以下权限的自定义角色：

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

若要取消数据库复制，需要具有以下角色

* “订阅所有者”或
* “SQL Server 参与者”角色或
* 对源和目标数据库具有以下权限的自定义角色：

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

若要使用 Azure 门户管理数据库副本，还需要以下权限：

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

若要查看门户上资源组中部署下的操作、跨多个资源提供程序的操作（包括 SQL 操作），还需要以下权限：

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>解析登录名

当新数据库在目标服务器上联机后，使用 [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) 语句将新数据库中的用户重新映射到目标服务器上的登录名。 若要解析孤立用户，请参阅[孤立用户疑难解答](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)。 另请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](active-geo-replication-security-configure.md)。

新数据库中的所有用户都保持他们在源数据库中已有的权限。 启动数据库复制过程的用户成为新数据库的数据库所有者。 复制成功之后，重新映射其他用户之前，只有数据库所有者才能登录到新数据库。

若要了解如何在将数据库复制到其他服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库的安全性](active-geo-replication-security-configure.md)。

## <a name="database-copy-errors"></a>数据库复制错误

在 Azure SQL 数据库中复制数据库时，可能会发生以下错误。 有关详细信息，请参阅[复制 Azure SQL 数据库](database-copy.md)。

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 40635 |16 |IP 地址为“%.&#x2a;ls”的客户端已暂时禁用。 |
| 40637 |16 |创建数据库副本当前处于禁用状态。 |
| 40561 |16 |数据库复制失败。 源数据库或目标数据库不存在。 |
| 40562 |16 |数据库复制失败。 源数据库已删除。 |
| 40563 |16 |数据库复制失败。 目标数据库已删除。 |
| 40564 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40565 |16 |数据库复制失败。 不允许来自同一源的多个并发数据库复制。 请删除目标数据库，并重试。 |
| 40566 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40567 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40568 |16 |数据库复制失败。 源数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40569 |16 |数据库复制失败。 目标数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40570 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40571 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |

## <a name="next-steps"></a>后续步骤

* 有关登录名的信息，请参阅[管理登录名](logins-create-manage.md)和[灾难恢复后如何管理 Azure SQL 数据库安全性](active-geo-replication-security-configure.md)。
* 要导出数据库，请参阅[将数据库导出到 BACPAC](database-export.md)。
