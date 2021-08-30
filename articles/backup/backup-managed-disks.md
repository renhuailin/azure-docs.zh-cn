---
title: 备份 Azure 托管磁盘
description: 了解如何从 Azure 门户备份 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: 9bbb29aa4741f71448876a0af947964f728b0fb0
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300729"
---
# <a name="back-up-azure-managed-disks"></a>备份 Azure 托管磁盘

本文介绍如何从 Azure 门户备份 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)。

本文将指导如何进行以下操作：

- 创建备份保管库

- 创建备份策略

- 配置 Azure 磁盘备份

- 运行按需备份作业

有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是 Azure 中的一个存储实体，用于保存 Azure 备份支持的各种新型工作负荷（例如 Azure Database for PostgreSQL 服务器和 Azure 磁盘）的备份数据。 备份保管库便于组织备份数据，并最大限度降低管理开销。 备份保管库基于 Azure 的 Azure 资源管理器型号，提供增强的功能来帮助保护备份数据。

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。
1. 在搜索框中键入“备份中心”。
1. 在“服务”下，选择“备份中心”。
1. 在“备份中心”页面上，选择“保管库”。

   ![在“备份中心”选择“保管库”](./media/backup-managed-disks/backup-center.png)

1. 在“启动：创建保管库”屏幕上，选择“备份保管库”，然后选择“继续”。

   ![启动：创建保管库](./media/backup-managed-disks/initiate-create-vault.png)

