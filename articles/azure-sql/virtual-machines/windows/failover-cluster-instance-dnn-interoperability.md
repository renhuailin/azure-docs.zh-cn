---
title: 使用 SQL Server FCI 和 DNN 时的功能互操作性
description: '了解在 Azure VM 的 SQL Server 上将某些 SQL Server 功能与分布式网络名称 (DNN) 资源及故障转移群集实例一起使用时的额外注意事项。 '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358208"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>使用 SQL Server FCI 和 DNN 时的功能互操作性
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

某些 SQL Server 功能依赖于硬编码的虚拟网络名称 (VNN)。 因此，在 Azure VM 上将分布式网络名称 (DNN) 资源与故障转移群集实例和 SQL Server 一起使用时，还有一些额外的注意事项。 

在本文中，你将了解在使用 DNN 资源时如何配置网络别名，以及哪些 SQL Server 功能需要额外注意一些事项。

## <a name="create-network-alias-fci"></a>创建网络别名 (FCI)

某些服务器端组件依赖于硬编码的 VNN 值，并且需要使用将 VNN 映射到 DNN DNS 名称的网络别名才能正常运行。 请按照[创建服务器别名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client)中的步骤创建一个将 VNN 映射到 DNN DNS 名称的别名。 

对于默认实例，你可以直接将 VNN 映射到 DNN DNS 名称，使 VNN = DNN DNS 名称。
例如，如果 VNN 名称为 `FCI1`，实例名称为 `MSSQLSERVER`，并且 DNN 为 `FCI1DNN`（客户端以前连接到 `FCI`，现在连接到 `FCI1DNN`），则将 VNN `FCI1` 映射到 DNN `FCI1DNN`。 

对于命名实例，应当针对整个实例执行网络别名映射，使 `VNN\Instance` = `DNN\Instance`。 例如，如果 VNN 名称为 `FCI1`，实例名称为 `instA`，并且 DNN 为 `FCI1DNN`（客户端以前连接到 `FCI1\instA`，现在连接到 `FCI1DNN\instaA`），则将 VNN `FCI1\instaA` 映射到 DNN `FCI1DNN\instaA`。 



## <a name="client-drivers"></a>客户端驱动程序

对于 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驱动程序，用户需要在连接字符串中显式将 DNN DNS 名称指定为服务器名称。 若要确保故障转移时的快速连接，请将 `MultiSubnetFailover=True` 添加到连接字符串（如果 SQL 客户端支持它）。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、[sqlcmd](/sql/tools/sqlcmd-utility)、[Azure Data Studio](/sql/azure-data-studio/what-is) 和 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 的用户需要在连接字符串中显式将 DNN DNS 名称指定为服务器名称。 

## <a name="availability-groups-and-fci"></a>可用性组和 FCI

可以通过将故障转移群集实例用作副本之一来配置 Always On 可用性组。 在此配置中，FCI 副本的镜像终结点 URL 需要使用 FCI DNN。 同样，如果将 FCI 用作只读副本，则到 FCI 副本的只读路由需要使用 FCI DNN。 

镜像终结点的格式为：`ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'`。 

例如，如果你的 DNN DNS 名称为 `dnnlsnr`，并且 `5022` 是 FCI 的镜像终结点的端口，则用于创建终结点 URL 的 Transact-SQL (T-SQL) 代码片段如下所示： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同样，只读路由 URL 的格式为：`TCP://<DNN DNS name>:<SQL Server instance port>`。 

例如，如果你的 DNN DNS 名称为 `dnnlsnr`，并且 `1444` 是只读目标 SQL Server FCI 使用的端口，则用于创建只读路由 URL 的 T-SQL 代码片段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

如果端口是默认的 1433 端口，则可在 URL 中省略该端口。 对于命名实例，请为其配置静态端口，并在只读路由 URL 中指定该端口。  

## <a name="replication"></a>复制

复制有三个组件：发布服务器、分发服务器、订阅服务器。 这些组件中的任何一个都可以是故障转移群集实例。 由于 FCI VNN 在复制配置中显式或隐式地广泛使用，因此，可能需要使用将 VNN 映射到 DNN 的网络别名，复制才能正常进行。 

在复制中始终使用 VNN 名称作为 FCI 名称，但在下列远程情况下，请在配置复制之前创建网络别名：

