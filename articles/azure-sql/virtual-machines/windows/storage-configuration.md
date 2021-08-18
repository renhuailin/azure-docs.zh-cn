---
title: 为 SQL Server VM 配置存储 | Microsoft Docs
description: 本主题介绍 Azure 在预配期间如何配置 SQL Server VM 的存储（Azure 资源管理器部署模型）。 此外，还说明了如何为现有的 SQL Server VM 配置存储。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 3ddcd99257eb7605322fe81f1002123d4876456b
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435409"
---
# <a name="configure-storage-for-sql-server-vms"></a>为 SQL Server VM 配置存储
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何在 Azure Virtual Machines (VM) 上为 SQL Server 配置存储。

通过市场映像部署的 SQL Server VM 自动遵循默认的[存储最佳做法](performance-guidelines-best-practices-storage.md)，但可以在部署过程中修改默认的存储最佳做法。 某些配置设置可以在部署后更改。


## <a name="prerequisites"></a>先决条件

若要使用自动存储配置设置，虚拟机需有以下特征：

* 使用 [SQL Server 库映像](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)进行了预配，或者已经注册了 [SQL IaaS 扩展]()。
* 使用 [Resource Manager 部署模型](../../../azure-resource-manager/management/deployment-models.md)。
* 使用[高级 SSD](../../../virtual-machines/disks-types.md)。

## <a name="new-vms"></a>新的 VM

以下部分介绍了如何为新的 SQL Server 虚拟机配置存储。

### <a name="azure-portal"></a>Azure 门户

使用 SQL Server 库映像预配 Azure VM 时，请选择“SQL Server 设置”选项卡上的“更改配置”，以打开“性能优化存储配置”页。  你可以将值保留为默认值，或根据工作负载修改最适合你的需求的磁盘配置类型。

