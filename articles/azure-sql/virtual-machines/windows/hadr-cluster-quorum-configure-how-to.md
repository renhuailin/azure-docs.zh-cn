---
title: 配置群集仲裁
description: '了解如何为 Azure VM 上的 SQL Server 的 Windows Server 故障转移群集配置磁盘见证、云见证或文件共享见证作为仲裁。 '
services: virtual-machines
documentationCenter: na
author: cawrites
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: chadam
ms.openlocfilehash: b1db3b5d809c8dd80b0d03d5bdd15636115fdef0
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581936"
---
# <a name="configure-cluster-quorum-for-sql-server-on-azure-vms"></a>为 Azure VM 上的 SQL Server 配置群集仲裁
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文指导你为 Azure 虚拟机 (VM) 上的 SQL Server 上运行的 Windows Server 故障转移群集配置三个仲裁选项之一 - 磁盘见证、云见证和文件共享见证。


## <a name="overview"></a>概述

群集的仲裁由投票元素数确定，这些元素必须属于活动群集成员身份，以便群集正确启动或继续运行。 配置仲裁资源，将使两节点群集在只有一个节点联机时可以继续联机。 Windows Server 故障转移群集是 Azure VM 上的 SQL Server 高可用性选项的基础技术：[故障转移群集实例 (FCI)](failover-cluster-instance-overview.md) 和[可用性组 (AG)](availability-group-overview.md)。 

磁盘见证是最具复原能力的仲裁选项，但要对 Azure VM 上的 SQL Server 使用磁盘见证，必须使用 Azure 共享磁盘，这对高可用性解决方案施加了一些限制。 因此，在使用 Azure 共享磁盘配置故障转移群集实例时使用磁盘见证，否则尽可能使用云见证。 如果使用的是不支持云见证的 Windows Server 2012 R2 或更旧版本，则可以使用文件共享见证。 

以下仲裁选项可用于 Azure VM 上的 SQL Server： 

|  |[云见证](/windows-server/failover-clustering/deploy-cloud-witness) |[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支持的 OS**| Windows Server 2016 及更高版本 |全部 | 全部|

若要了解有关仲裁的详细信息，请参阅 [Windows Server 故障转移群集概述](hadr-windows-server-failover-cluster-overview.md)。 

## <a name="cloud-witness"></a>云见证

云见证是一种故障转移群集仲裁见证，它使用 Microsoft Azure 存储提供对群集仲裁的投票。 


下表提供了有关云见证的其他信息和注意事项： 

| 见证类型  | 说明  | 要求和建议  |
| ---------    |---------        |---------                        |
| 云见证     |  <ul><li> 使用 Azure 存储作为云见证，仅包含时间戳。 </li><li> 在多个站点、多个区域和多个地区中进行部署的理想选择。</li> <li> 在 Microsoft 存储帐户下创建已知容器 `msft-cloud-witness`。 </li> <li> 写入一个 blob 文件，其中包含对应群集的唯一 ID，该 ID 用作此容器下 blob 文件的文件名 </li>      |  <ul><li>默认大小为 1 MB。</li><li> 使用“常规用途”作为帐户类型。 不支持 Blob 存储。 </li><li> 使用标准存储。 不支持 Azure 高级存储。 </li><li> 故障转移群集使用 blob 文件作为仲裁点，这在读取数据时需要一些一致性保证。 因此，对于“复制”类型，必须选择“本地冗余存储”。</li><li> 应从备份和防病毒扫描中排除</li><li> 存储空间直通不支持磁盘见证</li> <li> 云见证使用 HTTPS（默认端口 443）来与 Azure blob 服务建立通信。 确保可通过网络代理访问 HTTPS 端口。 </li>|

为故障转移群集配置云见证仲裁资源时，请考虑：
- 故障转移群集将生成并安全地存储共享访问安全 (SAS) 令牌，而不是存储访问密钥。
- 只要访问密钥保持有效，生成的 SAS 令牌就仍有效。 轮换主访问密钥时，必须先使用辅助访问密钥更新云见证（在使用存储帐户的所有群集上），然后再重新生成主访问密钥。
- 云见证使用 Azure 存储帐户服务的 HTTPS REST 接口。 这意味着它需要在所有群集节点上打开 HTTPS 端口。


云见证需要 Azure 存储帐户。 要配置存储帐户，请执行以下步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“新建”->“数据 + 存储”->“存储帐户”。
3. 在“创建存储帐户”页中执行以下操作：
    1. 为存储帐户输入名称。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 存储帐户名称在 Azure 中也必须是唯一的。
    2. 对于“帐户类型”，选择“常规用途” 。
    3. 对于“性能”，请选择“标准”。
    2. 对于“复制”，请选择“本地冗余存储 (LRS)”。


创建存储帐户后，请按照以下步骤为故障转移群集配置云见证仲裁资源： 


# <a name="powershell"></a>[PowerShell](#tab/powershell)

现有的 Set-ClusterQuorum PowerShell 命令具有与云见证对应的新参数。

可以使用 PowerShell 命令的 cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) 配置云见证：

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey>
```

在极少数情况下，需要使用不同的终结点，请使用以下 PowerShell 命令： 

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey> -Endpoint <servername>
```

