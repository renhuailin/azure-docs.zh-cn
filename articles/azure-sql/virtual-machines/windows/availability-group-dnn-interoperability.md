---
title: 可用性组和 DNN 侦听器的功能互操作性
description: '了解有关在 Azure Vm 上使用 SQL Server 上的 Always On 可用性组的某些 SQL Server 功能和分布式网络名称 (DNN) 侦听器的其他注意事项。 '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359398"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>功能与 AG 和 DNN 侦听器的互操作性 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

某些 SQL Server 功能依赖于硬编码的虚拟网络名称 (VNN) 。 因此，使用分布式网络名称 (DNN) 侦听器与 Always On 可用性组和在 Azure Vm 上 SQL Server，可能还有一些其他注意事项。 

本文详细介绍可用性组 DNN 侦听器 SQL Server 功能和互操作性。 


## <a name="client-drivers"></a>客户端驱动程序

对于 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驱动程序，用户需要在连接字符串中显式指定 DNN 侦听器名称和端口作为服务器名称。 若要确保在故障转移时快速连接，请在 `MultiSubnetFailover=True` SQL 客户端支持连接字符串时将其添加到连接字符串中。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、 [sqlcmd](/sql/tools/sqlcmd-utility)、 [Azure Data Studio](/sql/azure-data-studio/what-is)和[SQL Server Data Tools](/sql/ssdt/sql-server-data-tools)的用户需要在连接字符串中显式指定 DNN 侦听器名称和端口作为服务器名称，以连接到侦听器。 

当前不支持通过 SQL Server Management Studio (SSMS) GUI 创建 DNN 侦听器。 


## <a name="availability-groups-and-fci"></a>可用性组和 FCI

你可以使用故障转移群集实例来配置 Always On 可用性组， (FCI) 为副本之一。 要使此配置与 DNN 侦听器一起使用， [故障转移群集实例还必须使用 DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) ，因为无法将 FCI 虚拟 ip 地址放入 AG DNN IP 列表。 

在此配置中，FCI 副本的镜像终结点 URL 需要使用 FCI DNN。 同样，如果将 FCI 用作只读副本，则 FCI 副本的只读路由需要使用 FCI DNN。 

镜像终结点的格式为： `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` 。 

例如，如果 FCI DNN DNS 名称是 `dnnlsnr` ，并且 `5022` 是 FCI 镜像终结点的端口，则用于创建终结点 URL 的 Transact-sql (t-sql) 代码段如下所示： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

同样，只读路由 URL 的格式为： `TCP://<FCI DNN DNS name>:<SQL Server instance port>` 。 

例如，如果 DNN DNS 名称是 `dnnlsnr` ，并且 `1444` 是只读目标 SQL Server FCI 使用的端口，则用于创建只读路由 URL 的 t-sql 代码段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

如果 URL 是默认的1433端口，则可以省略该端口。 对于命名实例，为命名实例配置静态端口，并在只读路由 URL 中指定该端口。  

## <a name="distributed-availability-group"></a>分布式可用性组

DNN 侦听器目前不支持分布式可用性组。 

## <a name="replication"></a>复制

事务性复制、合并复制和快照复制都支持将 VNN 侦听器替换为连接到侦听器的复制对象中的 DNN 侦听器和端口。 

有关如何对可用性组使用复制的详细信息，请参阅 [发行者和 ag](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)、 [订阅者和 Ag](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)以及 [分发服务器和 ag](/sql/relational-databases/replication/configure-distribution-availability-group)。

## <a name="msdtc"></a>MSDTC

支持本地和群集 MSDTC，但 MSDTC 使用动态端口，这需要标准 Azure 负载均衡器来配置 HA 端口。 因此，VM 必须使用标准 IP 保留，否则无法向 internet 公开。 

定义两个规则，一个用于 RPC 终结点映射器端口135，一个用于真实 MSDTC 端口。 故障转移后，将 LB 规则修改为新的 MSDTC 端口，并在新节点上更改它。 

如果 MSDTC 是本地的，请务必允许出站通信。 

## <a name="distributed-query"></a>分布式查询 

分布式查询依赖于链接服务器，该服务器可以使用 AG DNN 侦听器和端口进行配置。 如果端口不是1433，请在配置链接服务器时 SQL Server Management Studio (SSMS) 中选择 " **使用其他数据源** " 选项。 

## <a name="filestream"></a>FileStream

Filestream 是支持的，但不适用于用户通过使用 Windows 文件 API 访问范围文件共享的情况。 

## <a name="filetable"></a>FileTable

如果用户通过使用 Windows 文件 API 访问作用域文件共享，则支持 Filetable，但不支持 Filetable。 

## <a name="linked-servers"></a>链接服务器

使用 AG DNN 侦听器名称和端口配置链接服务器。 如果端口不是1433，请在配置链接服务器时 SQL Server Management Studio (SSMS) 中选择 " **使用其他数据源** " 选项。 


## <a name="frequently-asked-questions"></a>常见问题


- 哪些 SQL Server 版本引入了 AG DNN 侦听器支持？ 

   SQL Server 2019 CU8 及更高版本。

- 使用 DNN 侦听器时的预期故障转移时间是多少？

   对于 DNN 侦听器，故障转移时间只是 AG 故障转移时间，在使用 Azure 负载均衡器) 时，不会有任何额外时间 (如探测时间）。

- SQL 客户端是否有任何版本要求支持 OLEDB 和 ODBC DNN？

   建议 `MultiSubnetFailover=True` 为 DNN 侦听器提供连接字符串支持。 从 SQL Server 2012 (11. x) 开始提供此功能。

- 使用 DNN 侦听器需要 SQL Server 配置更改吗？ 

   SQL Server 不需要更改任何配置即可使用 DNN，但某些 SQL Server 功能可能需要更多的注意事项。 

- DNN 是否支持多子网群集？

   是。 群集会将 DNS 中的 DNN 绑定到可用性中所有副本的物理 IP 地址，而不考虑子网。 SQL 客户端尝试 DNS 名称的所有 IP 地址，而不考虑子网。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always on 可用性组](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

