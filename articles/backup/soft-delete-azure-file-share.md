---
title: 针对 Azure 文件共享的意外删除保护
description: 了解如何通过软删除来防止 Azure 文件共享的意外删除。
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179906"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>使用 Azure 备份对 Azure 文件共享进行意外删除保护

为了提供对网络攻击或意外删除的保护，在为相应的存储帐户中的任何文件共享配置备份时，会对存储帐户中的所有文件共享启用[软删除](../storage/files/storage-files-prevent-file-share-deletion.md)。 使用软删除时，即使恶意行动者删除文件共享，文件共享的内容和恢复点（快照）至少会保留 14 天的时间，从而支持在不丢失数据的情况下恢复文件共享。  标准存储帐户和高级存储帐户支持软删除，Azure 备份会为托管备份文件共享的所有存储帐户启用软删除。

以下流程图显示了在为存储帐户中的文件共享启用软删除时不同步骤和备份项的状态：

 ![软删除流程图](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>常见问题

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>何时将对存储帐户中的文件共享启用软删除？

当你首次为存储帐户中的任何文件共享配置备份时，Azure 备份服务会对相应存储帐户中的所有文件共享启用软删除。

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>我是否可以配置在我删除文件共享后快照和恢复点将被保留在软删除状态的天数？

是的，你可以根据需求设置保留期。 [本文档](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal)介绍配置保留期的步骤。 对于具有已备份文件共享的存储帐户，保留期设置最少应为 14 天。

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>如果我已将其配置为小于 14 天，Azure 备份是否会重置保留设置？

从安全角度来看，我们建议使用已备份文件共享的存储帐户至少保留 14 天。 因此，在每个备份作业运行时，如果 Azure 备份发现该设置少于 14 天，则会将其重置为 14 天。

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>在保留期内产生的成本是多少？

在软删除期间，受保护的实例成本和快照存储成本将保持不变。  此外，还将按标准文件共享的标准费率以及针对高级文件共享的快照存储费率向用户收费。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

首先需要撤消删除软删除的文件共享才能执行还原操作。 撤消删除操作将使文件共享进入已备份状态，你可以在这种状态下恢复到任何时间点。 若要了解如何撤消删除文件共享，请访问[此链接](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)或查看[删除文件共享脚本](./scripts/backup-powershell-script-undelete-file-share.md)。

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>如何清除至少具有一个受保护文件共享的存储帐户中的文件共享的数据？

如果在存储帐户中至少有一个受保护的文件共享，这意味着会为该帐户中的所有文件共享启用软删除，并且你的数据将在删除操作完成后保留 14 天。 但是，如果想要立即清除数据，而不希望保留数据，请执行以下步骤：

1. 如果已在启用软删除的情况下删除了文件共享，请先从[文件门户](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)或使用[撤消删除文件共享脚本](./scripts/backup-powershell-script-undelete-file-share.md)删除文件共享。
2. 按照[本文档](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)中所述的步骤操作，对存储帐户中的文件共享禁用软删除。
3. 现在删除要立即清除其内容的文件共享。

>[!NOTE]
>在对存储帐户中受保护的文件共享运行下一个计划的备份作业之前，应执行步骤 2。 因为当备份作业运行时，它将为存储帐户中的所有文件共享重新启用软删除。

>[!WARNING]
>在步骤 2 中禁用软删除后，对文件共享执行的任何删除操作都是永久删除操作。 因此，如果在禁用软删除后意外删除了备份文件共享，则会丢失所有快照，并且无法恢复数据。

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>在文件共享的软删除设置上下文中，当我注销存储帐户时，Azure 备份会进行哪些更改？

注销时，Azure 备份会检查文件共享的保留期设置，如果超过 14 天或少于 14 天，则保持不变。 但是，如果保留期为 14 天，则会将其视为由 Azure 备份启用，因此我们在注销过程中禁用软删除。 如果要在保持保留设置不变时注销存储帐户，请在完成注销后从存储帐户窗格中再次启用它。 有关配置步骤，请参阅[此链接](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)。

## <a name="next-steps"></a>后续步骤

了解如何[从 Azure 门户备份 Azure 文件共享](backup-afs.md)