1. 在“基本信息”选项卡中，提供订阅、资源组、备份保管库名称、区域和备份存储冗余。 选择“查看 + 创建”继续操作。 详细了解[创建备份保管库](./backup-vault-overview.md#create-a-backup-vault)。

   ![查看并创建保管库](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>创建备份策略

1. 在上一步中创建的“DemoVault 备份保管库”中，请参阅“备份策略”并选择“添加”。

   ![添加备份策略](./media/backup-managed-disks/backup-policies.png)

1. 在“基本信息”选项卡中，提供策略名称，将“Datasource 类型”选择为“Azure 磁盘” 。 已预填充保管库，并显示所选的保管库属性。

   >[!NOTE]
   > 尽管所选的保管库可能具有全局冗余设置，但当前 Azure 磁盘备份仅支持 snapshot 数据存储。 所有备份都存储在订阅的资源组中，不会复制到备份保管库存储中。

   ![选择数据源类型](./media/backup-managed-disks/datasource-type.png)

1. 在“备份策略”选项卡中，选择备份计划频率。

   ![选择备份计划频率](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure 磁盘备份每天提供多次备份。 如果需要更频繁地进行备份，请选择“每小时”备份频率，让你能够以每 4、6、8 或 12 小时的间隔进行备份。 根据所选的“时间”间隔安排备份。 例如，如果选择“每 4 小时”备份一次，则会按 4 小时的间隔执行备份，以便备份在一天中均匀分布。 如果每天备份一次即可满足需求，则选择“每日”备份频率。 可在每日备份频率中指定每日备份时间。 请注意，每日备份时间表示备份开始时间，而不是备份完成时间。 完成备份操作所需时间取决于各种因素，包括磁盘大小和连续备份的更改率。 但是，Azure 磁盘备份是使用[增量快照](../virtual-machines/disks-incremental-snapshots.md)的无代理备份，不会影响生产应用程序的性能。

1. 在“备份策略”选项卡中，选择满足恢复点目标 (RPO) 要求的保留设置。

   如果未指定其他保留规则，将应用默认的保留规则。 可以修改默认保留规则以更改保留期，但不能删除它。 可以通过选择“添加保留规则”来添加新的保留规则。

   ![添加保留规则](./media/backup-managed-disks/add-retention-rule.png)

   可以选择每天或每周执行的“第一次成功备份”，并提供特定备份在被删除之前保留的保留期。 此选项可用于将一天或一周的特定备份保留更长的时间。 所有其他频率的备份则保留较短时间。

   ![保留设置](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >托管磁盘的 Azure 备份使用每个磁盘限制为 200 快照的增量快照。 为了可以按计划进行备份，以及按需进行备份，备份策略将总备份限制为 180 个。 详细了解托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)。

1. 通过选择“查看 + 创建”完成备份策略创建。

## <a name="configure-backup"></a>配置备份

- Azure 磁盘备份仅支持操作层备份，将备份复制到保管库存储层当前不可用。 备份保管库存储冗余设置 (LRS/GRS) 不适用于操作层中存储的备份。
增量快照存储在标准 HDD 存储中，与父磁盘的存储类型无关。 为了提高可靠性，在支持[区域冗余存储](../storage/common/storage-redundancy.md) (ZRS) 的区域中，增量快照默认存储在 ZRS 上。

- Azure 磁盘备份支持跨订阅备份，并使用一个订阅中的备份保管库和另一个订阅中的源磁盘进行还原。 但不支持跨区域备份和还原。 这允许备份保管库和要备份的磁盘位于同一订阅或不同订阅中。 但是，备份保管库和要备份的磁盘必须位于同一区域。

- 在配置磁盘备份时，不能更改分配给备份实例的快照资源组。 

备份保管库使用托管标识来访问其他 Azure 资源。 如要配置托管磁盘的备份，备份保管库的托管标识需要对创建和管理快照的源磁盘和资源组具有一组权限。

系统分配的托管标识限制为每个资源一个，并绑定到此资源的生命周期。 可以使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予对托管标识的访问权限。 托管标识是一种只能用于 Azure 资源的特殊类型的服务主体。 详细了解[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

配置托管磁盘的备份，需要满足以下先决条件：

1. 在要备份的源磁盘上，将“磁盘备份读取器”角色分配到备份保管库的托管标识。

   1. 转到需要备份的磁盘。

   1. 依次选择“访问控制（标识和访问管理）”、“添加角色分配” 

   1. 在右侧的上下文窗格中，在“角色”下拉列表中选择“磁盘备份读取器”。 选择备份保管库的托管标识，然后单击“保存”。

   >[!TIP]
   >键入备份保管库名称以选择保管库的托管标识。

   ![添加磁盘备份读取器角色](./media/backup-managed-disks/disk-backup-reader-role.png)

1. 在 Azure 备份服务创建和管理备份的资源组上，将“磁盘快照参与者”角色分配给备份保管库的托管标识。 磁盘快照存储在订阅内的资源组中。 如要允许 Azure 备份服务创建、存储和管理快照，需要向备份保管库提供权限。

   **选择用于存储和管理快照的资源组：**

   - 不要选择与源磁盘相同的资源组。

   - 作为指导原则，建议创建一个专用资源组作为快照数据存储，供 Azure 备份服务使用。 有了专门的资源组，就可以限制资源组的访问权限，从而使备份数据的管理更安全和更方便。

   - 可以使用此资源组来存储正在（或计划）备份的多个磁盘上的快照。  

   - 无法在某一特定磁盘的订阅之外为该磁盘创建增量快照。 因此，请选择与要备份的磁盘同一订阅内的资源组。 详细了解托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)。

   - 在配置磁盘备份时，不能更改分配给备份实例的快照资源组。

   - 在备份操作过程中，Azure 备份会在快照资源组中创建存储帐户。 每个快照资源组只创建一个存储帐户。 该帐户在使用与快照资源组相同资源组的多个磁盘备份实例中重复使用。
     
     - 快照不会存储在存储帐户中。 托管磁盘的增量快照是在资源组而不是存储帐户中创建的 ARM 资源。 
     
     - 存储帐户用于存储每个恢复点的元数据。 Azure 备份服务会为每个磁盘备份实例创建一个 Blob 容器。 对于每个恢复点，将创建一个块 blob 来存储描述恢复点（例如订阅、磁盘 ID、磁盘属性等）的元数据，这些元数据占用的空间很小（以几 KiB 为单位）。
     
     - 如果区域支持区域冗余，存储帐户将创建为 RA GZRS。 如果区域不支持区域冗余，存储帐户将创建为 RA GRS。<br>如果任何现有策略停止在具有 GRS 冗余的订阅或资源组中创建存储帐户，则存储帐户将创建为 LRS。 创建的存储帐户是常规用途 v2，其中块 blob 存储在 Blob 容器中的热存储层上。 
     
     - 恢复点的数目由用于配置磁盘备份实例备份的备份策略决定。 根据垃圾回收过程删除较旧的块 blob，这是因为删除了相应的较旧恢复点。
   
   - 不要对 Azure 备份服务创建的快照资源组或存储帐户应用资源锁或策略。 此服务创建并管理此快照资源组中的资源，该资源组是在配置磁盘备份时分配给备份实例的。 存储帐户和其中的资源是由服务创建的，不应删除或移动。

     >[!NOTE]
     >如果删除某个存储帐户，则备份将失败，并且对于所有现有恢复点，还原将失败。

如要分配角色，请执行以下步骤：

   1. 转到资源组。 例如，资源组是 *SnapshotRG*，它与要备份的磁盘位于同一订阅中。

   1. 依次选择“访问控制（标识和访问管理）”、“添加角色分配” 。

   1. 在右侧的上下文窗格中，在“角色”下拉列表中选择“磁盘快照参与者”。 选择备份保管库的托管标识，然后单击“保存”。

   >[!TIP]
   >键入备份保管库名称以选择保管库的托管标识。

   ![添加磁盘快照参与者角色](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. 验证备份保管库的托管标识是否在用作快照数据存储的源磁盘和资源组上有一组正确的角色分配。

   1. 转到“备份保管库 - > 标识”，然后选择“Azure 角色分配”。

      ![选择 Azure 角色分配](./media/backup-managed-disks/azure-role-assignments.png)

   1. 验证是否正确反映了“角色”、“资源名称”和“资源类型”。

      ![验证角色、资源名称和资源类型](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >虽然在门户上正确地反映了角色分配，但可能需要大约 15 分钟才能将权限应用到备份库的托管标识上。

1. 满足先决条件后，转到“备份保管库 - > 概述”，然后选择“备份”开始配置磁盘备份。

   ![选择备份](./media/backup-managed-disks/select-backup.png)

1. 在“基本信息”选项卡中，选择“Azure 磁盘”作为数据源类型。

   ![选择 Azure 磁盘](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure 备份使用托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)，无论父磁盘的存储类型如何，增量快照仅存储自标准 HDD 存储上的上一次快照以来磁盘的增量更改。 为了提高可靠性，在支持区域冗余存储 (ZRS) 的区域中，增量快照默认存储在 ZRS 上。 目前，Azure 磁盘备份支持托管磁盘的操作备份，不会将备份复制到备份保管库存储中。 因此备份保管库的备份存储冗余设置不适用于恢复点。

1. 在“备份策略”选项卡中，选择备份策略。

   ![选择备份策略](./media/backup-managed-disks/choose-backup-policy.png)

1. 在“资源”选项卡中，选择“选择 Azure 磁盘”。 在右侧的上下文窗格中，选择要备份的磁盘。

   ![选择要备份的磁盘](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >尽管门户允许选择多个磁盘和配置备份，但每个磁盘都是一个单独的备份实例。 目前，Azure 磁盘备份仅支持备份单个磁盘。 不支持连接到虚拟机的多个磁盘的时间点备份。
   >
   >使用门户时，只能选择同一订阅中的磁盘。 如果要备份多个磁盘，或这些磁盘分布在不同的订阅中，则可以使用脚本进行自动执行。
   >
   >有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

1. 选择“快照资源组”，然后选择“验证”。 这是 Azure 备份服务为备份保管库创建和管理增量快照的资源组。 作为先决条件的一部分，为托管标识分配了所需的角色权限。

   ![选择快照资源组](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >验证可能需要几分钟才能完成，然后才能完成备份工作流的配置。 如果出现以下情况，验证可能失败：
   >
   > - 磁盘不受支持。 有关不受支持的方案，请参阅[支持矩阵](disk-backup-support-matrix.md)。
   > - 备份保管库托管标识在要备份的“磁盘”上或在存储增量快照的“快照资源组”上没有有效的角色分配。

1. 成功验证后，选择“查看和配置”，配置所选磁盘的备份。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在上一步中创建的“DemoVault 备份保管库”中，转到“备份实例”并选择“备份实例”。

   ![选择备份实例](./media/backup-managed-disks/select-backup-instance.png)

1. 在“备份实例”屏幕中，你会发现：

   - “基本”信息包括源磁盘名称、存储增量快照的快照资源组、备份保管库和备份策略。
   - “作业状态”显示备份和还原操作的摘要及其在过去 7 天内的状态。
   - 所选时间段的“还原点”。

1. 选择“备份”，启动按需备份。

   ![选择立即备份](./media/backup-managed-disks/backup-now.png)

1. 选择与备份策略关联的一个保留规则。 此保留规则将确定此按需备份的保留期。 选择“立即备份”，开始备份。

   ![开始备份](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>追踪备份操作

Azure 备份服务为计划的备份创建作业，或者触发按需备份操作以便进行跟踪。 查看备份作业状态：

1. 转到“备份实例”屏幕。 其中显示了过去 7 天的作业仪表板，其中包含操作和状态。

   ![作业仪表板](./media/backup-managed-disks/jobs-dashboard.png)

1. 如要查看备份操作的状态，请选择“查看全部”，显示此备份实例的当前作业和已完成的作业。

   ![选择查看全部](./media/backup-managed-disks/view-all.png)

1. 查看备份和还原作业及其状态的列表。 从作业列表中选择一个作业，查看作业详细信息。

   ![选择作业以查看详细信息](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>后续步骤

- [还原 Azure 托管磁盘](restore-managed-disks.md)