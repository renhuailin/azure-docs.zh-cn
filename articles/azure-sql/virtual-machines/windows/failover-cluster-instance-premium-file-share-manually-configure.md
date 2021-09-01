---
title: 使用高级文件共享创建 FCI
description: 使用高级文件共享 (PFS) 为 Azure 虚拟机上的 SQL Server 创建故障转移群集实例 (FCI)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: fa70dce0e245f706e5278e7274ac17855b50622f
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396897"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>使用高级文件共享创建 FCI（Azure VM 上的 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了如何使用[高级文件共享](../../../storage/files/storage-how-to-create-file-share.md)为 Azure 虚拟机 (VM) 上的 SQL Server 创建故障转移群集实例 (FCI)。

高级文件共享以存储空间直通 (SSD) 为基础，可以稳定保持较低的延迟，完全支持用于 Windows Server 2012 或更高版本上的 SQL Server 2012 或更高版本的故障转移群集实例。 高级文件共享提供更高的灵活性，使你能在不停机的情况下对文件共享进行大小调整和缩放。

若要了解详细信息，请参阅 [Azure VM 上的 SQL Server 的 FCI](failover-cluster-instance-overview.md) 和[群集最佳做法](hadr-cluster-best-practices.md)。 

> [!NOTE]
> 现在，可以使用 Azure Migrate 将故障转移群集实例解决方案直接迁移到 Azure VM 上的 SQL Server。 有关详细信息，请参阅[迁移故障转移群集实例](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)。 

## <a name="prerequisites"></a>先决条件

在按本文中的说明操作之前，你应已具备以下条件：

- Azure 订阅。
- 有权限在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- 一个[可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)中的或不同的[可用性区域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)中的[两个或更多个已准备好的 Windows Azure 虚拟机](failover-cluster-instance-prepare-vm.md)。
- 要基于数据库中数据文件的存储配额，用作群集驱动器的[高级文件共享](../../../storage/files/storage-how-to-create-file-share.md)。
- 最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="mount-premium-file-share"></a>装载高级文件共享

