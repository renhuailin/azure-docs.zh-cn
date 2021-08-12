---
title: “超大规模”次要副本
description: 本文介绍“超大规模”服务层级中提供的不同类型的次要副本。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 6/9/2021
ms.openlocfilehash: 3c3ff0dd5e8a9dd0b17aad116215a1f6a254d139
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428871"
---
# <a name="hyperscale-secondary-replicas"></a>“超大规模”次要副本
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

如[分布式函数体系结构](service-tier-hyperscale.md)中所述，Azure SQL 数据库“超大规模”提供两种不同类型的计算节点（也称为“副本”）。
- 主要副本：为读取和写入操作提供服务
- 次要副本：提供读取横向扩展、高可用性和异地复制

次要副本有三种不同的类型：

- 高可用性副本
- 命名副本（预览版）
- 异地副本（预览版）

每种类型具有不同的体系结构、功能集、用途和成本。 根据你需要的功能，可以只使用其中的一种类型，也可以将所有三种类型一起使用。

## <a name="high-availability-replica"></a>高可用性副本

高可用性 (HA) 副本使用与主要副本相同的页面服务器，因此无需进行数据复制即可添加 HA 副本。 HA 副本主要用于提供高可用性，因为它们在故障转移中充当热备用服务器。 如果主要副本不可用，将会自动故障转移到现有的 HA 副本之一。 无需更改连接字符串；在故障转移期间，应用程序可能会由于活动连接断开而出现极短暂的工作中断。 就像平常一样，对于这种情况，我们建议实施适当的连接重试逻辑。 有多个驱动程序已经提供了一定程度的自动重试逻辑。 

如果你使用的是 .NET，[最新的 Microsoft.Data.SqlClient](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) 库原生就完全支持可配置的自动重试逻辑。 HA 副本使用与主要副本相同的服务器和数据库名称。 其服务级别目标也始终与主要副本相同。 无法通过门户或者任何其他工具或 DMV 将 HA 副本作为独立的资源来管理。 

可以创建 0 到 4 个 HA 副本。 在创建数据库期间或之后，可以通过一般的管理终结点和工具（例如 PowerShell、AZ CLI、门户或 REST API）更改 HA 副本数量。 创建或删除 HA 副本不会影响主要副本上运行的连接。

### <a name="connecting-to-an-ha-replica"></a>连接到 HA 副本

在超大规模数据库中，客户端使用的连接字符串中的 ApplicationIntent 参数决定了连接是路由到读写主要副本还是只读的 HA 副本。 如果 ApplicationIntent 设置为 `ReadOnly` 并且数据库没有次要副本，则连接将路由到主要副本，并默认采用 `ReadWrite` 行为。

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

考虑到对于给定超大规模数据库而言，所有 HA 副本的资源容量是相同的，因此如果存在多个次要副本，则读取型工作负载将分布在所有可用的 HA 次要副本上。 如果存在多个 HA 副本，请记住，每个 HA 副本在处理主要副本上发生的数据更改方面可能会出现不同的数据延迟。 每个 HA 副本使用与同一组页面服务器上的主要副本相同的数据。 每个 HA 副本上的本地缓存通过事务日志服务来反映主要副本上发生的更改，该服务会将主要副本中的日志记录转发到 HA 副本。 因此，根据 HA 副本所处理的工作负载，应用日志记录的速度可能各不相同，因而不同的 HA 副本相对于主要副本而言可能会出现不同的数据延迟。

## <a name="named-replica-in-preview"></a>命名副本（预览版）

与 HA 副本一样，命名副本使用与主要副本相同的页面服务器。 类似于 HA 副本，无需进行数据复制即可添加命名副本。 

> [!NOTE]
> 有关超大规模命名副本的常见问题解答，请参阅 [Azure SQL 数据库超大规模命名副本常见问题解答](service-tier-hyperscale-named-replicas-faq.yml)。

与 HA 副本之间的差别在于，命名副本： 

- 在门户和 API（CLI、PowerShell、SQL）调用中显示为普通的（只读）Azure SQL 数据库 
- 数据库名称可以不同于主要副本，并且可以选择性地放置在不同的逻辑服务器上（前提是位于主要副本所在的同一区域中） 
- 具有自身的服务级别目标，该目标可以独立于主要副本进行设置和更改
- 最多支持 30 个命名副本（对于每个主要副本而言） 
- 支持通过在托管命名副本的逻辑服务器上创建不同的登录名，对每个命名副本使用不同的身份验证和授权方法

命名副本的主要目标是实现大规模的 OLTP 读取扩展方案，并改善混合事务和分析处理 (HTAP) 工作负载。 下面提供了有关如何创建此类解决方案的示例：

