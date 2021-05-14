---
title: 群集配置最佳做法
description: 在 Azure 虚拟机上为 SQL Server 配置高可用性和灾难恢复 (HADR) 时，了解支持的群集配置，例如支持的仲裁或连接路由选项。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201764"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>群集配置最佳做法（Azure VM 上的 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

群集用于 Azure 虚拟机 (VM) 上 SQL Server 的高可用性和灾难恢复 (HADR)。 

本文为与 Azure 虚拟机 (VM) 上的 SQL Server 一起使用的[故障转移群集实例 (FCI) ](failover-cluster-instance-overview.md)和[可用性组](availability-group-overview.md)提供群集配置最佳做法。 


## <a name="networking"></a>网络

每个服务器（群集节点）使用一个 NIC，并使用一个子网。 Azure 网络具有物理冗余，因此在 Azure 虚拟机来宾群集上不需要额外的 NIC 和子网。 群集验证报告将发出警告，指出节点只能在单个网络上访问。 在 Azure 虚拟机来宾故障转移群集上，可以忽略此警告。

### <a name="tuning-failover-cluster-network-thresholds"></a>调整故障转移群集网络阈值

使用 SQL Server AlwaysOn 在 Azure VM 中运行 Windows 故障转移群集节点时，建议将群集设置更改为更宽松的监视状态。  这将使群集更稳定、更可靠。  请参阅[使用 SQL AlwaysOn 的 IaaS - 调整故障转移群集网络阈值](/windows-server/troubleshoot/iaas-sql-failover-cluster)了解有关详细信息。

## <a name="quorum"></a>Quorum

尽管双节点群集即使没有[仲裁资源](/windows-server/storage/storage-spaces/understand-quorum)也能正常工作，但我们严格要求客户使用仲裁资源来获得生产支持。 没有仲裁资源，群集验证将不会通过任何群集。 

从技术上讲，在没有仲裁资源的情况下，三节点群集可以承受单个节点丢失（减少到两个节点）。 但是，在群集减少到两个节点后，如果节点丢失或通信失败，群集资源就有可能脱机，以防出现分区的情况。

配置仲裁资源，将使群集在只有一个节点联机时可以继续联机。

下表按建议用于 Azure VM 的顺序列出了可用的仲裁选项，其中磁盘见证是首选选项： 


