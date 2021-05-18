---
title: 还原 Azure 托管磁盘
description: 了解如何从 Azure 门户还原 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 995217cd17d1e2a16cd7a5f963ee88aa7116d4a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703743"
---
# <a name="restore-azure-managed-disks-in-preview"></a>还原 Azure 托管磁盘（预览版）

>[!IMPORTANT]
>此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅[支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此表格](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)以便注册预览版。

本文将讲解如何从 Azure 备份创建的还原点还原 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)。

目前，不支持通过替换备份所在的现有源磁盘选择 Original-Location Recovery (OLR) 还原选项。 可从恢复点还原，以在备份所在的源磁盘所属资源组或任何其他资源组创建一个新磁盘。 这种方法名为 Alternate-Location Recovery (ALR)，有助于保留源磁盘和还原的（新）磁盘。

本文将指导如何进行以下操作：

- 还原并创建新磁盘

- 跟踪还原操作状态

## <a name="restore-to-create-a-new-disk"></a>还原并创建新磁盘

备份保管库使用托管标识来访问其他 Azure 资源。 若要从备份中还原，备份保管库的托管标识需要对待还原磁盘所在的资源组拥有一组权限。

备份系统管库使用系统分配的托管标识（限制为每个资源一个），并绑定到此资源的生命周期。 可使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予托管标识的访问权限。 托管标识是一种只能用于 Azure 资源的特殊类型的服务主体。 详细了解[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

执行还原操作需要满足以下先决条件：

1. 在 Azure 备份服务将还原磁盘的资源组上，将“磁盘还原操作员”角色分配给备份保管库的托管标识。

    >[!NOTE]
    > 可以选择与从中进行备份的源磁盘相同的资源组，也可以选择相同或不同订阅中的任何其他资源组。

    1. 转到要将磁盘还原到的资源组。 例如，资源组为 TargetRG。

    1. 依次选择“访问控制（标识和访问管理）”、“添加角色分配” 

    1. 在右侧的上下文窗格中，在“角色”下拉列表中选择“磁盘还原操作员”。 选择备份保管库的托管标识，然后单击“保存”。

        >[!TIP]
        >键入备份保管库名称以选择保管库的托管标识。

        ![选择磁盘还原操作员角色](./media/restore-managed-disks/disk-restore-operator-role.png)

1. 验证备份保管库的托管标识是否在将还原磁盘的资源组上具有一组正确的角色分配。

    1. 转到“备份保管库 - > 标识”，然后选择“Azure 角色分配”

        ![选择“Azure 角色分配”](./media/restore-managed-disks/azure-role-assignments.png)

    1. 验证角色、资源名称和资源类型是否正确显示。

        ![验证角色、资源名称和资源类型](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >虽然在门户上正确地反映了角色分配，但可能需要大约 15 分钟才能将权限应用到备份库的托管标识上。
    >
    >在计划备份或按需备份操作期间，Azure 备份会将磁盘增量快照存储在配置磁盘备份的过程中提供的快照资源组中。 在还原操作过程中，Azure 备份会使用这些增量快照。 如果快照已从快照资源组中删除或移动，或者备份保管库角色分配已在快照资源组上撤销，则还原操作会失败。

1. 如果要还原的磁盘使用[客户管理的密钥 (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md)或者通过[使用平台管理的密钥和客户管理的密钥的双重加密](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)进行加密，则将“读者”角色权限分配给“磁盘加密集”资源上的备份保管库托管标识 。

满足先决条件后，请按照以下步骤执行还原操作。

1. 在 [Azure 门户](https://portal.azure.com/)中，转到“备份中心”。 在“管理”部分下选择“备份实例” 。 在备份实例列表中，选择要为其执行还原操作的磁盘备份实例。

    ![备份实例列表](./media/restore-managed-disks/backup-instances.png)

    或者，可以从用于为磁盘配置备份的备份保管库执行此操作。

1. 在“备份实例”屏幕中，选择要用于执行还原操作的还原点，然后选择“还原” 。

    ![选择还原点](./media/restore-managed-disks/select-restore-point.png)

1. 在“还原”工作流中，查看“基本信息”和“选择恢复点”选项卡信息，然后选择“下一步: 还原参数”   。

    ![查看基本信息和选择恢复点信息](./media/restore-managed-disks/review-information.png)

1. 在“还原参数”选项卡中，选择要将备份还原到的“目标订阅”和“目标资源组”  。 提供要还原的磁盘的名称。 选择“下一步: 审阅并还原”。

    ![还原参数](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Azure 备份使用磁盘备份解决方案备份的磁盘也可以由 Azure 备份使用 Azure VM 备份解决方案及恢复服务保管库进行备份。 如果配置了此磁盘附加到的 Azure VM 的保护，还可以使用 Azure VM 还原操作。 可以选择从相应 Azure VM 备份实例的恢复点还原 VM 或是磁盘和文件或文件夹。 有关详细信息，请参阅 [Azure VM 备份](./about-azure-vm-restore.md)。

1. 验证成功后，选择“还原”以启动还原操作。

    ![启动还原操作](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > 验证可能需要几分钟才能完成，然后才能触发还原操作。 如果出现以下情况，验证可能失败：
    >
    > - 目标资源组中已存在具有“还原磁盘名称”中提供的相同名称的磁盘 
    > - 备份保管库的托管标识在目标资源组上没有有效的角色分配
    > - 备份保管库的托管标识角色分配在存储增量快照的快照资源组上撤销
    > - 如果从快照资源组删除或移动增量快照

还原操作会在还原操作过程中提供的目标资源组中从所选恢复点创建新磁盘。 若要在现有虚拟机上使用还原的磁盘，需要执行更多步骤：

- 如果还原的磁盘是数据磁盘，则可以将现有磁盘附加到虚拟机。 如果还原的磁盘是 OS 磁盘，则可以从 Azure 门户“虚拟机”窗格下 - >“设置”部分中的“磁盘”菜单交换虚拟机的 OS 磁盘  。

    ![交换 OS 磁盘](./media/restore-managed-disks/swap-os-disks.png)

- 对于 Windows 虚拟机，如果还原的磁盘是数据磁盘，请按照说明从虚拟机中[分离原始数据磁盘](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal)。 然后，[将还原的磁盘附加](../virtual-machines/windows/attach-managed-disk-portal.md)到虚拟机。 按照说明将虚拟机的 [OS 磁盘与还原的磁盘交换](../virtual-machines/windows/os-disk-swap.md)。

- 对于 Linux 虚拟机，如果还原的磁盘是数据磁盘，请按照说明从虚拟机中[分离原始数据磁盘](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal)。 然后，[将还原的磁盘附加](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk)到虚拟机。 按照说明将虚拟机的 [OS 磁盘与还原的磁盘交换](../virtual-machines/linux/os-disk-swap.md)。

成功完成还原操作后，建议从目标资源组上的备份保管库托管标识撤销磁盘还原操作员角色分配 。

## <a name="track-a-restore-operation"></a>跟踪还原操作

触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 若要查看还原作业进度，请执行以下操作：

1. 转到“备份实例”屏幕。 其中显示了过去 7 天的作业仪表板，其中包含操作和状态。

    ![作业仪表板](./media/restore-managed-disks/jobs-dashboard.png)

1. 如要查看还原操作的状态，请选择“查看全部”，显示此备份实例的当前作业和已完成的作业。

    ![选择查看全部](./media/restore-managed-disks/view-all.png)

1. 查看备份和还原作业及其状态的列表。 从作业列表中选择一个作业，查看作业详细信息。

    ![作业列表](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份常见问题解答](disk-backup-faq.md)