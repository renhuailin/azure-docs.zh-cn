---
title: 与可用性组和 DNN 侦听器的功能互操作性
description: '了解在 Azure VM 的 SQL Server 上将某些 SQL Server 功能与分布式网络名称 (DNN) 侦听器及 Always On 可用性组一起使用时的额外注意事项。 '
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359398"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>与 AG 和 DNN 侦听器的功能互操作性 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

某些 SQL Server 功能依赖于硬编码的虚拟网络名称 (VNN)。 因此，在 Azure VM 上将分布式网络名称 (DNN) 侦听器与 Always On 可用性组和 SQL Server 一起使用时，可能还有一些额外的注意事项。 

本文详细介绍了 SQL Server 功能以及与可用性组 DNN 侦听器的互操作性。 


## <a name="client-drivers"></a>客户端驱动程序

对于 ODBC、OLEDB、ADO.NET、JDBC、PHP 和 Node.js 驱动程序，用户需要在连接字符串中显式将 DNN 侦听器名称和端口指定为服务器名称。 若要确保故障转移时的快速连接，请将 `MultiSubnetFailover=True` 添加到连接字符串（如果 SQL 客户端支持它）。 

## <a name="tools"></a>工具

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)、[sqlcmd](/sql/tools/sqlcmd-utility)、[Azure Data Studio](/sql/azure-data-studio/what-is) 和 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 的用户需要在连接字符串中显式将 DNN 侦听器名称和端口指定为服务器名称才能连接到侦听器。 

当前不支持通过 SQL Server Management Studio (SSMS) GUI 创建 DNN 侦听器。 


## <a name="availability-groups-and-fci"></a>可用性组和 FCI

可以通过将故障转移群集实例 (FCI) 用作副本之一来配置 Always On 可用性组。 若要将此配置与 DNN 侦听器一起使用，[故障转移群集实例还必须使用 DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md)，因为无法将 FCI 虚拟 IP 地址放入 AG DNN IP 列表中。 

在此配置中，FCI 副本的镜像终结点 URL 需要使用 FCI DNN。 同样，如果将 FCI 用作只读副本，则到 FCI 副本的只读路由需要使用 FCI DNN。 

镜像终结点的格式为：`ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'`。 

例如，如果你的 FCI DNN DNS 名称为 `dnnlsnr`，并且 `5022` 是 FCI 的镜像终结点的端口，则用于创建终结点 URL 的 Transact-SQL (T-SQL) 代码片段如下所示： 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

类似地，只读路由 URL 的格式为：`TCP://<FCI DNN DNS name>:<SQL Server instance port>`。 

例如，如果你的 DNN DNS 名称为 `dnnlsnr`，并且 `1444` 是只读目标 SQL Server FCI 使用的端口，则用于创建只读路由 URL 的 T-SQL 代码片段如下所示： 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

如果端口是默认的 1433 端口，则可在 URL 中省略该端口。 对于命名实例，请为其配置静态端口，并在只读路由 URL 中指定该端口。  

## <a name="distributed-availability-group"></a>分布式可用性组

DNN 侦听器目前不支持分布式可用性组。 

## <a name="replication"></a>复制

事务性复制、合并复制和快照复制都支持在连接到侦听器的复制对象中将 VNN 侦听器替换为 DNN 侦听器和端口。 

有关如何对可用性组使用复制的详细信息，请参阅[发布服务器和 AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)、[订阅服务器和 AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)以及[分发服务器和 AG](/sql/relational-databases/replication/configure-distribution-availability-group)。

## <a name="msdtc"></a>MSDTC

支持本地和群集化 MSDTC，但 MSDTC 使用动态端口，这需要使用标准 Azure 负载均衡器来配置 HA 端口。 因此，VM 必须使用标准 IP 预留，否则它无法向 Internet 公开。 

定义两个规则，一个用于 RPC 端点映射程序端口 135，一个用于真实的 MSDTC 端口。 故障转移后，当 MSDTC 端口在新节点上进行了更改，可修改 LB 规则，改用新端口。 

如果 MSDTC 是本地的，请务必允许出站通信。 

## <a name="distributed-query"></a>分布式查询 

分布式查询依赖于链接服务器，可以使用 AG DNN 侦听器和端口配置该服务器。 如果端口不是 1433，则在配置链接服务器时，请在 SQL Server Management Studio (SSMS) 中选择“使用其他数据源”选项。 

## <a name="filestream"></a>FileStream

文件流受支持，但不适用于用户通过 Windows 文件 API 访问具有作用域的文件共享的场景。 

## <a name="filetable"></a>FileTable

FileTable 受支持，但不适用于用户通过 Windows 文件 API 访问具有作用域的文件共享的场景。 

## <a name="linked-servers"></a>链接服务器

使用 AG DNN 侦听器名称和端口配置链接服务器。 如果端口不是 1433，则在配置链接服务器时，请在 SQL Server Management Studio (SSMS) 中选择“使用其他数据源”选项。 


## <a name="frequently-asked-questions"></a>常见问题


- 哪个 SQL Server 版本引入了 AG DNN 侦听器支持？ 

   SQL Server 2019 CU8 及更高版本。

- 使用 DNN 侦听器时的预期故障转移时间是多少？

   对于 DNN 侦听器，故障转移时间将恰好是 AG 故障转移时间，没有额外的时间（例如使用 Azure 负载均衡器时的探测时间）。

- 若要支持将 DNN 与 OLEDB 和 ODBC 一起使用，对 SQL 客户端是否有任何版本要求？

   建议使用针对 DNN 侦听器的 `MultiSubnetFailover=True` 连接字符串支持。 它从 SQL Server 2012 (11.x) 开始提供。

- 使用 DNN 侦听器需要更改 SQL Server 配置吗？ 

   SQL Server 不要求更改配置才能使用 DNN，但某些 SQL Server 功能可能需要额外注意一些事项。 

- DNN 是否支持多子网群集？

   是。 群集会将 DNS 中的 DNN 绑定到可用性组中所有副本的物理 IP 地址，不考虑子网。 SQL 客户端会尝试 DNS 名称的所有 IP 地址，不考虑子网。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always On 可用性组](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

