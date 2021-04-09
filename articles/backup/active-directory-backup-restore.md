---
title: 备份和还原 Active Directory
description: 了解如何备份和还原 Active Directory 域控制器。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733547"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>备份和还原 Active Directory 域控制器

备份 Active Directory 并确保在出现损坏、泄漏或灾难的情况下成功还原是 Active Directory 维护的关键部分。

本文概述了使用 Azure 备份来备份和还原 Active Directory 域控制器（无论它们是 Azure 虚拟机还是本地服务器）的正确过程。 本文讨论了需要将整个域控制器还原到备份时的状态的场景。 若要查看适合你的还原场景，请参阅[本文](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)。  

>[!NOTE]
> 本文不讨论如何从 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 还原项。 有关还原 Azure Active Directory 用户的信息，请参阅[本文](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="best-practices"></a>最佳做法

- 请确保至少备份一个域控制器。 若要备份多个域控制器，请确保备份具有 [FSMO（灵活单主机操作）角色](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement)的所有域控制器。

- 经常备份 Active Directory。 备份决不应超过逻辑删除生存期（默认为 60 天），因为超过逻辑删除生存期的对象将被“逻辑删除”，并且不再被视为有效。

- 有一个明确的灾难恢复计划，其中包含有关如何还原域控制器的说明。 若要准备还原 Active Directory 林，请阅读 [Active Directory 林恢复指南](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)。

- 如果需要还原域控制器，并且域中有另一个正常运行的域控制器，则可创建新服务器，而不是从备份还原。 将“Active Directory 域服务”服务器角色添加到新服务器，使其成为现有域中的域控制器。 然后，Active Directory 数据将复制到新服务器。 若要从 Active Directory 删除以前的域控制器，请按照[本文中](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup)的步骤执行元数据清理。

>[!NOTE]
>Azure 备份不包括 Active Directory 的项级还原。 如果要还原已删除的对象并且你可访问域控制器，请使用 [Active Directory 回收站](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)。 如果该方法不可用，则可使用域控制器备份通过 ntdsutil.exe 工具还原已删除的对象，如[此处](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)所述。
>
>有关执行 SYSVOL 的权威还原的信息，请参阅[本文](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)。

## <a name="backing-up-azure-vm-domain-controllers"></a>备份 Azure VM 域控制器

如果域控制器是 Azure VM，则可使用 [Azure VM 备份](backup-azure-vms-introduction.md)来备份服务器。

阅读[虚拟化域控制器的操作注意事项](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers)，确保 Azure VM 域控制器备份（以及将来的还原）成功。

## <a name="backing-up-on-premises-domain-controllers"></a>备份本地域控制器

若要备份本地域控制器，需要备份服务器的系统状态数据。

- 如果你使用的是 MARS，请按照[这些说明](backup-azure-system-state.md)操作。
- 如果使用的是 MABS（Azure 备份服务器），请按照[这些说明](backup-mabs-system-state-and-bmr.md)操作。

>[!NOTE]
> 不支持将本地域控制器（从系统状态或从 VM）还原到 Azure 云。 如果要选择从本地 Active Directory 环境故障转移到 Azure，请考虑使用 [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)。

## <a name="restoring-active-directory"></a>还原 Active Directory

可通过以下任一模式还原 Active Directory 数据：权威或非权威 。 在权威还原中，还原的 Active Directory 数据将替代在林中其他域控制器上找到的数据。

但在本场景下，我们要在现有域中重新生成一个域控制器，因此应执行非权威还原。

在还原过程中，服务器将以目录服务还原模式 (DSRM) 启动。 需要为目录服务还原模式提供管理员密码。

>[!NOTE]
>如果忘记了 DSRM 密码，可使用[这些说明](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))进行重置。

### <a name="restoring-azure-vm-domain-controllers"></a>还原 Azure VM 域控制器

若要还原 Azure VM 域控制器，请参阅[还原域控制器 VM](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。

如果要还原单个域控制器 VM 或单个域中的多个域控制器 VM，请像还原任何其他 VM 一样还原它们。 还可使用目录服务还原模式 (DSRM)，因此所有 Active Directory 恢复方案都是可行的。

如果需要还原多域配置中的单个域控制器 VM，请还原磁盘并使用 [PowerShell](backup-azure-vms-automation.md#restore-the-disks) 创建 VM。

如果要还原域中剩余的最后一个域控制器，或还原一个林中的多个域，建议使用[林恢复](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。

>[!NOTE]
> Windows 2012 及更高版本中的虚拟化域控制器使用[基于虚拟化的安全措施](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)。 利用这些安全措施，Active Directory 可了解还原的 VM 是否为域控制器，并执行必要的步骤来还原 Active Directory 数据。

### <a name="restoring-on-premises-domain-controllers"></a>还原本地域控制器

若要还原本地域控制器，请使用[域控制器上系统状态恢复的特殊注意事项](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)指南，按照其中的说明将系统状态还原为 Windows Server。

## <a name="next-steps"></a>后续步骤

- [Azure 备份的支持矩阵](backup-support-matrix.md)