1. 登录 [Azure 门户](https://portal.azure.com)。 转到你的存储帐户。
1. 转到“数据存储”下的“文件共享”，然后选择要用于 SQL 存储的高级文件共享 。
1. 选择“连接”使系统显示文件共享的连接字符串。
1. 在下拉列表中，选择要使用的驱动器号，选择“存储帐户密钥”作为身份验证方法，然后将代码块复制到文本编辑器（如记事本）。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="从文件共享连接门户复制 PowerShell 命令":::

1. 使用由 SQL Server FCI 用作服务帐户的帐户通过远程桌面协议 (RDP) 连接到 SQL Server VM。
1. 打开 PowerShell 命令管理控制台。
1. 运行之前从文件共享门户复制到文本编辑器的命令。
1. 使用文件资源管理器或“运行”对话框（键盘上的 Windows + R）转到共享。 使用网络路径 `\\storageaccountname.file.core.windows.net\filesharename`。 例如： `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`
1. 在新连接的文件共享上创建至少一个文件夹以将 SQL 数据文件放入其中。
1. 在将加入群集的每个 SQL Server VM 上重复这些步骤。

  > [!IMPORTANT]
  > - 考虑对备份文件使用单独的文件共享，以便节省此共享中数据和日志文件占用的每秒输入/输出操作数 (IOPS) 和空间容量。 可将高级或标准文件共享用于备份文件。


## <a name="add-windows-cluster-feature"></a>添加 Windows 群集功能

1. 使用属于本地管理员的成员并且有权在 Active Directory 中创建对象的域帐户，通过 RDP 连接到第一个虚拟机。 使用此帐户完成余下的配置。

1. [将故障转移群集添加到每个虚拟机](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要通过 UI 安装故障转移群集，请在两个虚拟机上执行以下步骤：
   1. 在“服务器管理器”中选择“管理”，然后选择“添加角色和功能”。  
   1. 在“添加角色和功能”向导中选择“下一步”，直到出现“选择功能”。  
   1. 在“选择功能”中，选择“故障转移群集”。  请包含所有所需的功能和管理工具。 
   1. 选择“添加功能”。
   1. 选择“下一步”，然后选择“完成”安装这些功能。 

   若要使用 PowerShell 安装故障转移群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```



## <a name="create-failover-cluster"></a>创建故障转移群集

若要创建故障转移群集，需要：

- 将成为群集节点的虚拟机的名称。
- 故障转移群集的名称。
- 故障转移群集的 IP 地址。 可以使用群集节点所在的同一 Azure 虚拟网络和子网中未使用的 IP 地址。


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

对于 Windows server 2012 到 Windows Server 2016，可通过以下 PowerShell 脚本创建故障转移群集。 使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

以下 PowerShell 脚本为 Windows Server 2019 创建故障转移群集。  使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

有关详细信息，请参阅[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。

---

## <a name="configure-quorum"></a>配置仲裁

尽管磁盘见证是最具复原能力的仲裁选项，但它需要一个 Azure 共享磁盘，在使用高级文件共享配置时对故障转移群集实例施加一些限制。 因此，对于 Azure VM 上的 SQL Server 的此类群集配置，云见证是建议的仲裁解决方案。 否则，请配置文件共享见证。 

如果群集中的投票数为偶数，请配置最适合你的业务需求的[仲裁解决方案](hadr-cluster-quorum-configure-how-to.md)。 有关详细信息，请参阅 [SQL Server VM 上的仲裁](hadr-windows-server-failover-cluster-overview.md#quorum)。 

## <a name="validate-cluster"></a>验证群集

使用 UI 或 PowerShell 验证群集。

若要使用 UI 来验证群集，请在某个虚拟机中执行以下步骤：

1. 在“服务器管理器”下，依次选择“工具”、“故障转移群集管理器”。  
1. 在“故障转移群集管理器”下，依次选择“操作”、“验证配置”。  
1. 选择“**下一页**”。
1. 在“选择服务器或群集”下，输入两个虚拟机的名称。
1. 在“测试选项”下，选择“仅运行选择的测试”。  
1. 选择“**下一页**”。
1. 在“测试选择”下，选择除“存储”和“存储空间直通”以外的所有测试，如下所示  ：

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="选择群集验证测试":::

1. 选择“**下一页**”。
1. 在“确认”下，选择“下一步”。 

“验证配置”向导会运行验证测试。

若要使用 PowerShell 验证群集，请在某个虚拟机上通过管理员 PowerShell 会话运行以下脚本：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```



## <a name="test-cluster-failover"></a>测试群集故障转移

测试你的群集的故障转移。 在“故障转移群集管理器”中，右键单击你的群集并选择“更多操作” > “移动核心群集资源” > “选择节点”，然后选择群集的其他节点。    将核心群集资源移到群集的每个节点，再将它移回主节点。 如果可以成功将群集移到每个节点，则表示你已为安装 SQL Server 做好了准备。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="通过将核心资源移到其他节点来测试群集故障转移":::


## <a name="create-sql-server-fci"></a>创建 SQL Server FCI

配置故障转移群集后，可以创建 SQL Server FCI。

1. 使用 RDP 连接到第一个虚拟机。

1. 在“故障转移群集管理器”中，确保所有核心群集资源位于第一个虚拟机上。 如有必要，请将所有资源移到此虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 

1. 选择“设置”。

1. 在“SQL Server 安装中心”中选择“安装” 。

1. 选择“新的 SQL Server 故障转移群集安装”，然后按照向导中的说明安装 SQL Server FCI。

   FCI 数据目录需位于高级文件共享上。 输入共享的完整路径，格式为：`\\storageaccountname.file.core.windows.net\filesharename\foldername`。 系统将显示一条警告，提示你已将文件服务器指定为数据目录。 预计会出现此警告。 请确保在保存文件共享时通过 RDP 访问 VM 时所用的用户帐户，是 SQL Server 服务用来避免可能的故障的同一帐户。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="将文件共享用作 SQL 数据目录":::

1. 完成向导中的步骤后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 安装程序在第一个节点上安装 FCI 后，请使用 RDP 连接到第二个节点。

1. 打开“SQL Server 安装中心”，然后选择“安装”。 

1. 选择“将节点添加到 SQL Server 故障转移群集”。 按照向导中的说明安装 SQL Server 并将此服务器添加到 FCI。

   >[!NOTE]
   >如果使用了包含 SQL Server 的 Azure 市场库映像，该映像已随附 SQL Server 工具。 如果未使用其中的某个映像，请单独安装 SQL Server 工具。 有关详细信息，请参阅 [下载 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)。

1. 在要添加到 SQL Server 故障转移群集实例的任何其他节点上重复这些步骤。 

## <a name="register-with-the-sql-vm-rp"></a>注册到 SQL VM RP

若要从门户管理 SQL Server VM，请在[轻型管理模式](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)下将其注册到 SQL IaaS 代理扩展 (RP)。目前，这是 Azure VM 上的 FCI 和 SQL Server 唯一支持的模式。 

使用 PowerShell 在轻型模式下注册 SQL Server VM（-LicenseType 可以是 `PAYG` 或 `AHUB`）：

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>配置连接 

可为故障转移群集实例配置虚拟网络名称或分布式网络名称。 [查看两者之间的差异](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)；然后，为故障转移群集实例部署[分布式网络名称](failover-cluster-instance-distributed-network-name-dnn-configure.md)或[虚拟网络名称](failover-cluster-instance-vnn-azure-load-balancer-configure.md)。

## <a name="limitations"></a>限制

- Windows Server 2016 及更低版本不支持 Microsoft 分布式事务处理协调器 (MSDTC)。 
- 使用高级文件共享的故障转移群集不支持文件流。 若要使用文件流，请改用[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)或 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)部署你的群集。
- 仅支持在[轻型管理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)下注册到 SQL IaaS 代理扩展。 
- [由于稀疏文件限制，Azure 文件存储](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)当前不支持数据库快照。
- 当前不支持运行 DBCC CHECKDB，因为无法创建数据库快照。 
- 使用高级文件共享部署的故障转移群集实例不支持使用内存中 OLTP 功能的数据库。 如果你的业务需要内存中 OLTP，请考虑改用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)或[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)来部署 FCI。

## <a name="next-steps"></a>后续步骤

使用[虚拟网络名称和 Azure 负载均衡器](failover-cluster-instance-vnn-azure-load-balancer-configure.md)或[分布式网络名称 (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) 配置到 FCI 的连接（如果尚未执行此操作）。 


如果高级文件共享不是适合你的 FCI 存储解决方案，请考虑改用 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)或[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)创建 FCI。 

若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [故障转移群集实例与 Azure VM 上的 SQL Server](failover-cluster-instance-overview.md)
- [故障转移群集实例概述](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)