| **复制组件（包含 DNN 的 FCI）** | **远程组件** | **网络别名映射** | **带有网络映射的服务器**| 
|---------|---------|---------|-------- | 
|发布者 | 分发服务器 | 发布服务器 VNN 到发布服务器 DNN| 分发服务器| 
|分发服务器|订阅者 |分发服务器 VNN 到分发服务器 DNN| 订阅者 | 
|分发服务器|发布者 | 分发服务器 VNN 到分发服务器 DNN | 发布者| 
|订阅者| 分发服务器| 订阅服务器 VNN 到订阅服务器 DNN | 分发服务器| 

例如，假设你在复制拓扑中有一个发布服务器配置为使用 DNN 的 FCI，并且分发服务器是远程的。 在这种情况下，请在分发服务器上创建一个网络别名，以将发布服务器 VNN 映射到发布服务器 DNN： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="使用 SQL Server 配置管理器将 DNN DNS 名称配置为网络别名。" :::

对于命名实例，请使用完整的实例名称，如以下示例图所示： 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="为命名实例配置网络别名时，使用完整的实例名称。" :::

## <a name="database-mirroring"></a>数据库镜像

配置数据库镜像时可以使用 FCI 作为数据库镜像伙伴。 请使用 [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) 而非 SQL Server Management Studio GUI 进行配置。 使用 T-SQL 可确保使用 DNN 而非 VNN 来创建数据库镜像终结点。 

例如，如果你的 DNN DNS 名称是 `dnnlsnr`，并且数据库镜像终结点是 7022，则以下 T-SQL 代码片段会配置数据库镜像伙伴： 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

对于客户端访问，“故障转移伙伴”属性可以处理数据库镜像故障转移，但不能处理 FCI 故障转移。 

## <a name="msdtc"></a>MSDTC

FCI 可参与由 Microsoft 分布式事务处理协调器 (MSDTC) 进行协调的分布式事务。 尽管群集化 MSDTC 和本地 MSDTC 都支持 FCI DNN，但在 Azure 中，群集化 MSDTC 仍然需要负载均衡器。 FCI 中定义的 DNN 没有代替 Azure 中群集化 MSDTC 对 Azure 负载均衡器的需求。 

## <a name="filestream"></a>FileStream

尽管 FCI 中的数据库支持 FileStream，但不支持将文件系统 API 与 DNN 配合使用来访问 FileStream 或 FileTable。 

## <a name="linked-servers"></a>链接服务器

支持将链接服务器与 FCI DNN 一起使用。 请直接使用 DNN 来配置链接服务器，或使用网络别名将 VNN 映射到 DNN。 


例如，若要为命名实例 `insta1` 创建 DNN DNS 名称为 `dnnlsnr` 的链接服务器，请使用以下 Transact-SQL (T-SQL) 命令：

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

也可改用虚拟网络名称 (VNN) 创建链接服务器，但随后需要定义网络别名来将 VNN 映射到 DNN。 

例如，对于实例名称 `insta1`、VNN 名称 `vnnname` 和 DNN 名称 `dnnlsnr`，请使用以下 Transact-SQL (T-SQL) 命令创建使用 VNN 的链接服务器：

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

然后，创建一个网络别名来将 `vnnname\insta1` 映射到 `dnnlsnr\insta1`。 



## <a name="frequently-asked-questions"></a>常见问题


- 哪个 SQL Server 版本引入了 DNN 支持？ 

   SQL Server 2019 CU2 及更高版本。

- 使用 DNN 时的预期故障转移时间是多少？

   对于 DNN，故障转移时间恰好是 FCI 故障转移时间，未增加任何时间（例如使用 Azure 负载均衡器时的探测时间）。

- 若要支持将 DNN 与 OLEDB 和 ODBC 一起使用，对 SQL 客户端是否有任何版本要求？

   建议使用针对 DNN 的 `MultiSubnetFailover=True` 连接字符串支持。 它从 SQL Server 2012 (11.x) 开始提供。

- 使用 DNN 需要更改 SQL Server 配置吗？ 

   SQL Server 不要求更改配置才能使用 DNN，但某些 SQL Server 功能可能需要额外注意一些事项。 

- DNN 是否支持多子网群集？

   是。 群集会将 DNS 中的 DNN 绑定到群集中所有节点的物理 IP 地址，不考虑子网。 SQL 客户端会尝试 DNS 名称的所有 IP 地址，不考虑子网。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

