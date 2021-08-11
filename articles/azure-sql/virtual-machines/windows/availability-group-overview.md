---
title: SQL Server Always On 可用性组概述
description: 本文介绍 Azure 虚拟机上的 SQL Server Always On 可用性组。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: d26a95a5db616be89eac89e140e20bd371088716
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580901"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上的 SQL Server 的 Always On 可用性组
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍 Azure 虚拟机 (VM) 上 SQL Server 的 Always On 可用性组 (AG)。 

## <a name="overview"></a>概述

Azure 虚拟机上的 Always On 可用性组类似于[本地 Always On 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)，并依赖于基础 [Windows Server 故障转移群集](hadr-windows-server-failover-cluster-overview.md)。 但是，由于虚拟机托管在 Azure 中，还存在其他一些注意事项，例如 VM 冗余以及 Azure 网络上的流量路由。 

下图演示了 Azure VM 上的 SQL Server 可用性组：

![可用性组](./media/availability-group-overview/00-EndstateSampleNoELB.png)

> [!NOTE]
> 现在，可以使用 Azure Migrate 将可用性组解决方案直接迁移到 Azure VM 上的 SQL Server。 有关详细信息，请参阅[迁移可用性组](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)。 

## <a name="vm-redundancy"></a>VM 冗余 

要增加冗余和提高可用性，SQL Server VM 应位于相同的[可用性集](../../../virtual-machines/availability-set-overview.md)或不同的[可用性区域](../../../availability-zones/az-overview.md)中。

通过将一组 VM 放在同一个可用性集，可保护免受由设备故障导致的数据中心内的中断影响（可用性集中的 VM 不会共享资源），或免受更新导致的影响（可用性集中的 VM 不会同时更新）。 

可用性区域可保护免受整个数据中心故障的影响，其中每个区域表示一个地区内的一组数据中心。  通过确保资源放在不同的可用性区域，任何数据中心级别的中断都没法导致所有 VM 脱机。

创建 Azure VM 时，必须选择配置可用性集还是配置可用性区域。  一个 Azure VM 不能同时加入这两者。 

尽管可用性区域可能会提供比可用性集更高的可用性（分别为 99.99% 和 99.95%），但还应考虑性能。 可用性集中的 VM 可以放置在[邻近放置组](../../../virtual-machines/co-location.md)中，这可以保证它们彼此接近，最大程度降低它们之间的网络延迟。 位于不同可用性区域中的 VM 之间具有更大的网络延迟，这可能会增加在主副本和次要副本之间同步数据所需的时间。 这可能会导致主副本延迟，并在发生计划外故障转移时增加数据丢失的几率。 务必在负载下测试建议的解决方案，并确保满足性能和可用性方面的 SLA。

## <a name="connectivity"></a>连接

