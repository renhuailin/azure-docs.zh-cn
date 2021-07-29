---
title: 将 Azure VM 备份到恢复服务保管库中
description: 介绍如何使用 Azure 备份将 Azure VM 备份到恢复服务保管库中
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 42ce8990e4593fc734be619aa363f080f3055878
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783559"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>将 Azure VM 备份到恢复服务保管库中

本文介绍如何使用 [Azure 备份](backup-overview.md)服务将 Azure VM 备份到恢复服务保管库中。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 准备 Azure VM。
> * 创建保管库。
> * 发现 VM 并配置备份策略。
> * 为 Azure VM 启用备份。
> * 运行初始备份。

> [!NOTE]
> 本文介绍如何设置保管库并选择要备份的 VM。 若要备份多个 VM，则本文的内容会有所帮助。 或者，可以直接从 VM 设置[备份单个 Azure VM](backup-azure-vms-first-look-arm.md)。

## <a name="before-you-start"></a>开始之前

* [查看](backup-architecture.md#architecture-built-in-azure-vm-backup) Azure VM 备份体系结构。
* [了解](backup-azure-vms-introduction.md) Azure VM 备份和备份扩展。
* 在配置备份之前，[查看支持矩阵](backup-support-matrix-iaas.md)。

此外，在某些情况下，可能需要执行以下几个操作：

* 在 VM 上安装 VM 代理：Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 如果 VM 是根据 Azure 市场映像创建的，则代理将安装并运行。 如果创建自定义 VM，或迁移本地计算机，则可能需要[手动安装代理](#install-the-vm-agent)。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>修改存储复制

默认情况下，保管库使用[异地冗余存储 (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)。

* 如果保管库是你的主要备份机制，则建议使用 GRS。
* 可使用[本地冗余存储 (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) 来降低费用。
* [区域冗余存储 (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) 在[可用性区域](../availability-zones/az-overview.md#availability-zones)复制数据，从而确保同一区域中的数据驻留和复原能力。

按如下所述修改存储复制类型：

1. 在新保管库的“设置”部分，选择“属性” 。
2. 在“属性”中的“备份配置”下，选择“更新”。  
3. 选择存储复制类型，然后选择“保存”。

      ![设置新保管库的存储配置](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > 保管库已设置并包含备份项后，无法修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。

## <a name="apply-a-backup-policy"></a>应用备份策略

若要将备份策略应用于 Azure VM，请执行以下步骤：

1. 导航到备份中心，在“概述”选项卡中单击“+备份”。 

   ![“备份”按钮](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. 选择“Azure 虚拟机”作为“数据源类型”，然后选择已创建的保管库。 然后单击“继续”。

   ![“备份”和“备份目标”窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. 分配备份策略。

    - 默认策略每天备份一次 VM。 每日备份保留 30 天。 即时恢复快照保留两天。

      ![默认备份策略](./media/backup-azure-arm-vms-prepare/default-policy.png)

    - 如果不想使用默认策略，请选择“新建”，然后按照下一过程中所述创建自定义策略。

1. 在“虚拟机”下，选择“添加”。

      ![添加虚拟机](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. 将打开“选择虚拟机”窗格。 选择要使用策略进行备份的 VM。 然后选择“确定”。 

   * 随后将验证选定的 VM。
   * 只能选择与保管库位于同一区域中的 VM。
   * 只能在单个保管库中备份 VM。

     ![“选择虚拟机”窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > 与保管库位于同一区域和订阅中的所有 VM 都可用于配置备份。 配置备份时，你可浏览到虚拟机名称及其资源组，即使你对这些 VM 没有所需的权限也是如此。 如果 VM 处于软删除状态，则不显示在此列表中。 如果需要重新保护 VM，则需要等到软删除期限到期或从软删除列表中撤销删除该 VM。 有关详细信息，请参阅 [VM 的软删除](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal)一文。

1. 在“备份”中，选择“启用备份”。  这会将策略部署到保管库和 VM，并在 Azure VM 上运行的 VM 代理中安装备份扩展。

启用备份后：

* 无论 VM 是否在运行，备份服务都会安装备份扩展。
* 初始备份将根据备份计划运行。
* 运行备份时，请注意：
  * 正在运行的 VM 最有可能捕获应用程序一致性恢复点。
  * 但是，即使 VM 已关闭，也会对其进行备份。 此类 VM 称为脱机 VM。 在这种情况下，恢复点将是崩溃一致性恢复点。
* 不需要显式出站连接即可备份 Azure VM。

### <a name="create-a-custom-policy"></a>创建自定义策略

如果选择了创建新备份策略，请填写策略设置。

1. 在“策略名称”中，指定一个有意义的名称。
2. 在“备份计划”中，指定应何时进行备份。 可以对 Azure VM 执行每日或每周备份。
3. 在“即时还原”中，指定要在本地保留快照以进行即时还原的时间长度。
    * 还原时，已备份的 VM 磁盘将通过网络从存储复制到恢复存储位置。 使用即时还原时，可利用在执行备份作业期间创建的存储于本地的快照，无需等到将备份数据传输到保管库。
    * 可以将快照保留 1 到 5 天以进行即时还原。 默认设置是 2 天。
4. 在“保留范围”中，指定要保留每日或每周备份点的时间长度。
5. 在“每月备份点的保留”和“每年备份点的保留”中，指定是否要保留每日或每周备份的每月或每年备份 。
6. 选择“确定”来保存策略。
    > [!NOTE]
    > 为了存储还原点集合 (RPC)，备份服务将创建一个单独的资源组 (RG)。 此 RG 不同于 VM 的 RG。 [了解详细信息](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)。

    ![新建备份策略](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 备份不支持根据 Azure VM 备份的夏令时时差自动调整时钟。 随着时间的推移，根据需要手动修改备份策略。

## <a name="trigger-the-initial-backup"></a>触发初始备份

初始备份将根据计划运行，但可以按如下所示立即运行：

1. 导航到备份中心，然后选择“备份实例”菜单项。
1. 选择“Azure 虚拟机”作为“数据源类型”。 然后搜索已配置的用于备份的 VM。
1. 右键单击相关行或选择“更多”图标 (...)，然后单击“立即备份”。
1. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然后选择“确定”。 
1. 监视门户通知。
   若要监视作业进度，请转到“备份中心” > “备份作业”，通过筛选列表来查找处于“正在进行中”状态的作业。  
   创建初始备份可能需要一些时间，具体取决于 VM 的大小。

## <a name="verify-backup-job-status"></a>验证备份作业状态

每个 VM 备份的备份作业详细信息包括两个阶段，先是“快照”阶段，然后是“将数据传输到保管库”阶段 。<br/>
“快照”阶段可保证与磁盘一起存储的恢复点的可用性以进行即时还原，并且最多可以使用 5 天，具体取决于用户配置的快照保留期。 “将数据传输到保管库”阶段会在保管库中创建恢复点以实现长期保留。 仅在“快照”阶段完成后，才会开始“将数据传输到保管库”阶段。

  ![备份作业状态](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

有两个子任务在后端运行，一个用于前端备份作业，可按如下所示从“备份作业”详细信息窗格中将其选中 ：

  ![备份作业状态子任务](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

“将数据传输到保管库”阶段可能需要数天才能完成，具体取决于磁盘的大小、每个磁盘的变动率和若干其他因素。

作业状态可能因以下情况而有所不同：

**快照** | 将数据传输到保管库 | **作业状态**
--- | --- | ---
已完成 | 正在进行 | 正在进行
已完成 | 已跳过 | 已完成
已完成 | 已完成 | 已完成
已完成 | 失败 | 已完成，但出现警告
失败 | 失败 | 失败

现在借助此功能，对于同一 VM，两个备份可以并行运行，但在任一阶段（快照、将数据传输到保管库）中，只有一个子任务可以运行。 因此，借助此分离功能，将避免正在进行的备份作业导致第二天的备份失败的情况。 如果之前某天的备份作业处于“正在进行”状态，则随后几天的备份可在跳过“将数据传输到保管库”后完成快照。
在保管库中创建的增量恢复点将捕获在保管库中创建的最新恢复点的所有变动。 不会对用户产生任何成本影响。

## <a name="optional-steps"></a>可选步骤

### <a name="install-the-vm-agent"></a>安装 VM 代理

Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 如果 VM 是根据 Azure 市场映像创建的，则代理将安装并运行。 如果创建自定义 VM，或迁移本地计算机，则可能需要手动安装代理，如下表中所示。

**VM** | **详细信息**
--- | ---
**Windows** | 1.[下载并安装](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理 MSI 文件。<br/><br/> 2.使用管理员权限在计算机上进行安装。<br/><br/> 3.验证安装。 在 VM 上的 C:\WindowsAzure\Packages 中，右键单击“WaAppAgent.exe” > “属性” 。 在“详细信息”选项卡上，“产品版本”应为 2.6.1198.718 或更高。 <br/><br/> 如果要更新代理，请确保没有备份操作正在运行，并[重新安装代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 使用发行版的包存储库中的 RPM 或 DEB 包进行安装。 这是安装和升级 Azure Linux 代理的首选方法。 所有[认可的分发版提供商](../virtual-machines/linux/endorsed-distros.md)会将 Azure Linux 代理包集成到其映像和存储库。 [GitHub](https://github.com/Azure/WALinuxAgent) 上提供了该代理，但我们不建议从此处安装。<br/><br/> 如果要更新代理，请确保没有备份操作正在运行，并更新二进制文件。

## <a name="next-steps"></a>后续步骤

* 排查 [Azure VM 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 备份](backup-azure-vms-troubleshoot.md)出现的任何问题。
* [还原](backup-azure-arm-restore-vms.md) Azure VM。