有关查找存储帐户 AccessKey 的帮助，请参阅[云见证文档](/windows-server/failover-clustering/deploy-cloud-witness)。 


# <a name="failover-cluster-manager"></a>[故障转移群集管理器](#tab/fcm-gui)

使用内置于故障转移群集管理器中的仲裁配置向导配置云见证。 为此，请执行下列步骤： 

1. 打开故障转移群集管理器。

2. 右键单击“群集”->“更多操作” -> “配置群集仲裁设置”。 这会启动“配置群集仲裁”向导。

    ![在故障转移群集管理器 UI 中配置群集仲裁设置的菜单路径的快照](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-7.png)
    
3. 在“选择仲裁配置”页上，选择“选择仲裁见证”。

    ![群集仲裁向导中“选择仲裁见证”单选按钮的快照](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-8.png)
   
4. 在“选择仲裁见证”页上，选择“配置云见证”。

    ![用于选择云见证的相应单选按钮的快照](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-9.png)
    
5. 在“配置云见证”页上，输入 Azure 存储帐户信息。 有关查找此信息的帮助，请参阅[云见证文档](/windows-server/failover-clustering/deploy-cloud-witness)。 
   1. （必需参数）Azure 存储帐户名称。
   2. （必需参数）与存储帐户对应的访问密钥。
       1. 首次创建时，使用主访问密钥 
       2. 轮换主访问密钥时，使用辅助访问密钥
   3. （可选参数）如果要使用不同的 Azure 服务终结点（例如中国的 Microsoft Azure 服务），则更新终结点服务器名称。

    ![群集仲裁向导中“云见证配置”窗格的快照](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-10.png)
      

6. 成功配置云见证后，可以在故障转移群集管理器管理单元中查看新创建的见证资源。

    ![成功配置云见证](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-11.png)
    


---


## <a name="disk-witness"></a>磁盘见证

磁盘见证是群集可用存储组中的小型群集磁盘。 此磁盘具有高可用性，并且可在节点之间故障转移。 

与共享存储高可用性解决方案（例如具有 Azure 共享磁盘的故障转移群集实例）一起使用时，磁盘见证是建议的仲裁选项。 

下表提供了有关仲裁磁盘见证的其他信息和注意事项： 

| 见证类型  | 说明  | 要求和建议  |
| ---------    |---------        |---------                        |
| 磁盘见证     |  <ul><li> 存储群集数据库副本的专用 LUN</li><li> 对具有共享（非复制）存储的群集最有用</li>       |  <ul><li>LUN 的大小必须至少有 512 MB</li><li> 必须专用于群集而且不分配给群集角色</li><li> 必须包括在群集存储中并且通过存储验证测试</li><li> 不可以是群集共享卷 (CSV) 磁盘</li><li> 具有单个卷的基本磁盘</li><li> 不需要有驱动器号</li><li> 可以使用 NTFS 或 ReFS 格式化</li><li> 可以使用容错功能的硬件 RAID 有选择性地进行配置</li><li> 应从备份和防病毒扫描中排除</li><li> 存储空间直通不支持磁盘见证</li>|

