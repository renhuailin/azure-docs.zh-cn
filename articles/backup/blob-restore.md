---
title: 还原 Azure Blob
description: 了解如何还原 Azure Blob。
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: aaca425e2ea13948fbd52bf60ed30d64c29c27d3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108767268"
---
# <a name="restore-azure-blobs"></a>还原 Azure Blob

在配置了操作备份的存储帐户中，块 blob 可以还原到保持期内的任何时间点。 此外，还可以将还原的范围限定为存储帐户中的所有块 blob 或 blob 的子集。

## <a name="before-you-start"></a>开始之前

- Blob 将还原到相同的存储帐户。 因此，在还原后发生了更改的 blob 将被覆盖。
- 在还原操作过程中，只能还原标准常规用途 v2 存储帐户中的块 blob。 追加 blob、页 blob 和高级块 blob 不会还原。
- 还原作业正在进行时，无法读取或写入存储中的 blob。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围内包含具有活动租约的 blob，则还原操作将自动失败。 在开始还原操作之前中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 只有基本 blob 还原到其以前的状态。
- 如果通过调用“删除容器”操作从存储帐户中删除了容器，将无法使用还原操作来还原该容器。 如果要在以后还原它们，请删除单个 blob，而不是删除整个容器。 此外，Microsoft 还建议为容器启用软删除，以及执行操作备份，以防止意外删除容器。
- 有关所有限制和支持的方案，请参阅[支持矩阵](blob-backup-support-matrix.md)。

## <a name="restore-blobs"></a>还原 blob

可以通过备份中心启动还原。

1. 在备份中心，在顶部栏中转到“还原”。

    ![在备份中心还原](./media/blob-restore/backup-center-restore.png)

1. 在“启动还原”选项卡中，选择“Azure Blob（Azure 存储）”作为“数据源类型”，然后选择要还原的“备份实例”。 此处的备份实例是包含要还原的 blob 的存储帐户。

     ![选择备份实例](./media/blob-restore/select-backup-instance.png)

1. 在“选择恢复点”选项卡中，选择要恢复数据的日期和时间。 还可以使用滑块来选择要从其还原的时间点。 日期旁边的信息气泡显示了可还原数据的有效持续时间。 Blob 的操作备份是连续备份，使你可以精细控制要恢复数据的点。

    >[!NOTE]
    > 此处描述的时间是本地时间。

    ![还原的日期和时间](./media/blob-restore/date-and-time.png)

1. 在“还原参数”选项卡中，选择是要还原存储帐户中的所有 blob、特定容器，还是使用前缀匹配还原 blob 的子集。 使用前缀匹配时，可指定最多 10 个前缀或 filepaths 范围。 [此处](#use-prefix-match-for-restoring-blobs)提供了有关使用前缀匹配的更多详细信息。

    ![还原参数](./media/blob-restore/restore-parameters.png)

    选择以下选项之一：

    - **还原存储帐户中的所有 blob**：如果使用此选项，则会通过将存储帐户中的所有块 blob 回滚到选定的时间点来还原这些 blob。 包含大量数据或见证高流失率的存储帐户可能需要更长的时间才能还原。

    - **浏览和还原选定的容器**：使用此选项可浏览并选择最多 10 个要还原的容器。 必须具有足够的权限才能查看存储帐户中的容器，否则可能无法看到存储帐户的内容。

    - **选择要使用前缀匹配还原的 blob**：此选项允许你使用前缀匹配来还原 blob 的子集。 最多可以在一个容器内或跨多个容器指定 10 个字典范围的 blob，以在给定时间点将这些 blob 恢复到它们以前的状态。 需谨记以下几点：

        - 可以使用正斜杠 (/) 来分隔容器名称和 blob 前缀
        - 指定的范围的开始包括在内，但是不包括指定的范围。

    有关使用前缀还原 blob 范围的详细信息，请参阅[本部分](#use-prefix-match-for-restoring-blobs)。

1. 完成指定要还原的 blob 后，继续转到“审阅并还原”选项卡，然后选择“还原”以启动还原。

1. **跟踪还原**：使用“备份作业”视图跟踪还原的详细信息和状态。 为此，请导航到“备份中心” > “备份作业”。 执行还原时，状态将显示为“正在处理”。

    ![“备份作业”选项卡](./media/blob-restore/backup-jobs.png)

    还原操作成功完成后，状态将更改为“已完成”。 还原成功完成后，你将能够在存储帐户中再次读取和写入 blob。

## <a name="additional-topics"></a>其他主题

### <a name="use-prefix-match-for-restoring-blobs"></a>使用前缀匹配来还原 blob

请看下面的示例：

![用前缀匹配进行还原](./media/blob-restore/prefix-match.png)

图中显示的还原操作执行以下操作：

- 还原 container1 的完整内容。
- 还原 container2 中 blob1 至 blob5 的字典范围中的 blob。   此范围将还原名称为 blob1、blob11、blob100、blob2 等的 blob。 由于不包括范围的末尾，因此会还原名称以 blob4 开头的 blob，但不会还原名称以 blob5 开头的 blob。
- 还原 container3 和 container4中的所有 blob。 因为不包括范围的末尾，所以此范围不会还原 container5。

## <a name="next-steps"></a>后续步骤

- [Azure Blob 操作备份概述](blob-backup-overview.md)
