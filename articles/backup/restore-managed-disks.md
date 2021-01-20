---
title: 还原 Azure 托管磁盘
description: 了解如何从 Azure 门户还原 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 848a7476b1c5095d4e4d3156d4c7ce33da777090
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611128"
---
# <a name="restore-azure-managed-disks-in-preview"></a> (预览中还原 Azure 托管磁盘) 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅 [支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

本文介绍如何从 Azure 备份创建的还原点还原 [Azure 托管磁盘](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) 。

目前，Original-Location 恢复 (OLR) 选项，通过替换从其执行备份的现有源磁盘来进行还原。 你可以从恢复点还原，以便在同一资源组中创建一个新磁盘，该磁盘与从中进行备份的源磁盘或任何其他资源组中的源磁盘相同。 这称为 Alternate-Location 恢复 (ALR) ，这有助于将源磁盘和还原 (的新) 磁盘保留下来。

本文介绍如何执行以下操作：

- 还原以创建新磁盘

- 跟踪还原操作状态

## <a name="restore-to-create-a-new-disk"></a>还原以创建新磁盘

备份保管库使用托管标识来访问其他 Azure 资源。 若要从备份还原，备份保管库的托管标识需要对要在其中还原磁盘的资源组拥有一组权限。

备份保管库使用系统分配的托管标识，该标识限制为每个资源一个，并与此资源的生命周期相关联。 可以使用 Azure RBAC)  (基于角色的访问控制向托管标识授予权限。 托管标识是特定类型的服务主体，只能与 Azure 资源一起使用。 了解有关 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

执行还原操作需要以下必备项：

1. 在 Azure 备份服务将还原磁盘的资源组上，将 **磁盘还原操作员** 角色分配给备份保管库的托管标识。

    >[!NOTE]
    > 你可以选择与源磁盘相同的资源组，从该资源组中进行备份或在相同或不同的订阅中的任何其他资源组。

    1. 请参阅要将磁盘还原到的资源组。 例如，资源组为 *TargetRG*。

    1. **(IAM)** 中转到 "访问控制"，然后选择 "**添加角色分配**"

    1. 在右侧的上下文窗格中，在 "**角色**" 下拉列表中选择 "**磁盘还原操作员**"。 选择备份保管库的托管标识，然后单击 " **保存**"。

        >[!TIP]
        >键入备份保管库的名称，以选择保管库的托管标识。

        ![选择磁盘还原操作员角色](./media/restore-managed-disks/disk-restore-operator-role.png)

1. 验证备份保管库的托管标识是否在将还原磁盘的资源组上有一组正确的角色分配。

    1. 请参阅 **备份保管库-> 标识** 并选择 **Azure 角色分配**

        ![选择 Azure 角色分配](./media/restore-managed-disks/azure-role-assignments.png)

    1. 验证 "角色"、"资源名称" 和 "资源类型" 是否正确显示。

        ![验证角色、资源名称和资源类型](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >虽然角色分配在门户上正确反映，但在备份保管库的托管标识上应用权限可能需要大约15分钟。
    >
    >在计划的备份或按需备份操作期间，Azure 备份会将磁盘增量快照存储在配置磁盘的备份过程中提供的快照资源组中。 在还原操作过程中，Azure 备份将使用这些增量快照。 如果快照已从快照资源组中删除或移动，或者快照资源组上的备份保管库角色分配已撤消，则还原操作将失败。

1. 如果要还原的磁盘是使用客户管理的密钥加密的， [ (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal) 或者使用 [使用平台托管密钥和客户托管密钥的双加密](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal)，则将 " **读取** 者角色" 权限分配给 " **磁盘加密集** " 资源上的备份保管库的托管标识。

满足先决条件后，请按照以下步骤执行还原操作。

1. 在 [Azure 门户](https://portal.azure.com/)中，请参阅 " **备份中心**"。 选择 "**管理**" 部分下的 "**备份实例**"。 在备份实例列表中，选择要为其执行还原操作的磁盘备份实例。

    ![备份实例列表](./media/restore-managed-disks/backup-instances.png)

    或者，你可以从用于配置磁盘备份的备份保管库执行此操作。

1. 在 " **备份实例** " 屏幕中，选择要用于执行还原操作的还原点，然后选择 " **还原**"。

    ![选择还原点](./media/restore-managed-disks/select-restore-point.png)

1. 在 **还原** 工作流中，查看 " **基本** 信息" 并 **选择 "恢复点** " 选项卡信息，然后选择 " **下一步：还原参数**"。

    ![查看基础知识和选择恢复点信息](./media/restore-managed-disks/review-information.png)

1. 在 " **还原参数** " 选项卡中，选择要将备份还原到的 **目标订阅** 和 **目标资源组** 。 提供要还原的磁盘的名称。 选择 **下一步：查看 + 还原**。

    ![还原参数](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Azure 备份使用磁盘备份解决方案备份的磁盘也可以通过 azure VM 备份解决方案，通过恢复服务保管库进行备份。 如果已配置此磁盘附加到的 Azure VM 的保护，还可以使用 Azure VM 还原操作。 你可以选择从相应的 Azure VM 备份实例的恢复点还原 VM、磁盘和文件或文件夹。 有关详细信息，请参阅 [AZURE VM 备份](https://docs.microsoft.com/azure/backup/about-azure-vm-restore)。

1. 验证成功后，选择 " **还原** " 以启动还原操作。

    ![启动还原操作](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > 验证可能需要几分钟才能触发还原操作。 如果出现以下情况，验证可能失败：
    >
    > - **目标资源组** 中已存在具有相同名称的磁盘已在 **还原磁盘名称** 中提供
    > - 备份保管库的托管标识在 **目标资源组** 上没有有效的角色分配
    > - 在存储增量快照的 **快照资源组** 上撤消备份保管库的托管标识角色分配
    > - 如果从快照资源组删除或移动增量快照

还原操作将在还原操作过程中提供的目标资源组中从所选恢复点创建新磁盘。 若要在现有虚拟机上使用还原的磁盘，需要执行更多步骤：

- 如果还原的磁盘是数据磁盘，则可以将现有磁盘附加到虚拟机。 如果还原的磁盘是 OS 磁盘，则可以从 "**设置**" 部分中 "**虚拟机**" 窗格下的 ">**磁盘**" 菜单下的 Azure 门户交换虚拟机的 os 磁盘。

    ![交换 OS 磁盘](./media/restore-managed-disks/swap-os-disks.png)

- 对于 Windows 虚拟机，如果还原的磁盘是数据磁盘，请按照说明从虚拟机中 [分离原始数据磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) 。 然后， [将还原的磁盘附加](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) 到虚拟机。 按照说明将虚拟机 [的 OS 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) 与还原的磁盘交换。

- 对于 Linux 虚拟机，如果还原的磁盘是数据磁盘，请按照说明从虚拟机中 [分离原始数据磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) 。 然后， [将还原的磁盘附加](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) 到虚拟机。 按照说明将虚拟机 [的 OS 磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) 与还原的磁盘交换。

在成功完成还原操作后，建议从 **目标资源组** 上的备份保管库的托管标识撤消 **磁盘还原操作员** 角色分配。

## <a name="track-a-restore-operation"></a>跟踪还原操作

触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 查看还原作业进度：

1. 请参阅 **备份实例** 屏幕。 其中显示了过去7天的作业仪表板，其中包含操作和状态。

    ![作业仪表板](./media/restore-managed-disks/jobs-dashboard.png)

1. 若要查看还原操作的状态，请选择 " **全部查看** " 以显示此备份实例的持续和过去作业。

    ![选择全部查看](./media/restore-managed-disks/view-all.png)

1. 查看备份和还原作业及其状态的列表。 从作业列表中选择一个作业以查看作业详细信息。

    ![作业列表](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份常见问题解答](disk-backup-faq.md)