- [OLTP 横向扩展示例](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [HTAP 横向扩展示例](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

除了上面列出的主要方案以外，命名副本还提供其他许多用例所需的灵活性和弹性：
- [访问隔离](hyperscale-named-replica-security-configure.md)：仅为某个登录名授予对某个命名副本的访问权限，拒绝它访问主要副本或其他命名副本。
- 工作负载相关的服务目标：由于命名副本可以有自身的服务级别目标，因此可为不同的工作负载和用例使用不同的命名副本。 例如，可以使用一个命名副本来为 Power BI 请求提供服务，而使用另一个命名副本来为 Apache Spark for Data Science 任务提供数据。 每个命名副本可以有独立的服务级别目标，并可独立缩放。
- 工作负载相关的路由：在命名副本数最多可达 30 个的情况下，可以按组使用命名副本，使应用程序可以相互隔离。 例如，包含四个命名副本的组可用于为来自移动应用程序的请求提供服务，而包含两个命名副本的另一个组可用于为来自 Web 应用程序的请求提供服务。 采用这种方法可以精细地调整每个组的性能和成本。

以下示例为 `WideWorldImporters` 数据库创建服务级别目标为 HS_Gen5_4 的 `WideWorldImporters_NR` 命名副本。 这两个副本使用同一个逻辑服务器 `MyServer`。 如果你更想要直接使用 REST API，则也可以采取以下做法：[数据库 - 创建一个数据库作为命名次要副本](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary)。

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR_" -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

由于不涉及移动数据，因此在大多数情况下，大约只需一分钟就能创建一个命名副本。 创建命名副本后，可以通过门户或任何命令行工具（例如 AZ CLI 或 PowerShell）查看它。 命名副本可用作普通数据库，只不过它是只读的。 

### <a name="connecting-to-a-named-replica"></a>连接到命名副本

若要连接到某个命名副本，必须使用该命名副本的连接字符串。 由于命名副本始终是只读的，因此无需指定选项“ApplicationIntent”。 仍可使用该选项，只是它不会起到任何其他作用。
与 HA 副本一样，即使主要副本、HA 副本和命名副本共享同一组页面服务器上的相同数据，每个命名副本上的缓存也仍会通过事务日志服务来与主要副本保持同步，该服务会将主要副本中的日志记录转发到命名副本。 因此，根据命名副本所处理的工作负载，应用日志记录的速度可能各不相同，因而不同的命名副本相对于主要副本而言可能会出现不同的数据延迟。

### <a name="modifying-a-named-replica"></a>修改命名副本

在创建命名副本时，可以通过 `ALTER DATABASE` 命令或任何其他受支持的方式（AZ CLI、PowerShell 或 REST API）定义该命名副本的服务级别目标。 如果在创建命名副本后需要更改服务级别目标，可以通过对该命名副本本身使用普通的 `ALTER DATABASE…MODIFY` 命令进行更改。 例如，如果 `WideWorldImporters_NR` 是 `WideWorldImporters` 数据库的命名副本，则可按下面所示执行此操作。 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>删除命名副本

可以像删除普通的数据库那样删除命名副本。 请确保已连接到包含所要删除的命名副本的服务器的 `master` 数据库，然后使用以下命令：

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!NOTE]
> 当从中创建了命名副本的主要副本被删除时，这些命名副本也会一并删除。

### <a name="known-issues"></a>已知问题

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>从 sys.databases 返回部分错误的数据
在公共预览版中，从命名副本的 `sys.databases` 返回的行值（在 `name` 和 `database_id` 以外的列中）可能不一致且不正确。 例如，尽管从中创建了命名副本的主数据库设置为 150，但该命名副本的 `compatibility_level` 列有可能会报告为 140。 解决方法（如果可行）是使用系统函数 `databasepropertyex` 获取相同的数据，该函数将返回正确的数据。 


## <a name="geo-replica-in-preview"></a>异地副本（预览版）

使用[活动异地复制](active-geo-replication-overview.md)可以在相同或不同的区域中创建超大规模主数据库的可读次要副本。 必须在不同的逻辑服务器上创建异地副本。 异地副本的数据库名称始终与主要副本的数据库名称相匹配。

创建异地副本时，会将主要副本中的所有数据复制到一组不同的页面服务器。 异地副本不与主要副本共享页面服务器，即使它们位于同一区域。 此体系结构为异地故障转移提供了必要的冗余。

异地副本主要用于保持数据库的事务一致性副本，方法是在一个不同的地理区域中进行异步复制，这样，在主要区域发生灾难或服务中断时便可以实现灾难恢复。 异地副本还可用于异地读取扩展方案。

使用[“超大规模”服务层级中的活动异地复制](active-geo-replication-overview.md)时，必须手动发起故障转移。 故障转移后，新的主要副本将获得一个不同的连接终结点，该终结点引用托管新主要副本的逻辑服务器名称。 有关详细信息，请参阅[活动异地复制](active-geo-replication-overview.md)。

适用于超大规模数据库的异地复制目前为预览版，它存在以下限制：
- （在相同或不同的区域中）只能创建一个异地副本。
- 不支持故障转移组。 
- 不支持计划内故障转移。
- 不支持异地副本的时间点还原
- 不支持创建异地副本的数据库副本。 
- 不支持次要副本的次要副本（也称为“异地副本链”）。 

## <a name="next-steps"></a>后续步骤

- [“超大规模”服务层级](service-tier-hyperscale.md)
- [活动异地复制](active-geo-replication-overview.md)
- [配置安全性以允许对 Azure SQL 数据库超大规模命名副本进行独立访问](hyperscale-named-replica-security-configure.md)
- [Azure SQL 数据库超大规模命名副本常见问题解答](service-tier-hyperscale-named-replicas-faq.yml)
