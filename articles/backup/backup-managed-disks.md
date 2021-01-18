---
title: 备份 Azure 托管磁盘
description: 了解如何从 Azure 门户备份 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c4e2d4e649d59389ac3f8bd115e43acabc792eb2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557345"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>在预览版中备份 Azure 托管磁盘 () 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅 [支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

本文介绍如何从 Azure 门户备份 [Azure 托管磁盘](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) 。

本文介绍如何执行以下操作：

- 创建备份保管库

- 创建备份策略

- 配置 Azure 磁盘的备份

- 运行按需备份作业

有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是 Azure 中的一个存储实体，用于保存 Azure 备份支持的各种新型工作负荷（例如 Azure Database for PostgreSQL 服务器和 Azure 磁盘）的备份数据。 备份保管库可以方便地组织备份数据，同时将管理开销降至最低。 备份保管库基于 azure 的 Azure 资源管理器型号，提供增强的功能来帮助保护备份数据。

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。
1. 在搜索框中键入 " **备份中心** "。
1. 在 " **服务**" 下，选择 " **备份中心**"。
1. 在 " **备份中心** " 页上，选择 **保管库**。

   ![在备份中心选择保管库](./media/backup-managed-disks/backup-center.png)

1. 在 " **启动：创建保管库** " 屏幕上，选择 " **备份保管库**"，然后 **继续**。

   ![启动：创建保管库](./media/backup-managed-disks/initiate-create-vault.png)