若要将 Azure 共享磁盘用于磁盘见证，必须先创建该磁盘并装载它。 为此，请按照 Azure 共享磁盘故障转移群集实例指南中的[装载磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md#add-azure-shared-disk)部分中的步骤进行操作。 磁盘不必是高级磁盘。 

装载磁盘后，将其添加到群集存储中，步骤如下： 

1. 打开故障转移群集管理器。 
1. 在左侧导航窗格上，选择“存储”下的“磁盘”。 
1. 在右侧导航窗格上，选择“操作”下的“添加磁盘”。 
1. 选择刚装载的 Azure 共享驱动器并记下名称，例如 `Cluster Disk 3`。 

添加磁盘作为群集存储后，使用 PowerShell 将其配置为磁盘见证：  


现有的 Set-ClusterQuorum PowerShell 命令具有与云见证对应的新参数。

使用 PowerShell cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) 时，使用文件共享的路径作为磁盘见证的参数：

```PowerShell
Set-ClusterQuorum -NodeAndDiskMajority "Cluster Disk 3"
```

还可以使用故障转移群集管理器；遵循与云见证相同的步骤，但改为选择磁盘见证作为仲裁选项。 


## <a name="file-share-witness"></a>文件共享见证

文件共享见证是一个 SMB 文件共享，通常在运行 Windows Server 的文件服务器上配置。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。 在 Azure 中，你可以在单独的虚拟机上配置文件共享。 

如果磁盘见证或云见证在你的环境中不可用或不受支持，则配置文件共享见证。 

下表提供了有关仲裁文件共享见证的其他信息和注意事项： 

| 见证类型  | 说明  | 要求和建议  |
| ---------    |---------        |---------                        |
| 文件共享见证     | <ul><li>在运行 Windows Server 的文件服务器上配置的 SMB 文件共享</li><li> 不存储群集数据库的副本</li><li> 仅维护 witness.log 文件中的群集信息</li><li> 对具有复制存储的多站点群集最有用 </li>       |  <ul><li>必须至少具有 5 MB 的可用空间</li><li> 必须专用于单个群集而不用于存储用户或应用程序数据</li><li> 必须对群集名称的计算机对象启用写入权限</li></ul><br>以下是有关托管文件共享见证的文件服务器的其他注意事项：<ul><li>可以使用多个群集的文件共享见证配置单个文件服务器。</li><li> 文件服务器必须位于与群集工作负载分开的站点上。 如果站点到站点网络通信丢失，则允许向任何群集站点提供均等的生存机会。 如果文件服务器位于同一站点上，则该站点成为主站点，并且它是可以访问文件共享的唯一站点。</li><li> 如果虚拟机未托管在使用文件共享见证的同一群集上，则文件服务器可以在虚拟机上运行。</li><li> 为了获得高可用性，可在单独的故障转移群集上配置文件服务器。 </li>      |

创建文件共享并正确配置权限后，将文件共享装入群集节点。 可以执行与装载文件共享相同的常规步骤，如高级文件共享故障转移群集实例操作指南中的[装载文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)部分中所述。 

正确配置并装载文件共享后，使用 PowerShell 添加文件共享作为仲裁见证资源： 

```powershell
Set-ClusterQuorum -FileShareWitness <UNC path to file share> -Credential $(Get-Credential)
```

系统将提示你输入对共享具有完全管理权限的本地（对于文件共享）非管理员帐户的帐户和密码。  群集将保持名称和密码的加密，并且任何人都无法访问。

你还可以使用故障转移群集管理器；遵循与云见证相同的步骤，但改为选择文件共享见证作为仲裁选项。 

## <a name="change-quorum-voting"></a>更改仲裁投票


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




## <a name="next-steps"></a>后续步骤

若要了解更多信息，请参阅以下文章：

- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)
- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [故障转移群集实例与 Azure VM 上的 SQL Server](failover-cluster-instance-overview.md)
- [故障转移群集实例概述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