||[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[云见证](/windows-server/failover-clustering/deploy-cloud-witness)  |[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支持的 OS**| 全部 |Windows Server 2016 及更高版本| 全部|


### <a name="disk-witness"></a>磁盘见证

磁盘见证是群集可用存储组中的小型群集磁盘。 此磁盘具有高可用性，并且可在节点之间故障转移。 它包含群集数据库的副本，其默认大小通常小于 1 GB。 对于使用 Azure 共享磁盘（或任何共享磁盘解决方案，如共享 SCSI、iSCSI 或光纤信道 SAN）的任何群集，磁盘见证都是首选的仲裁选项。  群集共享卷不能用作磁盘见证。

将 Azure 共享磁盘配置为磁盘见证。 

若要开始，请参阅[配置磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支持的操作系统**：All   


### <a name="cloud-witness"></a>云见证

云见证是一种故障转移群集仲裁见证，它使用 Microsoft Azure 提供对群集仲裁的投票。 默认大小约为 1 MB，仅包含时间戳。 云见证是在多个站点、多个区域和多个地区中进行部署的理想选择。

若要开始，请参阅[配置云见证](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)。


**支持的操作系统**：Windows Server 2016 及更高版本   


### <a name="file-share-witness"></a>文件共享见证

文件共享见证是一个 SMB 文件共享，通常在运行 Windows Server 的文件服务器上配置。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。 在 Azure 中，你可以在单独的虚拟机上配置文件共享。

若要开始，请参阅[配置文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支持的操作系统**：Windows Server 2012 和更高版本   

## <a name="connectivity"></a>连接

在传统的本地网络环境中，SQL Server 故障转移群集实例似乎是在单台计算机上运行的 SQL Server 的单个实例。 由于故障转移群集实例从一个节点到另一个节点进行故障转移，该实例的虚拟网络名称 (VNN) 会提供一个统一的连接点，并允许应用程序连接到 SQL Server 实例，而无需知道哪个节点当前处于活动状态。 当发生故障转移时，虚拟网络名称会在新的活动节点启动后注册到该节点。 此过程对于连接到 SQL Server 的客户端或应用程序是透明的，可以最大限度地缩短出现故障时客户端或应用程序的停机时间。 同样，可用性组侦听程序会使用 VNN 将流量路由到适当的副本。 

使用带有 Azure 负载均衡器的 VNN 或分布式网络名称 (DNN)，将流量路由到 Azure VM 上具有 SQL Server 的故障转移群集实例的 VNN，或替换可用性组中的现有 VNN 侦听程序。 


下表比较了 HADR 连接的可支持性： 

| |**虚拟网络名称 (VNN)**  |**分布式网络名称 (DNN)**  |
|---------|---------|---------|
|**最低操作系统版本**| All | Windows Server 2016 |
|**最低 SQL Server 版本** |All |SQL Server 2019 CU2（用于 FCI）<br/> SQL Server 2019 CU8（用于 AG）|
|**支持的 HADR 解决方案** | 故障转移群集实例 <br/> 可用性组 | 故障转移群集实例 <br/> 可用性组|


### <a name="virtual-network-name-vnn"></a>虚拟网络名称 (VNN)

由于虚拟 IP 访问点在 Azure 中的工作方式不同，因此需要配置 [Azure 负载均衡器](../../../load-balancer/index.yml)，以将流量路由到 FCI 节点或可用性组侦听程序的 IP 地址。 在 Azure 虚拟机中，负载均衡器将保留群集 SQL Server 资源所依赖的 VNN 的 IP 地址。 负载均衡器分会配到达前端的入站流，然后将该流量路由到后端池定义的实例。 可以使用负载均衡规则和运行状况探测来配置流量。 通过 SQL Server FCI，后端池实例是运行 SQL Server 的 Azure 虚拟机。 

使用负载均衡器时存在轻微的故障转移延迟，因为默认情况下，运行状况探测每 10 秒执行一次活动检查。 

首先，了解如何为[故障转移群集实例](failover-cluster-instance-vnn-azure-load-balancer-configure.md)或[可用性组](availability-group-vnn-azure-load-balancer-configure.md)配置 Azure 负载均衡器

**支持的操作系统**：All   
**支持的 SQL 版本**：All   
**支持的 HADR 解决方案**：故障转移群集实例与可用性组   


### <a name="distributed-network-name-dnn"></a>分布式网络名称 (DNN)

分布式网络名称是面向 SQL Server 2019 的 Azure 新特性。 DNN 为 SQL Server 客户端提供了一种替代方法，使其无需使用负载均衡器即可连接到 SQL Serve r故障转移群集实例或可用性组。 

创建 DNN 资源后，群集将 DNS 名称与群集中所有节点的 IP 地址绑定。 SQL 客户端将尝试连接到此列表中的每个 IP 地址，以查找要连接到的资源。  可以通过在连接字符串中指定 `MultiSubnetFailover=True` 来加快此过程。 此设置会让提供程序并行尝试所有 IP 地址，以便客户端可以立即连接到 FCI 或侦听程序。 

建议尽可能通过负载均衡器使用分布式网络名称，原因是： 
- 端到端解决方案更加可靠，因为不再需要维护负载均衡器资源。 
- 消除负载均衡器探测可最大程度地缩短故障转移时间。 
- DNN 使用 Azure VM 上的 SQL Server 简化故障转移群集实例或可用性组侦听程序的配置和管理。 

使用 DNN 时，大多数 SQL Server 功能透明地适用于 FCI 和可用性组，但是某些功能可能需要特殊考虑。 请参阅 [FCI 和 DNN 互操作性](failover-cluster-instance-dnn-interoperability.md) 以及 [AG 和 DNN 互操作性](availability-group-dnn-interoperability.md)了解详细信息。 

首先，了解如何为[故障转移群集实例](failover-cluster-instance-distributed-network-name-dnn-configure.md)或[可用性组](availability-group-distributed-network-name-dnn-listener-configure.md)配置分布式网络名称资源

**支持的操作系统**：Windows Server 2016 及更高版本   
**支持的 SQL 版本**：SQL Server 2019 CU2 (FCI) 和 SQL Server 2019 CU8 (AG)   
**支持的 HADR 解决方案**：故障转移群集实例与可用性组   


## <a name="limitations"></a>限制

在 Azure 虚拟机上使用 FCI 或可用性组以及 SQL Server 时，请注意以下限制。 

### <a name="msdtc"></a>MSDTC 

Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC)，其中存储位于群集共享卷 (CSV) 和[标准负载均衡器](../../../load-balancer/load-balancer-overview.md)上，或者位于正在使用 Azure 共享磁盘的 SQL Server VM 上。 

在 Azure 虚拟机上，具有群集共享卷的 Windows Server 2016 或更早版本不支持 MSDTC，因为：

- 无法将群集 MSDTC 资源配置为使用共享存储。 在 Windows Server 2016 上，如果创建 MSDTC 资源，即使存储可用，它也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。


## <a name="next-steps"></a>后续步骤

在确定适合解决方案的最佳做法之后，请开始进行以下操作：[为 FCI 准备 SQL Server VM](failover-cluster-instance-prepare-vm.md)，或者使用 [Azure 门户](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md) 或 [Azure 快速启动模板](availability-group-quickstart-template-configure.md)创建可用性组。
