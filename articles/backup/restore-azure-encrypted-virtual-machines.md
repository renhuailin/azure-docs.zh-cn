---
title: 还原加密的 Azure VM
description: 介绍如何使用 Azure 备份服务还原加密的 Azure VM。
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 95d665fb2ae2e1dc0427090842cddbecfb3c3706
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187797"
---
# <a name="restore-encrypted-azure-virtual-machines"></a>还原加密的 Azure 虚拟机

本文介绍如何使用 [Azure 备份](backup-overview.md)服务还原包含加密的磁盘的 Windows 或 Linux Azure 虚拟机 (VM)。 有关详细信息，请参阅 [Azure VM 备份的加密](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups)。


## <a name="before-you-start"></a>开始之前

在开始还原加密的 VM 之前，请查看已知限制

- 可以备份和还原同一订阅中的 ADE 加密的 VM。
- Azure 备份支持使用独立密钥加密的 VM。 目前不支持属于用于加密 VM 的证书的任何密钥。
- 无法在文件/文件夹级别恢复 ADE 加密的 VM。 需要恢复整个 VM 才能还原文件和文件夹。
- 还原 VM 时，无法对 ADE 加密的 VM 使用[替换现有 VM](backup-azure-arm-restore-vms.md#restore-options) 选项。 只有未加密的托管磁盘才支持此选项。


## <a name="restore-an-encrypted-vm"></a>还原已加密的 VM

仅可通过还原 VM 磁盘和创建虚拟机实例来还原加密的 VM，如下所述。 目前不支持替换现有 VM 上的现有磁盘、从还原点创建 VM 以及文件或文件夹级别的还原。
 
请按照以下步骤还原加密的 VM：

### <a name="step-1-restore-the-vm-disk"></a>步骤 1：还原 VM 磁盘

1. 在“还原配置” > “新建 > 还原类型”中，选择“还原磁盘”   。
1. 在“资源组”中，为已还原的磁盘选择现有资源组，或者创建具有全局唯一名称的新资源组。
1. 在“暂存位置”中，指定应将 VHD 复制到其中的存储帐户。 [了解详细信息](backup-azure-arm-restore-vms.md#storage-accounts)。

    ![选择“资源组”和“暂存位置”](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. 选择“还原”来触发还原操作。

当虚拟机使用托管磁盘，而你选择“创建虚拟机”选项时，Azure 备份不使用指定的存储帐户。 在使用“还原磁盘”和“即时还原”时，存储帐户仅用于存储模板。 在指定的资源组中创建了托管磁盘。
当虚拟机使用非托管磁盘时，它们会以 Blob 的形式还原到存储帐户。

   > [!NOTE]
   > 还原 VM 磁盘后，可以手动将原始 VM 的 OS 磁盘与已还原的 VM 磁盘进行交换，而无需重新创建它。 [了解详细信息](https://azure.microsoft.com/blog/os-disk-swap-managed-disks/)。

### <a name="step-2-recreate-the-virtual-machine-instance"></a>步骤 2：重新创建虚拟机实例 

执行下列操作之一：

- 使用执行还原操作期间生成的模板来自定义 VM 设置并触发 VM 部署。 [了解详细信息](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
  >[!NOTE]
   >部署模板时，请验证存储帐户容器和公共/专用设置。
- 使用 PowerShell 从已还原的磁盘创建新的 VM。 [了解详细信息](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

### <a name="step-3-restore-an-encrypted-linux-vm"></a>步骤 3：还原加密的 Linux VM

请重新安装 ADE 扩展，使数据磁盘处于已打开和已装载状态。

## <a name="cross-region-restore-for-an-encrypted-azure-vm"></a>加密的 Azure VM 的跨区域还原

Azure 备份支持将加密的 Azure VM 跨区域还原到 [Azure 配对区域](../best-practices-availability-paired-regions.md)。 了解如何为加密的 VM [启用跨区域还原](backup-create-rs-vault.md#configure-cross-region-restore)。

## <a name="move-an-encrypted-azure-vm"></a>移动加密的 Azure VM

跨保管库或资源组移动加密的 VM 与移动备份的 Azure 虚拟机是相同的。 请参阅：

- [将 Azure 虚拟机移动到其他恢复服务保管库的步骤](backup-azure-move-recovery-services-vault.md#move-an-azure-virtual-machine-to-a-different-recovery-service-vault)
- [将 Azure 虚拟机移动到其他资源组或订阅的步骤](../azure-resource-manager/management/move-resource-group-and-subscription.md)


## <a name="next-steps"></a>后续步骤

如果遇到任何问题，请查看以下文章：

- 备份和还原已加密的 Azure VM 时出现的[常见错误](backup-azure-vms-troubleshoot.md)。
- [Azure VM 代理/备份扩展](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)问题。



