---
title: 跨云数据库的分布式事务（预览版）
description: Azure SQL 数据库和 Azure SQL 托管实例的弹性数据库事务概述。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: scoriani
ms.author: scoriani
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: c5586c425de4ca33565df39b1839bcab075faee5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110690112"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>跨云数据库的分布式事务（预览版）
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用 Azure SQL 数据库和 Azure SQL 托管实例的弹性数据库事务，可以运行跨多个数据库的事务。 弹性数据库事务适用于使用 ADO.NET 的 .NET 应用程序，并且与你熟悉的使用 [System.Transaction](/dotnet/api/system.transactions) 类的编程体验相集成。 若要获取该库，请参阅 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981)（Web 安装程序）。
此外，对于托管实例，[Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 中提供了分布式事务。

在本地，这种方案通常需要运行 Microsoft 分布式事务处理协调器 (MSDTC)。 由于 MSDTC 不适用于 Azure 中的平台即服务应用程序，因此现在已将协调分布式事务的功能直接集成到 SQL 数据库或托管实例中。 应用程序可以连接到任何数据库来启动分布式事务，其中一个数据库或服务器会以透明方式协调分布式事务，如下图所示。

在本文档中，“分布式事务”和“弹性数据库事务”这两个术语被视为同义词，可以互换使用。

  ![使用弹性数据库事务在 Azure SQL 数据库中执行分布式事务 ][1]

## <a name="common-scenarios"></a>常见方案

弹性数据库事务可让应用程序对多个不同数据库中存储的数据进行原子性更改。 预览版着重于 C# 和 .NET 的客户端开发体验。 使用 [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 的服务器端体验（以存储过程或服务器端脚本编写的代码）仅适用于托管实例。
> [!IMPORTANT]
> 在预览版中，目前不支持在 Azure SQL 数据库与 Azure SQL 托管实例之间运行弹性数据库事务。 弹性数据库事务只能跨越一组 SQL 数据库或一组托管实例。

弹性数据库事务面向以下方案：

* Azure 中的多数据库应用程序：在此方案中，数据垂直分区到 SQL 数据库或托管实例中的多个数据库，使不同类型的数据位于不同的数据库。 某些操作需要更改两个或更多数据库中保存的数据。 应用程序使用弹性数据库事务来协调数据库之间的更改并确保原子性。
* Azure 中的分片数据库应用程序：在此方案中，数据层使用[弹性数据库客户端库](elastic-database-client-library.md)或自我分片，将数据水平分区到 SQL 数据库或托管实例中的许多数据库。 常见的用例之一是在分片的多租户应用程序中，当更改涉及到多个租户时，需要执行原子更改。 例如，从一个租户转移到另一个租户，而两者位于不同的数据库。 第二种方案是以细致分片来适应大租户的容量需求，这又通常表示某些原子操作需要扩展到用于同一租户的多个数据库。 第三种方案是以原子更新来引用数据库之间复制的数据。 现在，可以使用预览版跨多个数据库协调这几个方面原子性事务操作。
  弹性数据库事务使用两阶段提交，确保跨数据库的事务原子性。 如果单个事务一次涉及到的数据库少于 100 个，则适合采用此方案。 这些限制不是强制施加的，但是如果超出这些限制，弹性数据库事务的性能和成功率很有可能会下降。

## <a name="installation-and-migration"></a>安装和迁移

我们更新了 .NET 库 System.Data.dll 和 System.Transactions.dll，以提供弹性数据库事务功能。 DLL 确保必要时使用两阶段事务提交，以确保原子性。 若要使用弹性数据库事务来开始开发应用程序，请安装 [.NET 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 或更高版本。 在旧版 .NET Framework 上运行时，事务无法升级为分布式事务，并会引发异常。

安装后，可以通过连接到 SQL 数据库和托管实例来使用 System.Transactions 中的分布式事务 API。 如果现有的 MSDTC 应用程序使用了这些 API，请在安装 4.6.1 Framework 之后，以 .NET 4.6 为目标重建现有的应用程序。 如果项目以 .NET 4.6 为目标，则会自动使用 Framework 新版本中更新的 DLL，结合 SQL 数据库或托管实例连接的分布式事务 API 调用现在会成功。

请记住，弹性数据库事务不需要安装 MSDTC。 弹性数据库事务改为直接由该服务进行管理。 这可大幅简化云方案，因为不必部署 MSDTC 即可结合使用分布式事务和 SQL 数据库或托管实例。 第 4 部分更详细说明了如何将弹性数据库事务和所需的 .NET Framework 连同云应用程序一起部署到 Azure。

## <a name="net-installation-for-azure-cloud-services"></a>适用于 Azure 云服务的 .NET 安装

Azure 为托管 .NET 应用程序提供了多个产品。 不同产品的比较可见于 [Azure 应用服务、云服务和虚拟机比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。 如果产品的来宾 OS 版本低于弹性事务所需的 .NET 4.6.1，需要将来宾 OS 升级到 4.6.1。

对于 Azure 应用服务，当前不支持升级到来宾 OS。 对于 Azure 虚拟机，只需要登录到 VM 并运行最新的 .NET framework 安装程序即可。 对于 Azure 云服务，需要将更高版本的 .NET 安装包括到部署的启动任务中。 [在云服务角色上安装 .NET](../../cloud-services/cloud-services-dotnet-install-dotnet.md) 中说明了概念和步骤。  

请注意，与 .NET 4.6 的安装程序相比，.NET 4.6.1 的安装程序在 Azure 云服务上执行引导过程时，可能需要更多的临时存储空间。 为了确保安装成功，需要在 ServiceDefinition.csdef 文件中启动任务的 LocalResources 部分和环境设置中，增加 Azure 云服务的临时存储，如以下示例所示：

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET 开发体验

### <a name="multi-database-applications"></a>多数据库应用程序

以下示例代码使用熟悉的 .NET System.Transactions 编程体验。 TransactionScope 类在 .NET 中创建环境事务。 （“环境事务”是位于当前线程中的事务。）在 TransactionScope 内打开的所有连接都参与该事务。 如果有不同的数据库参与，事务自动提升为分布式事务。 通过设置完成范围来指示提交，即可控制事务的结果。

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>分片数据库应用程序

SQL 数据库和托管实例的弹性数据库事务还支持协调分布式事务，这需要使用弹性数据库客户端库的 OpenConnectionForKey 方法来打开横向扩展的数据层的连接。 假设需要保证事务一致性，使更改跨多个不同的分片键值。 与托管不同分片键值的分片的连接由 OpenConnectionForKey 来中转。 在一般情况下，可以连接到不同的分片，以确保事务保证需要分布式事务。
以下代码示例演示了此方法。 假设使用一个称为 shardmap 的变量代表来自弹性数据库客户端库的分片映射：

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-SQL 开发体验

使用 Transact-SQL 的服务器端分布式事务仅适用于 Azure SQL 托管实例。 只能在属于同一[服务器信任组](../managed-instance/server-trust-group-overview.md)的托管实例之间执行分布式事务。 在这种情况下，托管实例需要使用[链接服务器](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure)来相互引用。

下面的示例 Transact-SQL 代码使用 [BEGIN DISTRIBUTED TRANSACTION](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 来启动分布式事务。

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>组合 .NET 和 Transact-SQL 开发体验

使用 System.Transaction 类的 .NET 应用程序可以将 TransactionScope 类与 Transact-SQL 语句 BEGIN DISTRIBUTED TRANSACTION 组合使用。 在 TransactionScope 内，执行 BEGIN DITRIBUTED TRANSACTION 的内部事务将显式提升为分布式事务。 此外，在 TransactionScope 内打开第二个 SqlConnecton 时，它会被隐式提升为分布式事务。 在分布式事务启动之后，所有后续事务请求（无论是来自 .NET 还是来自 Transact-SQL）都将加入父分布式事务。 因此，由 BEGIN 语句启动的所有嵌套事务范围将在同一事务中结束，COMMIT/ROLLBACK 语句将对总体结果产生以下影响：
 * COMMIT 语句不会对由 BEGIN 语句启动的事务范围产生任何影响，也就是说，在 TransactionScope 对象上调用 Complete() 方法之前，不会提交任何结果。 如果 TransactionScope 对象在完成前被销毁，则会回滚在该范围内执行的所有更改。
 * ROLLBACK 语句将导致回滚整个 TransactionScope。 之后，任何在 TransactionScope 中登记新事务的尝试都会失败，尝试在 TransactionScope 对象上调用 Complete() 也会失败。

下面是一个示例，其中使用了 Transact-SQL 将事务显式提升为分布式事务。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

下面的示例展示了在 TransactionScope 中启动第二个 SqlConnecton 后会被隐式提升为分布式事务的事务。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Azure SQL 数据库的跨多个服务器的事务

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

Azure SQL 数据库中支持跨不同服务器的弹性数据库事务。 当事务跨越服务器边界时，参与的服务器首先需要进入相互通信关系。 一旦建立了通信关系，任意两个服务器中的任何数据库都可以与另一服务器的数据库参与弹性事务。 当事务跨越两个以上的服务器时，任意服务器对之间的通信关系需要准备就绪。

使用以下 PowerShell cmdlet 来管理弹性数据库事务的跨服务器通信关系：

* **New-AzSqlServerCommunicationLink**：使用此 cmdlet 在 Azure SQL 数据库中的两个服务器之间创建新的通信关系。 这种关系是对称的，这意味着一台服务器可使用另一台服务器启动事务。
* **Get-AzSqlServerCommunicationLink**：使用此 cmdlet 来检索现有通信关系及其属性。
* **Remove-AzSqlServerCommunicationLink**：使用此 cmdlet 来删除现有通信关系。

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的跨多个服务器的事务

Azure SQL 托管实例中支持跨不同服务器的分布式事务。 当事务跨托管实例边界时，参与的实例首先需要进入一个可确保相互安全并可相互通信的关系。 这是通过创建[服务器信任组](../managed-instance/server-trust-group-overview.md)来完成的，可以在 Azure 门户上完成此操作。 如果托管实例不在同一虚拟网络中，则需要设置[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)，并且在所有参与的虚拟网络上，网络安全组入站和出站规则都需要允许端口 5024 和 11000-12000。

  ![Azure 门户上的服务器信任组][3]

下图显示了包含托管实例的服务器信任组，这些实例可以使用 .NET 或 Transact-SQL 执行分布式事务。

  ![使用弹性事务在 Azure SQL 托管实例中执行分布式事务][2]

## <a name="monitoring-transaction-status"></a>监视事务状态

使用动态管理视图 (DMV) 监视正在进行的弹性数据库事务的状态和进度。 与事务相关的所有 DMV 都与 SQL 数据库和托管实例中的分布式事务相关。 可以在此处找到相应的 DMV 列表：[与事务相关的动态管理视图和函数 (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql)。

这些 DMV 特别有用：

* **sys.dm\_tran\_active\_transactions**：列出当前正在使用的事务及其状态。 UOW（工作单位）列可以标识属于同一分布式事务的不同子事务。 同一分布式事务中的所有事务具有相同的 UOW 值。 有关详细信息，请参阅 [DMV 文档](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)。
* **sys.dm\_tran\_database\_transactions**：提供有关事务的其他信息，例如事务在日志中的位置。 有关详细信息，请参阅 [DMV 文档](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)。
* **sys.dm\_tran\_locks**：提供当前进行中事务所持有的锁的相关信息。 有关详细信息，请参阅 [DMV 文档](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)。

## <a name="limitations"></a>限制

SQL 数据库中的弹性数据库事务当前存在以下限制：

* 仅支持 SQL 数据库中跨数据库的事务。 其他 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 资源提供程序和除 SQL 数据库以外的数据库无法参与弹性数据库事务。 这意味着，弹性数据库事务无法扩展到本地 SQL Server 和 Azure SQL 数据库。 对于本地的分布式事务，请继续使用 MSDTC。
* 仅支持来自 .NET 应用程序的客户端协调事务。 目前已规划 T-SQL 的服务器端支持，例如 BEGIN DISTRIBUTED TRANSACTION，但尚未推出。
* 不支持跨 WCF 服务的事务。 例如，有一个执行事务的 WCF 服务方法。 事务范围内的调用将失败，并显示异常 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)。

托管实例中的分布式事务当前存在以下限制：

* 仅支持托管实例中的跨数据库事务。 Azure SQL 托管实例外部的其他 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 资源提供程序和数据库无法参与分布式事务。 这意味着，分布式事务无法扩展到本地 SQL Server 和 Azure SQL 托管实例。 对于本地的分布式事务，请继续使用 MSDTC。
* 不支持跨 WCF 服务的事务。 例如，有一个执行事务的 WCF 服务方法。 事务范围内的调用将失败，并显示异常 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)。
* Azure SQL 托管实例必须是[服务器信任组](../managed-instance/server-trust-group-overview.md)的一部分才能参与分布式事务。
* [服务器信任组](../managed-instance/server-trust-group-overview.md)的限制影响分布式事务。
* 参与分布式事务的托管实例需要具有通过专用终结点进行的连接（使用部署了专用终结点的虚拟网络中的专用 IP 地址），并且需要使用专用 FQDN 来相互引用。 客户端应用程序可以在专用终结点上使用分布式事务。 此外，当 Transact-SQL 利用引用了专用终结点的链接服务器时，客户端应用程序也可以在公共终结点上使用分布式事务。 下图对此限制进行了说明。
  ![专用终结点连接限制][4]
## <a name="next-steps"></a>后续步骤

* 如有疑问，请在[有关 SQL 数据库的 Microsoft 问答页](/answers/topics/azure-sql-database.html)上与我们联系。
* 对于功能请求，请将它们添加到 [SQL 数据库反馈论坛](https://feedback.azure.com/forums/217321-sql-database/) 或 [托管实例论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)。



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
