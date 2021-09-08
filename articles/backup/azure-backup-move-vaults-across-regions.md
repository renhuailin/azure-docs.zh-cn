---
title: 将 Azure 恢复服务保管库移到另一个区域
description: 本文介绍如何在跨区域移动资源后确保继续备份。
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: faf6ad49234d9753a0479151bf931714a1eba226
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113633"
---
# <a name="back-up-resources-in-recovery-services-vault-after-moving-across-regions"></a>在跨区域移动后备份恢复服务保管库中的资源

Azure 资源转移器支持跨区域移动多个资源。 将资源从一个区域移到另一个区域时，可以确保资源始终处于受保护状态。 由于 Azure 备份支持保护多个工作负载，你可能需要执行几个步骤才能在新区域中保持相同的保护级别。

若要了解实现此目的的详细步骤，请参阅以下各部分。

>[!Note]
>Azure 备份目前不支持将备份数据从一个恢复服务保管库移到另一个恢复服务保管库。 若要保护新区域中的资源，资源需要注册并备份到新区域中新的/现有的保管库。 将资源从一个区域移到另一个区域时，可以根据要求保留/删除旧区域中现有恢复服务保管库内的备份数据。 如果选择保留旧保管库中的数据，将会相应地产生备份费用。

## <a name="back-up-azure-virtual-machine-after-moving-across-regions"></a>跨区域移动后备份 Azure 虚拟机

将受恢复服务保管库保护的 Azure 虚拟机 (VM) 从一个区域移到另一个区域后，不再可以将它备份到旧保管库。 旧保管库中的备份将开始失败并出现错误“BCMV2VMNotFound”或“[ResourceNotFound](/azure/backup/backup-azure-vms-troubleshoot#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)” 。

若要保护新区域中的 VM，应执行以下步骤：

1. 在移动 VM 之前，[在现有保管库仪表板的“备份项”选项卡上选择该 VM](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud)，然后选择“**停止保护**”并根据要求保留/删除数据。 如果停止 VM 的数据备份并保留数据，恢复点将永久保留且不遵守任何策略。 这可以确保备份数据始终可用于还原。

   >[!Note]
   >保留旧保管库中的数据会产生备份费用。 如果你不再想要保留数据以免产生费用，需要使用[“删除数据”选项](/azure/backup/backup-azure-manage-vms#delete-backup-data)删除保留的备份数据。

1. 使用 [Azure 资源转移器](/azure/resource-mover/tutorial-move-region-virtual-machines)将 VM 移到新区域。

1. 开始保护新区域中新的或现有恢复服务保管库内的 VM。
   需要从旧备份还原时，如果先前已选择保留备份数据，则仍可以从旧恢复服务保管库进行还原。 

上述步骤还有助于确保在新区域中备份资源。

## <a name="back-up-azure-file-share-after-moving-across-regions"></a>跨区域移动后备份 Azure 文件共享

若要将存储帐户连同其中的文件共享从一个区域移到另一个区域，请参阅[将 Azure 存储帐户移到另一个区域](/azure/storage/common/storage-account-move)。

>[!Note]
>跨区域复制 Azure 文件共享时，其关联的快照不会随之移动。 若要将快照数据移到新区域，需要使用 [AzCopy](/azure/storage/common/storage-use-azcopy-files#copy-all-file-shares-directories-and-files-to-another-storage-account) 将快照的各个文件和目录移到新区域中的存储帐户。

Azure 备份现在为 Azure 文件存储提供了[快照管理解决方案](/azure/backup/backup-afs#discover-file-shares-and-configure-backup)。 这意味着，文件共享数据不会移到恢复服务保管库。 此外，由于快照不会随存储帐户一起移动，因此，所有备份（快照）实际上只会保留在现有区域中并受现有保管库的保护。 但是，可以执行以下步骤，确保在新区域中创建的新文件共享受 Azure 备份的保护：

1. 开始保护已复制到新区域中新的或现有恢复服务保管库的新存储帐户中的 Azure 文件共享。  

1. 将 Azure 文件共享复制到新区域后，可以根据需要选择停止保护并保留/删除原始 Azure 文件共享的快照（和相应的恢复点）。 为此，可以在原始保管库仪表板的[“备份项”选项卡](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud)上选择你的文件共享。 如果停止 Azure 文件共享的数据备份并保留数据，恢复点将永久保留且不遵守任何策略。
   
   这可以确保快照始终可用于从旧保管库还原。 
 
## <a name="back-up-sql-server-in-azure-vmsap-hana-in-azure-vm"></a>在 Azure VM/Azure VM 中的 SAP HANA 中备份 SQL Server

将运行 SQL 或 SAP HANA 服务器的 VM 移到另一区域后，这些 VM 中的 SQL 和 SAP HANA 数据库不再可以备份到源区域的保管库中。 若要保护在新区域中的 Azure VM 上运行的 SQL 和 SAP HANA 服务器，请执行以下步骤：
 
1. 在将运行 SQL Server/SAP HANA 的 VM 移到新区域之前，在现有保管库仪表板的[“备份项目”选项卡](/azure/backup/backup-azure-delete-vault#delete-protected-items-in-the-cloud)中选择该 VM，然后选择需要为其停止备份的数据库。 选择“停止保护”，然后根据需要保留/删除数据。 如果停止数据备份并保留数据，恢复点将永久保留且不遵守任何策略。 这可以确保备份数据始终可用于还原。

   >[!Note]
   >保留旧保管库中的数据会产生备份费用。 如果你不再想要保留数据以免产生费用，需要使用[“删除数据”选项](/azure/backup/backup-azure-manage-vms#delete-backup-data)删除保留的备份数据。

1. 使用 [Azure 资源转移器](/azure/resource-mover/tutorial-move-region-virtual-machines)将运行 SQL Server/SAP HANA 的 VM 移到新区域。

1. 开始保护新区域中新的/现有恢复服务保管库内的 VM。 需要从旧备份还原时，仍可以从旧恢复服务保管库进行还原。
 
上述步骤还有助于确保在新区域中备份资源。