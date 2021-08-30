---
title: HADR 配置最佳做法
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
ms.date: 06/01/2021
ms.author: mathoma
ms.openlocfilehash: b9aa10e9a11ee1268c8bb49d5cb32d0550c2ca3a
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112582071"
---
# <a name="hadr-configuration-best-practices-sql-server-on-azure-vms"></a>HADR 配置最佳做法（Azure VM 上的 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Windows Server 故障转移群集](hadr-windows-server-failover-cluster-overview.md)与 Azure 虚拟机 (VM) 上的 SQL Server 配合使用，可实现 高可用性和灾难恢复 (HADR)。 

本文为与 Azure 虚拟机 (VM) 上的 SQL Server 一起使用的[故障转移群集实例 (FCI) ](failover-cluster-instance-overview.md)和[可用性组](availability-group-overview.md)提供群集配置最佳做法。 

有关详细信息，请参阅本系列教程中的其他文章：[清单](performance-guidelines-best-practices-checklist.md)、[VM 大小](performance-guidelines-best-practices-vm-size.md)、[存储](performance-guidelines-best-practices-storage.md)、[安全性](security-considerations-best-practices.md)、[HADR 配置](hadr-cluster-best-practices.md)和[收集基线](performance-guidelines-best-practices-collect-baseline.md)。 

## <a name="checklist"></a>清单

请查看以下清单，以大致了解本文其余部分详细介绍的 HADR 最佳做法。 

对于 Windows 群集，请考虑以下最佳做法： 