![突出显示“SQL Server 设置”选项卡和“更改配置”选项的屏幕截图。](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

在 **存储优化** 下选择要为其部署 SQL Server 的工作负荷类型。 使用“常规”优化选项时，默认情况下，你将有一个最大 IOPS 为 5000 的数据磁盘，你的数据、事务日志和 TempDB 存储都将使用此相同驱动器。

选择 **事务性处理** (OLTP) 或 **数据仓库** 将为数据创建单独的磁盘、为事务日志创建单独的磁盘，并将本地 SSD 用于 TempDB。 **事务处理** 与 **数据仓库** 之间没有存储差异，但它确实更改了 [条带化配置和跟踪标志](#workload-optimization-settings)。 根据 [SQL Server VM 性能最佳方案](./performance-guidelines-best-practices-checklist.md)，选择“高级存储”会将数据驱动器的缓存设置为“只读”，为日志驱动器的缓存设置为“None”。 

![预配期间的 SQL Server VM 存储配置](./media/storage-configuration/sql-vm-storage-configuration.png)

磁盘配置可完全自定义，因此，你可以配置 SQL Server VM 工作负荷所需的存储拓扑、磁盘类型和 IOPs。 如果你的 SQL Server VM 位于某个受支持的区域（美国东部 2、东南亚和北欧），并且你已为 [订阅启用了的超磁盘](../../../virtual-machines/disks-enable-ultra-ssd.md)，你还可以使用 UltraSSD（预览）作为 **磁盘类型** 的选项。

此外，还可以设置磁盘的缓存。 与[高级磁盘](../../../virtual-machines/disks-types.md#premium-ssd)一起使用时，Azure VM 具有一种称为 [Blob 缓存的多层缓存技术](../../../virtual-machines/premium-storage-performance.md#disk-caching)。 Blob Cache 使用虚拟机 RAM 和本地 SSD 的组合进行缓存。

高级 SSD 的磁盘缓存可以是 *ReadOnly*、*ReadWrite* 或 *None*。

- 对于存储在高级存储上的 SQL Server 数据文件，*ReadOnly* 缓存非常有用。 *ReadOnly* 缓存提供较低的读取延迟、较高的读取 IOPS 和吞吐量，因为从缓存（位于 VM 内存和本地 SSD 内）执行读取。 与从 Azure Blob 存储读取数据磁盘相比，这些读取速度要快得多。 高级存储不将从缓存提供的读取操作计入磁盘 IOPS 和吞吐量。 因此，应用程序能够实现更高的总 IOPS 和吞吐量。
- *None* 缓存配置应用于承载 SQL Server 日志文件的磁盘，因为日志文件是按顺序写入的，不能从 *ReadOnly* 缓存中获益。
- 不应使用 *ReadWrite* 缓存来承载 SQL Server 文件，因为 SQL Server 不支持与 *ReadWrite* 缓存的数据一致性。 如果写入操作通过 *ReadOnly* blob 缓存层，则写入会浪费 *ReadOnly* blob 缓存的容量并且延迟略微增加。


   > [!TIP]
   > 请确保存储配置与所选 VM 大小施加的限制相匹配。 选择超出 VM 大小的性能上限的存储参数将导致警告：`The desired performance might not be reached due to the maximum virtual machine disk performance cap`。 可以通过更改磁盘类型来减少 IOPs，或增加 VM 大小以提高性能上限。 这不会停止预配。


根据所做的选择，Azure 会在创建 VM 后执行以下存储配置任务：

* 创建高级 SSD 盘并将其附加到虚拟机。
* 配置 SQL Server 可访问的数据磁盘。
* 根据指定的大小和性能（IOPS 和吞吐量）要求，在存储池中配置数据磁盘。
* 将存储池与虚拟机上的新驱动器相关联。
* 根据指定的工作负荷类型（“数据仓库”、“事务处理”或“常规”）优化此新驱动器。

有关如何在 Azure 门户中创建 SQL Server VM 的完整演练，请参阅[预配教程](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)。

### <a name="resource-manager-templates"></a>Resource Manager 模板

如果使用以下 Resource Manager 模板，则会默认附加两个不带存储池配置的高级数据磁盘。 但是，可以自定义这些模板，更改附加到虚拟机的高级数据磁盘的数目。

* [使用自动备份创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autobackup)
* [使用自动修补创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autopatching)
* [使用 AKV 集成创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-keyvault)

### <a name="quickstart-template"></a>快速入门模板

可以使用以下快速入门模板通过存储优化来部署 SQL Server VM。

* [通过存储优化创建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage/)
* [创建使用 UltraSSD 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>现有 VM

对于现有的 SQL Server VM，可以在 Azure 门户中修改某些存储设置。 打开 [SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-resource)，并选择 **概述**。 “SQL Server 概述”页面显示了 VM 当前的存储用量。 此图显示了 VM 上存在的所有驱动器。 每个驱动器的存储空间都分四个部分显示：

* SQL 数据
* SQL 日志
* 其他（非 SQL 存储）
* 可用

要修改存储设置，请选择“设置”下的“配置”。 

![突出显示“配置”选项和“存储使用情况”部分的屏幕截图。](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

可以修改在 SQL Server VM 创建过程中配置的驱动器的磁盘设置。 选择“扩展驱动器”将打开驱动器修改页，使你可以更改磁盘类型以及添加更多磁盘。

![为现有 SQL Server VM 配置存储](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>自动更改

本部分提供有关在 Azure 门户中预配或配置 SQL Server VM 期间，Azure 自动执行的存储配置更改的参考信息。

* Azure 通过从 VM 中选择的存储配置存储池。 本主题的下一部分提供了有关存储池配置的详细信息。
* 自动存储配置始终使用[高级 SSD](../../../virtual-machines/disks-types.md) P30 数据磁盘。 因此，所选 TB 数目与附加到 VM 的数据磁盘数目之间存在 1:1 映射。

有关价格信息，请参阅“磁盘存储”选项卡上的[存储定价](https://azure.microsoft.com/pricing/details/storage)页。

### <a name="creation-of-the-storage-pool"></a>创建存储池

Azure 使用以下设置在 SQL Server VM 上创建存储池。

| 设置 | 值 |
| --- | --- |
| 条带大小 |256 KB（数据仓库）；64 KB（事务） |
| 磁盘大小 |每个磁盘 1 TB |
| 缓存 |读取 |
| 分配大小 |64 KB NTFS 分配单元大小 |
| 恢复 | 简单恢复（不可复原） |
| 列数 |数据磁盘数最多 8 个<sup>1</sup> |


<sup>1</sup> 创建存储池后，无法更改存储池中的列数。


### <a name="workload-optimization-settings"></a>工作负荷优化设置

下表描述了三个可用的工作负荷类型选项及其对应的优化：

| 工作负荷类型 | 说明 | 优化 |
| --- | --- | --- |
| **常规** |支持大多数工作负荷的默认设置 |无 |
| **事务处理** |针对传统数据库 OLTP 工作负荷优化存储 |跟踪标志 1117<br/>跟踪标志 1118 |
| **数据仓库** |针对分析和报告工作负荷优化存储 |跟踪标志 610<br/>跟踪标志 1117 |

> [!NOTE]
> 只有在预配 SQL Server 虚拟机时才能指定工作负载类型，方法是在存储配置步骤中进行选择。

## <a name="enable-caching"></a>启用缓存

在磁盘级别更改缓存策略。 可以使用 Azure 门户、[PowerShell](/powershell/module/az.compute/set-azvmdatadisk) 或 [Azure CLI](/cli/azure/vm/disk) 执行此操作。

若要在 Azure 门户中更改缓存策略，请按照以下步骤操作：

1. 停止 SQL Server 服务。
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到虚拟机，在“设置”下面选择“磁盘”。 

   ![显示 Azure 门户中 VM 磁盘配置边栏选项卡的截图。](./media/storage-configuration/disk-in-portal.png)

1. 从下拉列表中为磁盘选择适当的缓存策略。

   ![显示 Azure 门户中磁盘缓存策略配置的屏幕截图。](./media/storage-configuration/azure-disk-config.png)

1. 更改生效后，重新启动 SQL Server VM 并启动 SQL Server 服务。


## <a name="enable-write-accelerator"></a>启用写入加速器

写入加速是一项磁盘功能，仅适用于 M 系列虚拟机 (VM)。 写入加速的目的在于，当你因大量的任务关键型 OLTP 工作负载或数据仓库环境而需要个位数 I/O 延迟时，可以缩短对 Azure 高级存储进行写入的 I/O 延迟。

在对写入加速策略进行更改之前，停止所有 SQL Server 活动并关闭 SQL Server 服务。

如果磁盘已条带化，请分别为每个磁盘启用写入加速，并在进行任何更改之前关闭 Azure VM。

若要使用 Azure 门户启用写入加速，请执行以下步骤：

1. 停止 SQL Server 服务。 如果磁盘已条带化，请关闭虚拟机。
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到虚拟机，在“设置”下面选择“磁盘”。 

   ![显示 Azure 门户中 VM 磁盘配置边栏选项卡的截图。](./media/storage-configuration/disk-in-portal.png)

1. 从下拉列表中，为磁盘选择包含“写入加速器”的缓存选项。

   ![显示写入加速器缓存策略的屏幕截图。](./media/storage-configuration/write-accelerator.png)

1. 更改生效后，启动虚拟机和 SQL Server 服务。

## <a name="disk-striping"></a>磁盘条带化

为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，请分析 SQL Server 数据文件（包括日志和 tempdb）所需的吞吐量和带宽。 吞吐量和带宽限制因 VM 大小而异。 若要了解详细信息，请参阅 [VM 大小](../../../virtual-machines/sizes.md)


* 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))：

  1. 将交错（条带大小）设置为 64 KB（65,536字节），以避免因分区不对齐而影响性能。 这必须使用 PowerShell 设置。

  2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。

例如，以下 PowerShell 创建新的存储池，其交错大小为 64 KB，其列数等于存储池中的物理磁盘数量：

# <a name="windows-server-2016-"></a>[Windows Server 2016 +](#tab/windows2016)

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Windows Storage on <VM Name>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false
  ```

在 Windows Server 2016 及更高版本中，`-StorageSubsystemFriendlyName` 的默认值为 `Windows Storage on <VM Name>`



# <a name="windows-server-2008---2012-r2"></a>[Windows Server 2008 - 2012 R2](#tab/windows2012)



  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces on <VMName>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

在 Windows Server 2008 到 2012 R2 中，`-StorageSubsystemFriendlyName` 的默认值为 `Storage Spaces on <VMName>`。 

---


  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 从 Windows 8/Windows Server 2012 开始不再提供此选项。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api) 中的支持声明。

  * 如果将[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 与 [SQL Server 故障转移群集实例](./failover-cluster-instance-storage-spaces-direct-manually-configure.md)配合使用，则必须配置单个池。 虽然可以在该单个池上创建不同的卷，但它们都拥有相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)。


## <a name="next-steps"></a>后续步骤

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [SQL Server on Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md)（Azure 虚拟机上的 SQL Server）。

