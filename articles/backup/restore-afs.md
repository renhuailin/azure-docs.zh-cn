---
title: 还原 Azure 文件共享
description: 了解如何使用 Azure 门户从 Azure 备份所创建的还原点还原整个文件共享或特定文件。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376768"
---
# <a name="restore-azure-file-shares"></a>还原 Azure 文件共享

本文介绍如何使用 Azure 门户从 [Azure 备份](./backup-overview.md)所创建的还原点还原整个文件共享或特定文件。

本文将指导如何进行以下操作：

* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。
* 跟踪还原操作状态。

## <a name="steps-to-perform-a-restore-operation"></a>执行还原操作的步骤

若要执行还原操作，请执行下列步骤。

### <a name="select-the-file-share-to-restore"></a>选择要还原的文件共享

1. 在 [Azure 门户](https://portal.azure.com/)中，打开用于为文件共享配置备份的恢复服务保管库。

1. 在“概述”窗格的“受保护的项”部分，选择“备份项” 。

    ![选择“备份项”](./media/restore-afs/backup-items.png)

1. 选择“备份项”后，“概述”窗格旁边会显示一个新窗格，其中列出了所有备份管理类型。

    ![备份管理类型](./media/restore-afs/backup-management.png)

1. 在“备份项”窗格的“备份管理类型”下，选择“Azure 存储 (Azure 文件存储)”  。 你将看到使用此保管库备份的所有文件共享及其相应存储帐户的列表。

    ![所有文件共享列表](./media/restore-afs/file-shares.png)

1. 在 Azure 文件共享列表中，选择要为其执行还原操作的文件共享。

### <a name="full-share-recovery"></a>完全共享恢复

可以使用此还原选项在原始位置或备用位置还原完整的文件共享。

1. 在[选择要还原的文件共享](#select-the-file-share-to-restore)部分的步骤 5 中选择要还原的文件之后，在“备份项”窗格中选择显示的“还原共享”选项 。

   ![选择“还原共享”](./media/restore-afs/restore-share.png)

1. 选择“还原共享”之后，“还原”窗格随即打开 。 若要选择用于执行还原操作的还原点，请选择“还原点”文本框下方的“选择”链接文本 。

    ![通过选择“选择”来选择还原点](./media/restore-afs/select-restore-point.png)

1. 随后右侧会打开“选择还原点”文本窗格，其中列出可用于所选文件共享的还原点。 选择要用于执行还原操作的还原点，然后选择“确定”。

    ![选择还原点](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >默认情况下，“选择还原点”窗格会列出过去 30 天的还原点。 如果要查看特定时间段内创建的还原点，请通过选择相应的“开始时间”和“结束时间”来指定范围，然后选择“刷新”按钮  。

1. 下一步是选择“还原位置”。 在“恢复目标”部分，指定还原数据的位置或方式。 使用切换按钮选择以下两个选项之一：

    * **原始位置**：将完整的文件共享还原到与原始源相同的位置。
    * **备用位置**：将完整的文件共享还原到备用位置，并将原始文件共享保持原样。

#### <a name="restore-to-the-original-location-full-share-recovery"></a>还原到原始位置（完全共享恢复）

1. 选择“原始位置”作为“恢复目标”，然后通过在“存在冲突时”下拉列表中选择合适的选项，选择在出现冲突时是跳过还是覆盖  。

1. 选择“还原”以启动还原操作。

    ![选择“还原”以启动操作](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>还原到备用位置（完全共享恢复）

1. 选择“备用位置”作为“恢复目标” 。
1. 在“存储帐户”下拉列表中选择要在其中还原备份内容的目标存储帐户。
1. “选择文件共享”下拉列表显示在步骤 2 中所选的存储帐户中存在的文件共享。 选择要在其中还原备份内容的文件共享。
1. 在“文件夹名称”框中，指定要在包含已还原内容的目标文件共享中创建的文件夹名称。
1. 选择在出现冲突时是跳过还是覆盖。
1. 在所有框中输入相应的值后，选择“还原”以启动还原操作。

    ![选择“备用位置”](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>项目级恢复

可以使用此还原选项来还原原始位置或备用位置中的单个文件或文件夹。

1. 在[选择要还原的文件共享](#select-the-file-share-to-restore)部分的步骤 5 中选择要还原的文件之后，在“备份项”窗格中选择显示的“文件恢复”选项 。

    ![选择“文件恢复”](./media/restore-afs/file-recovery.png)

1. 选择“文件恢复”之后，“还原”窗格随即打开 。 若要选择用于执行还原操作的还原点，请选择“还原点”文本框下方的“选择”链接文本 。

    ![通过选择“选择”链接来选择还原点](./media/restore-afs/select-restore-point.png)

1. 随后右侧会打开“选择还原点”文本窗格，其中列出可用于所选文件共享的还原点。 选择要用于执行还原操作的还原点，然后选择“确定”。

    ![选择还原点](./media/restore-afs/restore-point.png)

1. 下一步是选择“还原位置”。 在“恢复目标”部分，指定还原数据的位置或方式。 使用切换按钮选择以下两个选项之一：

    * **原始位置**：将所选文件或文件夹还原到与原始源相同的文件共享。
    * **备用位置**：将所选文件或文件夹还原到备用位置，并将原始文件共享内容保持原样。

#### <a name="restore-to-the-original-location-item-level-recovery"></a>还原到原始位置（项目级恢复）

1. 选择“原始位置”作为“恢复目标”，然后通过在“存在冲突时”下拉列表中选择合适的选项，选择在出现冲突时是跳过还是覆盖  。

    ![针对项目级恢复的原始位置](./media/restore-afs/original-location-item-level.png)

1. 若要选择要还原的文件或文件夹，请选择“添加文件”按钮。 随即会在右侧打开一个上下文窗格，其中显示所选的要还原的文件共享还原点的内容。

    ![选择“添加文件”](./media/restore-afs/add-file.png)

1. 选中要还原的文件或文件夹所对应的复选框，然后选择“选择”。

    ![选择文件或文件夹](./media/restore-afs/select-file-folder.png)

1. 重复步骤 2 到 4，选择多个要还原的文件或文件夹。
1. 选择所有要还原的项后，选择“还原”以启动还原操作。

    ![选择“还原”以启动操作](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>还原到备用位置（项目级恢复）

1. 选择“备用位置”作为“恢复目标” 。
1. 在“存储帐户”下拉列表中选择要在其中还原备份内容的目标存储帐户。
1. “选择文件共享”下拉列表显示在步骤 2 中所选的存储帐户中存在的文件共享。 选择要在其中还原备份内容的文件共享。
1. 在“文件夹名称”框中，指定要在包含已还原内容的目标文件共享中创建的文件夹名称。
1. 选择在出现冲突时是跳过还是覆盖。
1. 若要选择要还原的文件或文件夹，请选择“添加文件”按钮。 随即会在右侧打开一个上下文窗格，其中显示所选的要还原的文件共享还原点的内容。

    ![选择要还原到备用位置的项](./media/restore-afs/restore-to-alternate-location.png)

1. 选中要还原的文件或文件夹所对应的复选框，然后选择“选择”。

    ![选择恢复目标](./media/restore-afs/recovery-destination.png)

1. 重复步骤 6 到 8，选择多个要还原的文件或文件夹。
1. 选择所有要还原的项后，选择“还原”以启动还原操作。

    ![选择所有文件后选择“确定”](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>跟踪还原操作

触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 若要查看针对作业的操作，请选择通知超链接。

![选择通知超链接](./media/restore-afs/notifications-link.png)

还可以在恢复服务保管库中监视还原进度：

1. 从触发还原操作的位置打开恢复服务保管库。
1. 在“概述”窗格中，选择“监视”部分下的“备份作业”，以查看针对不同工作负载运行的操作的状态 。

    ![选择“备份作业”](./media/restore-afs/backup-jobs.png)

1. 选择文件共享所对应的工作负载名称，以查看有关还原操作的更多详细信息，例如传输的数据和还原的文件数 。

    ![查看还原详细信息](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[管理 Azure 文件共享备份](manage-afs-backup.md)。