* 将群集更改为主动性较低的参数，以避免暂时性网络故障或 Azure 平台维护带来的意外中断。 要了解详细信息，请参阅[检测信号和阈值设置](#heartbeat-and-threshold)。 对于 Windows Server 2012 或更高版本，请使用以下建议值： 
   - **SameSubnetDelay**：1 秒
   - **SameSubnetThreshold**：40 个检测信号
   - **CrossSubnetDelay**：1 秒
   - **CrossSubnetThreshold**：40 个检测信号
* 将 VM 放置在可用性集或不同的可用性区域中。  要了解详细信息，请参阅 [VM 可用性设置](#vm-availability-settings)。 
* 每个群集节点使用一个 NIC，并使用一个子网。 
* 将群集[仲裁投票](#quorum-voting)配置为使用 3 个或更多奇数投票数。 不要将投票分配给 DR 区域。 
* 仔细监视[资源限制](#resource-limits)，避免因资源限制出现意外重启或故障转移。
   - 确保 OS、驱动程序和 SQL Server 都是最新版本。 
   - 针对 Azure VM 上的 SQL Server 优化性能。 查看本文中的其他部分了解详细信息。 
   - 减少或分散工作负荷，避免资源限制。 
   - 转到限制更高的 VM 或磁盘，避免受限。 

对于 SQL Server 可用性组或故障转移群集实例，请考虑以下最佳做法： 

* 如果经常出现意外失败，请遵循本文其余部分中概述的最佳性能做法。 
* 如果优化 SQL Server VM 性能无法解决意外的故障转移，请考虑放宽对可用性组或故障转移群集实例的[监视](#relaxed-monitoring)。 但这样做可能无法解决根本问题，同时可能会降低失败可能性而掩盖症状。 你可能仍需要调查并解决根本原因。 对于 Windows Server 2012 或更高版本，请使用以下建议值： 
   - **租用超时**：使用此公式计算最大租用超时值：   
   `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
   首先从 40 秒开始。 如果使用的是之前建议的宽松 `SameSubnetThreshold` 和 `SameSubnetDelay` 值，则租用超时值不超过 80 秒。   
   - **指定时间段内的最大失败数**：将此值设置为 6。 
* 使用虚拟网络名称 (VNN) 连接 HADR 解决方案时，即使群集只跨越一个子网，也请在连接字符串中指定 `MultiSubnetFailover = true`。 
   - 如果客户端不支持 `MultiSubnetFailover = True`，你可能需要设置 `RegisterAllProvidersIP = 0` 和 `HostRecordTTL = 300` 来缓存较短持续时间内的客户端凭据。 但这样做可能会导致对 DNS 服务器进行其他查询。 
- 要使用分布式网络名称 (DNN) 连接 HADR 解决方案，请考虑以下事项：
   - 必须使用支持 `MultiSubnetFailover = True` 的客户端驱动程序，而且此参数必须位于连接字符串中。 
   - 连接可用性组的 DNN 侦听器时，请在连接字符串中使用唯一的 DNN 端口。 
- 对基本可用性组使用数据库镜像连接字符串，免去负载均衡器或 DNN 需求。 
- 在部署高可用性解决方案之前验证 VHD 的扇区大小，避免出现未对齐的 I/O。 有关详细信息，请参阅 [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c)。 


## <a name="vm-availability-settings"></a>VM 可用性设置

为了减轻停机造成的影响，请考虑以下 VM 最佳可用性设置： 

* 将邻近放置组与加速网络一起使用以实现最低的延迟。
* 将虚拟机群集节点放置在不同的可用性区域中，以防止数据中心级故障，或放置在单个可用性集中，以实现同一数据中心内的低延迟冗余。
* 为可用性集中的 VM 使用高级托管 OS 和数据磁盘。
* 将每个应用程序层配置到不同的可用性集中。

## <a name="quorum"></a>Quorum

尽管双节点群集即使没有[仲裁资源](/windows-server/storage/storage-spaces/understand-quorum)也能正常工作，但我们严格要求客户使用仲裁资源来获得生产支持。 没有仲裁资源，群集验证将不会通过任何群集。 

从技术上讲，在没有仲裁资源的情况下，三节点群集可以承受单个节点丢失（减少到两个节点）。 但是，在群集减少到两个节点后，如果节点丢失或通信失败，群集资源就有可能脱机，以防出现脑裂情况。 配置仲裁资源，将使群集在只有一个节点联机时可以继续联机。

磁盘见证是最具弹性的仲裁选项，但若要在 Azure VM 中的 SQL Server 上使用磁盘见证，必须使用 Azure 共享磁盘，这会对高可用性解决方案施加一些限制。 因此，在使用 Azure 共享磁盘配置故障转移群集实例时请使用磁盘见证，否则尽量使用云见证。 

下表列出了 Azure VM 上的 SQL Server 可用的仲裁选项： 

|  |[云见证](/windows-server/failover-clustering/deploy-cloud-witness) |[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支持的 OS**| Windows Server 2016 及更高版本 |全部 | All|

- 对于多个站点、多个区域和多个地区中的部署，云见证是理想的选择。 除非使用的是共享存储群集解决方案，否则请尽量使用云见证。
- 磁盘见证是最具弹性的仲裁选项，并且对于使用 Azure 共享磁盘（或任何共享磁盘解决方案，例如共享 SCSI、iSCSI 或光纤通道 SAN）的任何群集，它都是首选的仲裁选项。  群集共享卷不能用作磁盘见证。 
- 当磁盘见证和云见证选项不可用时，适合使用文件共享见证。 

若要开始，请参阅[配置群集仲裁](hadr-cluster-quorum-configure-how-to.md)。 

## <a name="quorum-voting"></a>仲裁投票

可以更改参与 Windows Server 故障转移群集的节点的仲裁投票。 

修改节点投票设置时，请遵循以下指导原则： 

| 仲裁投票指导原则 |
|-|
| 默认从没有投票的每个节点开始。 每个节点应该只有一个附带明确理由的投票。|
| 为托管可用性组主副本的群集节点或者为故障转移群集实例的首选所有者启用投票。 |
| 为自动故障转移所有者启用投票。 在自动故障转移后可以托管主副本或 FCI 的每个节点应具有一个投票。 | 
| 如果某个可用性组具有多个次要副本，则仅为具有自动故障转移的副本启用投票。 | 
| 为辅助灾难恢复站点中的节点禁用投票。 如果主站点没有任何问题，则辅助站点中的节点不应该影响群集脱机决策。 | 
| 具有奇数个投票，至少有三个仲裁投票。 根据需要，在双节点群集中添加一个[仲裁见证](hadr-cluster-quorum-configure-how-to.md)，以额外增加一个投票。 | 
| 故障转移后重新评估投票分配。 你不希望故障转移到不支持运行状况仲裁的群集配置。 |


## <a name="connectivity"></a>连接


可以配置虚拟网络名称 (VNN)；从 SQL Server 2019 开始，可为故障转移群集实例和可用性组侦听器配置分布式网络名称 (DNN)。 

分布式网络名称是建议的连接选项（如果可用）： 
- 端到端解决方案更加可靠，因为不再需要维护负载均衡器资源。 
- 消除负载均衡器探测可最大程度地缩短故障转移时间。 
- DNN 使用 Azure VM 上的 SQL Server 简化故障转移群集实例或可用性组侦听程序的配置和管理。 

如果使用 DNN，或者使用跨多个子网的 AG 或 FCI，则必须使用支持 MultiSubnetFailover 参数的客户端驱动程序，并在连接字符串中指定 MultiSubnetFailover=True。 对于可用性组，该连接字符串应包含 DNN 端口号（对于 FCI，则不需要包含此端口号）。 

有关详细信息，请参阅 [Windows Server 故障转移群集概述](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)。 

若要配置连接，请参阅以下文章：
- 可用性组：[配置 DNN](availability-group-distributed-network-name-dnn-listener-configure.md)、[配置 VNN](availability-group-vnn-azure-load-balancer-configure.md)
- 故障转移群集实例：[配置 DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md)、[配置 VNN](failover-cluster-instance-vnn-azure-load-balancer-configure.md)。 

使用 DNN 时，大多数 SQL Server 功能透明地适用于 FCI 和可用性组，但是某些功能可能需要特殊考虑。 请参阅 [FCI 和 DNN 互操作性](failover-cluster-instance-dnn-interoperability.md) 以及 [AG 和 DNN 互操作性](availability-group-dnn-interoperability.md)了解详细信息。 

>[!TIP]
> 即使对于跨单个子网的 HADR 解决方案，也可以在连接字符串中将 MultiSubnetFailover 参数设置为 true，以支持将来跨越子网而无需更新连接字符串。  

## <a name="heartbeat-and-threshold"></a>检测信号和阈值 

将群集检测信号和阈值设置更改为宽松设置。 默认检测信号和阈值群集设置适用于高度优化的本地网络，不考虑云环境中延迟增大的可能性。 检测信号网络是通过 UDP 3343 维护的，在传统上该协议的可靠性远低于 TCP，且更容易造成对话不完整。
 
因此，在为 Azure VM 上的 SQL Server 高可用性解决方案运行群集节点时，请将群集设置更改为更宽松的监视状态，以避免由于更有可能发生网络延迟或故障、Azure 维护或资源瓶颈而导致的暂时性故障。 

延迟和阈值设置对总体运行状况检测结果产生累积影响。 例如，在执行恢复之前将 CrossSubnetDelay 设置为每隔 2 秒发送检测信号并将 CrossSubnetThreshold 设置为 10 个丢失的检测信号意味着，在执行恢复操作之前，群集总共可以容许 20 秒网络延迟 。 一般而言，最好是继续发送频繁的检测信号，但使用更高的阈值。 

为了确保在合法的服务中断期间能够恢复，同时为暂时性问题提供更高的容许度，请将延迟和阈值设置放宽为下表中详述的建议值： 

| 设置 | Windows Server 2012 或更高版本 | Windows Server 2008R2 |
|:---------------------|:----------------------------|:-----------------------|
| SameSubnetDelay      | 1 秒                    | 2 秒               |
| SameSubnetThreshold  | 40 个检测信号               | 10 个检测信号（最大值）         |
| CrossSubnetDelay     | 1 秒                    | 2 秒               |  
| CrossSubnetThreshold | 40 个检测信号               | 20 个检测信号（最大值）         |


使用 PowerShell 更改群集参数： 

# <a name="windows-server-2012-2019"></a>[Windows Server 2012-2019](#tab/windows2012)


```powershell
(get-cluster).SameSubnetThreshold = 40
(get-cluster).CrossSubnetThreshold = 40
```

# <a name="windows-server-2008r2"></a>[Windows Server 2008/R2](#tab/windows2008)


```powershell
(get-cluster).SameSubnetThreshold = 10
(get-cluster).CrossSubnetThreshold = 20 
(get-cluster).SameSubnetDelay = 2000
(get-cluster).CrossSubnetDelay = 2000
```

---

使用 PowerShell 验证更改： 

```powershell
get-cluster | fl *subnet*
```

考虑以下情况： 

* 此项更改会立即生效，无需重启群集或任何资源。 
* 相同的子网值不应大于跨子网值。 
* SameSubnetThreshold <= CrossSubnetThreshold
* SameSubnetDelay <= CrossSubnetDelay

根据应用程序、业务需求和环境，基于可容许的停机时间以及在采取纠正措施之前问题可持续的时间选择宽松值。 如果无法超过默认的 Windows Server 2019 值，在可能的情况下，请至少尝试匹配这些值： 

下表详细说明了默认值供用户参考： 


| 设置 | Windows Server 2019 |  Windows Server 2016 |    Windows Server 2008 - 2012 R2 |
|:---------------------|:----------------|   ------------|:----------------------------|
| SameSubnetDelay      | 1 秒        | 1 秒       | 1 秒                    |
| SameSubnetThreshold  | 20 个检测信号   | 10 个检测信号  | 5 个检测信号               |
| CrossSubnetDelay     | 1 秒        | 1 秒     | 1 秒                    |
| CrossSubnetThreshold | 20 个检测信号   | 10 个检测信号   | 5 个检测信号               |


有关详细信息，请参阅[优化故障转移群集网络阈值](/windows-server/troubleshoot/iaas-sql-failover-cluster)。

## <a name="relaxed-monitoring"></a>宽松监视

如果根据建议优化群集检测信号和阈值设置不能实现足够的容许度，并且仍然发现暂时性问题（而不是真正的服务中断）导致故障，则可以将 AG 或 FCI 监视配置为更宽松的设置。 在某些场景中，在活动级别一定的情况下，将监视暂时放宽一段时间可能很有利。 例如，在执行数据库备份、索引维护、DBCC CHECKDB 等 IO 密集型工作负载时，你可能想要放宽监视。活动完成后，将监视设置为不太宽松的值。 

> [!WARNING]
> 更改这些设置可能会掩盖根本性问题，应将此做法用作临时性的解决方法来减少而不是消除发生故障的可能性。 仍应调查并解决根本性问题。 

首先，将以下参数在其默认值的基础上增大以放宽监视，并根据需要进行调整： 


|参数 |默认值  |宽松值  |说明  |
|---------|---------|---------|---------|
|Healthcheck 超时|30000 |60000 |确定主要副本或节点的运行状况。 群集资源 DLL sp_server_diagnostics 按等于运行状况检查超时阈值的 1/3 的间隔返回结果。 如果 sp_server_diagnostics 的运行速度较慢且未返回信息，则资源 DLL 将等待运行状况检查超时阈值的完整间隔，然后确定该资源无响应，并启动自动故障转移（如果已配置为执行此操作）。 |
|**故障条件级别** |  3  |   2  |触发自动故障转移的条件。 有 5 个故障条件级别，其范围从最低限制（级别 1）到最高限制（级别 5）。  |

使用 Transact-SQL (T-SQL) 修改 AG 和 FCI 的运行状况检查和故障条件。 

对于可用性组： 

```sql
ALTER AVAILABILITY GROUP AG1 SET (HEALTH_CHECK_TIMEOUT =60000);
ALTER AVAILABILITY GROUP AG1 SET (FAILURE_CONDITION_LEVEL = 2);
```

对于故障转移群集实例： 

```sql
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY HealthCheckTimeout = 60000;
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY FailureConditionLevel = 2; 
```

具体对于可用性组而言，请从以下建议的参数开始，并根据需要进行调整： 

|参数 |默认值  |宽松值  |说明  |
|---------|---------|---------|---------|
|**租用超时**|20000|40000|防止脑裂。 |
|**会话超时**|10000 |20000|检查副本之间的通信问题。 会话超时期限是一个副本属性，用于控制可用性副本等待已连接副本的 ping 响应的时间（秒数），超过该期限即认为连接失败。 默认情况下，副本等待 ping 响应的时长为 10 秒钟。 此副本属性仅适用于可用性组的给定次要副本与主副本之间的连接。 |
| 指定时段内的最大故障次数 | 2 | 6 |用于避免在多次发生节点故障期间无限期地移动群集资源。 值太小可能会导致可用性组处于故障状态。 由于值太小可能会导致 AG 处于故障状态，因此请增大该值，以防止性能问题造成短暂的中断。 | 

在更改之前请注意以下事项： 
- 不要将任何超时值降低到默认值以下。 
- 使用以下公式计算最大租用超时值：   
 `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
  首先从 40 秒开始。 如果使用的是之前建议的宽松 `SameSubnetThreshold` 和 `SameSubnetDelay` 值，则租用超时值不超过 80 秒。    
- 对于同步提交副本，将会话超时更改为较大值可以增大 HADR_sync_commit 等待时间。

**租用超时** 

使用故障转移群集管理器修改可用性组的租用超时设置 。 有关详细步骤，请参阅 SQL Server [可用性组租用运行状况检查](/sql/database-engine/availability-groups/windows/availability-group-lease-healthcheck-timeout#lease-timeout)文档。

**会话超时** 

使用 Transact-SQL (T-SQL) 修改可用性组的会话超时： 

```sql
ALTER AVAILABILITY GROUP AG1
MODIFY REPLICA ON 'INSTANCE01' WITH (SESSION_TIMEOUT = 15);
```

指定时段内的最大故障次数

使用故障转移群集管理器修改“指定时段内的最大故障次数”值： 
1. 在导航窗格中选择“角色”。
1. 在“角色”下，右键单击群集资源并选择“属性” 。 
1. 选择“故障转移”选项卡，并根据需要增大“指定时段内的最大故障次数”值 。 

## <a name="resource-limits"></a>资源限制

VM 或磁盘限制可能导致资源瓶颈，影响群集的运行状况并妨碍运行状况检查。 如果资源限制造成了问题，请考虑采取以下措施： 

* 确保 OS、驱动程序和 SQL Server 都是最新版本。
* 根据 Azure 虚拟机上的 SQL Server 的[性能指导原则](performance-guidelines-best-practices-checklist.md)中所述，优化 Azure VM 上的 SQL Server 环境
* 减少或分散工作负载，以便在不超过资源限制的情况下降低利用率
* 抓住任何机会优化 SQL Server 工作负载，例如
    * 添加/优化索引
    * 根据需要更新统计信息，并在可能的情况下使用完全扫描  
    * 在运行备份或索引维护等特定工作负载期间，使用资源调控器（从 SQL Server 2014 开始，仅适用于企业）等功能限制资源利用率。 
* 迁移到限制更高的 VM 或磁盘，以满足甚至超过工作负载的需求。 

## <a name="networking"></a>网络

每个服务器（群集节点）使用一个 NIC，并使用一个子网。 Azure 网络具有物理冗余，因此在 Azure 虚拟机来宾群集上不需要额外的 NIC 和子网。 群集验证报告将发出警告，指出节点只能在单个网络上访问。 在 Azure 虚拟机来宾故障转移群集上，可以忽略此警告。

Azure 中不符合 RFC 的 DHCP 服务可能会导致某些故障转移群集配置创建失败。 失败的原因是为群集网络名称分配了重复的 IP 地址，例如与某个群集节点相同的 IP 地址。 当你使用可用性组时，会产生一个问题，因为它依赖于 Windows 故障转移群集功能。

创建两节点群集并使其联机时，请考虑此应用场景：

1. 群集联机，NODE1 随后会为群集网络名称请求一个动态分配的 IP 地址。
2. DHCP 服务除了 NODE1 自身的 IP 地址以外不提供任何 IP 地址，因为 DHCP 服务可以识别请求是否来自 NODE1 自身。
3. Windows 检测到同时向 NODE1 和故障转移群集网络名称分配了一个重复的地址，并且默认群集组未能联机。
4. 默认群集组会移动到 NODE2。 NODE2 将 NODE1 的 IP 地址作为群集 IP 地址，并使默认群集组联机。
5. 当 NODE2 尝试与 NODE1 建立连接时，针对 NODE1 的数据包从不离开 NODE2，因为后者将 NODE1 的 IP 地址解析为其自身。 NODE2 无法与 NODE1 建立连接，它会丢失仲裁并关闭群集。
6. NODE1 可向 NODE2 发送数据包，但 NODE2 无法回复。 NODE1 丢失仲裁并关闭群集。

可将未使用的静态 IP 地址分配给群集网络名称，让群集网络名称联机，从而避免这种情况发生。 例如，可以使用 169.254.1.1 等本地链路 IP 地址。 若要简化此过程，请参阅[在 Azure 中针对可用性组配置 Windows 故障转移群集](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)。

有关详细信息，请参阅[在 Azure 中配置可用性组 (GUI)](./availability-group-quickstart-template-configure.md)。


## <a name="known-issues"></a>已知问题

查看一些常见的已知问题和错误的解决方法： 

**从成员身份中删除了群集节点**


如果 [Windows 群集检测信号和阈值设置](#heartbeat-and-threshold)对于环境而言过于严格，则系统事件日志中经常会出现以下消息。 

```
Error 1135
Cluster node 'Node1' was removed from the active failover cluster membership. 
The Cluster service on this node may have stopped. This could also be due to the node having 
lost communication with other active nodes in the failover cluster. Run the Validate a 
Configuration Wizard to check your network configuration. If the condition persists, check 
for hardware or software errors related to the network adapters on this node. Also check for 
failures in any other network components to which the node is connected such as hubs, switches, or bridges.
```


有关详细信息，请查看[排查附带事件 ID 1135 的群集问题](/windows-server/troubleshoot/troubleshooting-cluster-event-id-1135)。


**租约已过期** / **租约不再有效**


如果[监视](#relaxed-monitoring)对于环境而言过于严格，可能会频繁发生 AG 或 FCI 重启、故障或故障转移。 此外，对于可用性组，SQL Server 错误日志中可能会出现以下消息： 

```
Error 19407: The lease between availability group 'PRODAG' and the Windows Server Failover Cluster has expired. 
A connectivity issue occurred between the instance of SQL Server and the Windows Server Failover Cluster. 
To determine whether the availability group is failing over correctly, check the corresponding availability group 
resource in the Windows Server Failover Cluster
```

```
Error 19419: The renewal of the lease between availability group '%.*ls' and the Windows Server Failover Cluster 
failed because the existing lease is no longer valid. 
``` 

**连接超时值**

如果会话超时对于可用性组环境而言过于严格，可能会经常出现以下消息：

```
Error 35201: A connection timeout has occurred while attempting to establish a connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or firewall issue exists, 
or the endpoint address provided for the replica is not the database mirroring endpoint of the host server instance.
```

```
Error 35206
A connection timeout has occurred on a previously established connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or a firewall issue 
exists, or the availability replica has transitioned to the resolving role. 
```

**组不故障转移**



如果“指定时段内的最大故障次数”值太小，并且暂时性问题导致了间歇性故障，则可用性组最终可能会进入故障状态。 请增大此值以容许更多的暂时性故障。 

```
Not failing over group <Resource name>, failoverCount 3, failoverThresholdSetting <Number>, computedFailoverThreshold 2. 
```


## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅以下文章：

- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)
- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [故障转移群集实例与 Azure VM 上的 SQL Server](failover-cluster-instance-overview.md)
- [故障转移群集实例概述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