可为可用性组配置虚拟网络名称或分布式网络名称。 [查看两者之间的差异](hadr-windows-server-failover-cluster-overview.md)；然后，为可用性组部署[分布式网络名称 (DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) 或[虚拟网络名称 (VNN)](availability-group-vnn-azure-load-balancer-configure.md)。 

使用 DNN 时，大多数 SQL Server 功能透明地适用于可用性组，但是某些功能可能需要特殊考虑。 有关详细信息，请参阅 [AG 和 DNN 互操作性](availability-group-dnn-interoperability.md)。 

此外，VNN 侦听器和 DNN 侦听器功能之间有一些行为差异，必须注意，具体包括： 

- **故障转移时间**：使用 DNN 侦听器时，故障转移时间更快，因为不需要等待网络负载均衡器来检测失败事件和更改其路由。 
- 现有连接：与故障转移可用性组中特定数据库之间的连接将会关闭，但与主要副本的其他连接将保持打开状态，因为在故障转移过程中，DNN 将保持联机状态。 这不同于传统的 VNN 环境。在传统 VNN 环境中，主要副本的所有连接通常在可用性组故障转移时关闭，侦听器进入脱机状态，而主要副本转换为次要角色。 使用 DNN 侦听器时，可能需要调整应用程序连接字符串，以确保在故障转移时将连接重定向到新的主要副本。
- 开放事务：针对故障转移可用性组中的数据库的开放事务将关闭并回滚，你需要手动重新连接。 例如，在 SQL Server Management Studio 中，关闭查询窗口并打开一个新窗口。 

在 Azure 中设置 VNN 侦听器时需要负载均衡器。 对于 Azure 中的负载平衡器，有两个主要选项：外部（公共）或内部。 外部（公共）负载均衡器是面向 Internet 的，并与可通过 Internet 访问的公共虚拟 IP 相关联。 内部负载均衡器仅支持同一虚拟网络内的客户端。 对于任一负载均衡器类型，都必须启用[直接服务器返回](../../../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip)。 

仍可通过直接连接到服务实例，单独连接到每个可用性副本。 此外，由于可用性组与数据库镜像客户端向后兼容，因此可以像数据库镜像伙伴一样连接到可用性副本，只要这些副本配置得类似于数据库镜像即可：

* 有一个主要副本和一个次要副本。
* 将辅助副本配置为不可读（“可读辅助副本”选项设置为“否”） 。

下面是一个示例客户端连接字符串，它对应于这个使用 ADO.NET 或 SQL Server Native Client 的类似于数据库镜像的配置：

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

有关客户端连接的详细信息，请参阅：

* [将连接字符串关键字用于 SQL Server 本机客户端](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [将客户端连接到数据库镜像会话 (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [在混合 IT 环境中连接到可用性组侦听器](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [可用性组侦听器、客户端连接和应用程序故障转移 (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [将数据库镜像连接字符串用于可用性组](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

## <a name="lease-mechanism"></a>租用机制 

对于 SQL Server，AG 资源 DLL 基于 AG 租用机制和 Always On 运行状况检测来确定 AG 的运行状况。 AG 资源 DLL 通过 *IsAlive* 操作公开资源运行状况。 资源监视器按群集检测信号间隔（由 **CrossSubnetDelay** 和 **SameSubnetDelay** 的群集范围值来设置）轮询 IsAlive。 在主节点上，只要资源 DLL 的 IsAlive 调用返回的 AG 运行不正常，群集服务就会启动故障转移。

AG 资源 DLL 监视内部 SQL Server 组件的状态。 Sp_server_diagnostics 按照某个间隔（由 **HealthCheckTimeout** 控制）向 SQL Server 报告这些组件的运行状况。

与其他故障转移机制不同，SQL Server 实例在租用机制中发挥着积极的作用。 租用机制用于充当群集资源主机与 SQL Server 进程之间的 *LooksAlive* 验证机制。 该机制用于确保双方（群集服务器和 SQL Server 服务）通信频繁，检查彼此的状态并最终避免出现裂脑情况。

在 Azure VM 中配置 AG 时，通常需要以不同于在本地环境中配置的方式来配置这些阈值。 若要根据 Azure VM 的最佳实践配置阈值设置，请参阅[群集最佳实践](hadr-cluster-best-practices.md)。 


## <a name="network-configuration"></a>网络配置  

在 Azure VM 故障转移群集上，建议为每个服务器（群集节点）设置一个 NIC，并且只使用一个子网。 Azure 网络具有物理冗余，这使得在 Azure VM 故障转移群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure VM 故障转移群集上可以安全地忽略此警告。 

## <a name="basic-availability-group"></a>基本可用性组

由于基本可用性组不允许使用多个次要副本，且没有对次要副本的读取访问权限，因此可以对基本可用性组使用数据库镜像连接字符串。 使用连接字符串便无需侦听器。 摆脱侦听器依赖项对于 Azure VM 上的可用性组很有用，因为这样便不需要负载均衡器，如果有多个侦听器用于附加数据库，则无需向负载均衡器中添加其他 IP。 

例如，若要使用 TCP/IP 显式连接到基本 AG（或者只有一个次要副本并且次要副本中不允许读取访问权的任何 AG）的 Replica_A 或 Replica_B 上的 AG 数据库 AdventureWorks，客户端应用程序可以提供以下数据库镜像连接字符串以成功连接到 AG

`Server=Replica_A; Failover_Partner=Replica_B; Database=AdventureWorks; Network=dbmssocn`


## <a name="deployment-options"></a>部署选项

有多个选项来将可用性组部署到 Azure VM 上的 SQL Server，某些选项的自动化程度比其他的高。 

下表提供了可用选项的比较：

| | [Azure 门户](availability-group-azure-portal-configure.md) | [Azure CLI / PowerShell](./availability-group-az-commandline-configure.md) | [快速启动模板](availability-group-quickstart-template-configure.md) | [手动](availability-group-manually-configure-prerequisites-tutorial.md) |
|---------|---------|---------|---------|---------|
|**SQL Server 版本** |2016+ |2016+|2016+|2012+|
|**SQL Server 版本** |Enterprise |Enterprise |Enterprise |Enterprise、Standard|
|**Windows Server 版本**| 2016+ | 2016+ | 2016+ | All|
|**为你创建群集**|是|是 | 是 |否|
|**为你创建可用性组** |是 |否|否|否|
|**分开创建侦听器和负载均衡器** |否|否|否|是|
|**能否使用此方法创建 DNN 侦听器？**|否|否|否|是|
|**WSFC 仲裁配置**|云见证|云见证|云见证|All|
|**具有多个区域的 DR** |否|否|否|是|
|**多子网支持** |是|是|是|是|
|**支持现有 AD**|是|是|是|是|
|**在同一区域中具有多个地区的 DR**|是|是|是|是|
|**不带 AD 的分布式 AG**|否|否|否|是|
|**不带群集的分布式 AG** |否|否|否|是|

## <a name="next-steps"></a>后续步骤

查看 [HADR 最佳做法](hadr-cluster-best-practices.md)，然后使用 [Azure 门户](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)、[快速入门模板](availability-group-quickstart-template-configure.md)或通过[手动](availability-group-manually-configure-prerequisites-tutorial.md)操作开始部署可用性组。

或者，可部署[无群集的可用性组](availability-group-clusterless-workgroup-configure.md)或者[多个区域](availability-group-manually-configure-multiple-regions.md)中的可用性组。

若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
