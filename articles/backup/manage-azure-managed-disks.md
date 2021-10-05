---
title: 管理 Azure 托管磁盘
description: 了解如何从 Azure 门户管理 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 4b40a71a71ce543c66bf347ec745fea6ae384779
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708891"
---
# <a name="manage-azure-managed-disks"></a>管理 Azure 托管磁盘

本文介绍如何从 Azure 门户管理 Azure 托管磁盘。

## <a name="stop-protection-preview"></a>停止保护（预览版）


可通过三种方式停止保护 Azure 磁盘：

- 停止保护并保留数据（永久保留）：此选项可帮助你阻止将来的所有备份作业保护你的磁盘。 但是，Azure 备份服务将永久保留已备份的恢复点。 你需要付费才能将恢复点保留在保管库中（有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 你可以根据需要还原磁盘。 若要恢复磁盘保护，请使用“恢复备份”选项。

- 停止保护并保留数据（按策略保留）：此选项可帮助你阻止将来的所有备份作业保护你的磁盘。 恢复点将按策略保留，并可根据 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)收费。 但是，最新的恢复点将永久保留。

- 停止保护并删除数据：此选项可帮助你阻止将来的所有备份作业保护你的磁盘，并删除所有恢复点。 你将无法还原磁盘，也无法使用“恢复备份”选项。

### <a name="stop-protection-and-retain-data"></a>停止保护并保留数据

1. 转到“备份中心”并选择“Azure 磁盘” 。

1. 从磁盘备份实例列表中，选择要保留的实例。

1. 选择“停止备份(预览版)”。

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="显示选择要停止的 Azure 磁盘备份实例的屏幕截图。" lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. 选择以下数据保留选项之一：

   1. 永久保留
   1. 按策略保留
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="显示用于停止磁盘备份实例保护的选项的屏幕截图。" lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   还可以从下拉列表中选择停止备份的原因。

1. 单击“停止备份”。

1. 选择“确认”以停止数据保护。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="显示要选择的磁盘备份实例保留选项的屏幕截图。" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

1. 转到“备份中心”并选择“Azure 磁盘” 。

1. 从磁盘备份实例列表中，选择要删除的实例。

1. 单击“停止备份(预览版)”。

1. 选择“删除备份数据”。

   提供备份实例的名称、删除数据的原因和任何其他备注。

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="确认停止磁盘备份的屏幕截图。" lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. 选择“停止备份”。

1. 选择“确认”以停止数据保护。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="显示要选择的磁盘备份实例保留选项的屏幕截图。" lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>恢复保护

如果选择了“停止保护并保留数据”选项，则可以恢复磁盘保护。

>[!Note]
>在开始保护磁盘时，备份策略也会应用于保留的数据。 如果按照策略，恢复点已过期，则会清理这些已过期的恢复点。

请使用以下步骤：

1. 转到“备份中心”并选择“Azure 磁盘” 。

1. 从磁盘备份实例列表中，选择要恢复的实例。

1. 选择“恢复备份(预览版)”。

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="显示用于恢复磁盘保护的选项的屏幕截图。" lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. 选择“恢复备份”。

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="显示用于恢复磁盘备份的选项的屏幕截图。" lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>删除备份实例

如果你选择停止所有计划的备份作业并删除所有现有备份，请使用“删除备份实例”。

>[!Note]
>如果手动删除了快照资源组或者撤销了对备份保管库托管标识的权限，则删除备份实例将会失败。 如果发生这种失败，请临时创建同名的快照资源组，并按照[此文档](/azure/backup/backup-managed-disks-ps#assign-permissions)中所述，为备份保管库的托管标识提供所需的角色权限。 可以在“备份实例”屏幕的“基本信息”选项卡上找到快照资源组的名称 。 

若要删除磁盘备份实例，请执行以下步骤：

1. 在备份实例屏幕上单击“删除”。

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="显示备份实例删除过程的屏幕截图。" lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. 提供确认详细信息，包括备份实例的名称、删除实例的原因和其他备注。

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="显示确认删除备份实例的屏幕截图。" lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. 单击“删除”进行确认并继续删除备份实例。

## <a name="next-steps"></a>后续步骤

[备份保管库概述](backup-vault-overview.md)