1. 在 " **基本** 信息" 选项卡中，提供订阅、资源组、备份保管库名称、区域和备份存储冗余。 选择 " **查看 + 创建**" 继续。 了解有关 [创建备份保管库的](https://docs.microsoft.com/azure/backup/backup-vault-overview#create-a-backup-vault)详细信息。

   ![查看并创建保管库](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>创建备份策略

1. 在上一步中创建的 *DemoVault* **备份保管库** 中，请参阅 " **备份策略** " 并选择 " **添加**"。

   ![添加备份策略](./media/backup-managed-disks/backup-policies.png)

1. 在 " **基本** 信息" 选项卡中，提供策略名称，将 **Datasource 类型** 选择为 **Azure 磁盘**。 已预填充保管库，并显示所选的保管库属性。

   >[!NOTE]
   > 尽管所选的保管库可能具有全局冗余设置，但当前 Azure 磁盘备份仅支持 snapshot 数据存储。 所有备份都存储在订阅的资源组中，不会复制到备份保管库存储中。

   ![选择数据源类型](./media/backup-managed-disks/datasource-type.png)

1. 在 " **备份策略** " 选项卡中，选择备份计划频率。

   ![选择备份计划频率](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure 磁盘备份每天提供多次备份。 如果需要更频繁地进行备份，请选择 "每 **小时** " 备份频率，并以每隔4、6、8或12小时的间隔进行备份。 根据所选的 **时间** 间隔，计划备份。 例如，如果 **每隔4小时选择一** 次，则会在每隔4小时的时间间隔内执行备份，以便在一天中平均分布备份。 如果每天备份一次，则选择 **每日** 备份频率。 在每天的备份频率中，可以指定备份的时间。 请注意，这一天的时间指示备份开始时间，而不是备份完成的时间。 完成备份操作所需的时间取决于各种因素，包括磁盘大小和连续备份之间的变动率。 但是，Azure 磁盘备份是使用 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal)的无代理备份，不会影响生产应用程序的性能。

1. 在 " **备份策略** " 选项卡中，选择满足恢复点目标的保留设置 (RPO) 要求。

   如果未指定其他保留规则，将应用默认的保留规则。 可以修改默认保留规则以更改保留期，但不能删除它。 可以通过选择 " **添加保留规则**" 来添加新的保留规则。

   ![添加保留规则](./media/backup-managed-disks/add-retention-rule.png)

   你可以选择每天或每周执行的 **第一次成功备份** ，并提供特定备份在被删除之前保留的保留期。 此选项可用于保留较长时间的一天或一周的特定备份。 在较短的时间内，可以保留所有其他频繁的备份。

   ![保留设置](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >托管磁盘的 Azure 备份使用限制为每个磁盘200快照的增量快照。 为了允许除计划备份外的按需备份，备份策略会将备份总数限制为180。 详细了解托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 。

1. 通过选择 " **查看 + 创建**" 完成备份策略创建。

## <a name="configure-backup"></a>配置备份

备份保管库使用托管标识来访问其他 Azure 资源。 若要配置托管磁盘的备份，备份保管库的托管标识需要对创建和管理快照的源磁盘和资源组具有一组权限。

系统分配的托管标识限制为每个资源一个，并绑定到此资源的生命周期。 可以使用 Azure RBAC)  (基于角色的访问控制向托管标识授予权限。 托管标识是特定类型的服务主体，只能与 Azure 资源一起使用。 了解有关 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

若要配置托管磁盘的备份，需要满足以下先决条件：

1. 在要备份的源磁盘上，将 **磁盘备份读取器** 角色分配到备份保管库的托管标识。

   1. 中转到需要备份的磁盘。

   1. **(IAM)** 中转到 "访问控制"，然后选择 "**添加角色分配**"

   1. 在右侧的上下文窗格中，在 "**角色**" 下拉列表中选择 "**磁盘备份读取器**"。 选择备份保管库的托管标识，然后单击 " **保存**"。

   >[!TIP]
   >键入备份保管库名称以选择保管库的托管标识。

   ![添加磁盘备份读取器角色](./media/backup-managed-disks/disk-backup-reader-role.png)

1. 在 Azure 备份服务创建和管理备份的资源组上，将 **磁盘快照参与者** 角色分配给备份保管库的托管标识。 磁盘快照存储在订阅内的资源组中。 若要允许 Azure 备份服务创建、存储和管理快照，需要向备份保管库提供权限。

   **选择用于存储和管理快照的资源组：**

   - 不要选择与源磁盘相同的资源组。

   - 作为指导原则，建议创建专用资源组作为要由 Azure 备份服务使用的快照数据存储。 拥有专用资源组允许限制对资源组的访问权限，从而使备份数据的管理更容易。

   - 可以使用此资源组在要 (或计划) 备份的多个磁盘上存储快照。  

   - 不能为该磁盘的订阅之外的特定磁盘创建增量快照。 因此，请选择与要备份的磁盘相同的订阅中的资源组。 详细了解托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 。

   若要分配角色，请执行以下步骤：

   1. 请参阅资源组。 例如，资源组是 *SnapshotRG*，它与要备份的磁盘位于同一订阅中。

   1. 请 **访问 (IAM) 的 "访问控制** "，然后选择 " **添加角色分配**"。

   1. 在右侧的上下文窗格中，在 "**角色**" 下拉列表中选择 "**磁盘快照参与者**"。 选择备份保管库的托管标识，然后单击 " **保存**"。

   >[!TIP]
   >键入备份保管库名称以选择保管库的托管标识。

   ![添加磁盘快照参与者角色](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. 验证备份保管库的托管标识是否在用作快照数据存储的源磁盘和资源组上有一组正确的角色分配。

   1. 请参阅 **备份保管库-> 标识** ，然后选择 " **Azure 角色分配**"。

      ![选择 Azure 角色分配](./media/backup-managed-disks/azure-role-assignments.png)

   1. 验证是否正确反映了 "角色"、"资源名称" 和 "资源类型"。

      ![验证角色、资源名称和资源类型](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >虽然角色分配在门户上正确反映，但在备份保管库的托管标识上应用权限可能需要大约15分钟。

1. 满足先决条件后，请参阅 **备份保管库-> 概述** ，然后选择 " **备份** " 开始配置磁盘备份。

   ![选择备份](./media/backup-managed-disks/select-backup.png)

1. 在 " **基本** 信息" 选项卡中，选择 " **Azure 磁盘** " 作为数据源类型。

   ![选择 Azure 磁盘](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure 备份使用托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) ，这些快照仅存储自上一次快照到标准 HDD 存储以来磁盘的增量更改，而不考虑父磁盘的存储类型。 为了提高可靠性，默认情况下，增量快照存储在区域冗余存储 (ZRS) 在支持 ZRS 的区域中。 目前，Azure 磁盘备份支持不将备份复制到备份保管库存储的托管磁盘的操作备份。 因此备份保管库的备份存储冗余设置不适用于恢复点。

1. 在 " **备份策略** " 选项卡中，选择备份策略。

   ![选择备份策略](./media/backup-managed-disks/choose-backup-policy.png)

1. 在 " **资源** " 选项卡中，选择 " **选择 Azure 磁盘**"。 在右侧的上下文窗格中，选择要备份的磁盘。

   ![选择要备份的磁盘](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >尽管门户允许你选择多个磁盘和配置备份，但每个磁盘都是一个单独的备份实例。 目前，Azure 磁盘备份仅支持备份单个磁盘。 不支持附加到虚拟磁盘的多个磁盘的时间点备份。
   >
   >使用门户时，只能选择同一订阅中的磁盘。 如果要备份多个磁盘，或这些磁盘分布在不同的订阅中，则可以使用脚本进行自动执行。
   >
   >有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

1. 选择 **快照资源组** ，然后选择 " **验证**"。 这是 Azure 备份服务创建和管理备份保管库的增量快照的资源组。 托管标识作为先决条件的一部分分配给所需的角色权限。

   ![选择快照资源组](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >验证可能需要几分钟才能完成，然后才能完成备份工作流的配置。 如果出现以下情况，验证可能失败：
   >
   > - 磁盘不受支持。 有关不支持的方案，请参阅 [支持矩阵](disk-backup-support-matrix.md) 。
   > - 备份保管库托管标识在要备份的 **磁盘** 上或在存储增量快照的 **快照资源组** 上没有有效的角色分配。

1. 成功验证后，选择 " **查看和配置** " 以配置所选磁盘的备份。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在上一步中创建的 *DemoVault* **备份保管库** 中，请参阅 " **备份实例** " 并选择备份实例。

   ![选择备份实例](./media/backup-managed-disks/select-backup-instance.png)

1. 在 " **备份实例** " 屏幕中，你会发现：

   - **基本** 信息，包括源磁盘名称、存储增量快照的快照资源组、备份保管库和备份策略。
   - **作业状态** 显示备份和还原操作的摘要及其在过去7天内的状态。
   - 所选时间段的 **还原点** 列表。

1. 选择 " **备份** " 以启动按需备份。

   ![选择 "立即备份"](./media/backup-managed-disks/backup-now.png)

1. 选择与备份策略关联的一个保留规则。 此保留规则将确定此按需备份的保持期。 选择 " **立即备份** " 以开始备份。

   ![启动备份](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>跟踪备份操作

Azure 备份服务为计划的备份创建作业，或者触发按需备份操作以便进行跟踪。 查看备份作业状态：

1. 请参阅 **备份实例** 屏幕。 其中显示了过去7天的作业仪表板，其中包含操作和状态。

   ![作业仪表板](./media/backup-managed-disks/jobs-dashboard.png)

1. 若要查看备份操作的状态，请选择 " **全部查看** " 以显示此备份实例的持续和过去作业。

   ![选择全部查看](./media/backup-managed-disks/view-all.png)

1. 查看备份和还原作业及其状态的列表。 从作业列表中选择一个作业以查看作业详细信息。

   ![选择作业以查看详细信息](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>后续步骤

- [还原 Azure 托管磁盘](restore-managed-disks.md)
