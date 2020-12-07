---
title: 备份和还原 Active Directory
description: 了解如何备份和还原 Active Directory 域控制器。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: afbc538b84c20f9baaa664c7d47140365c747cb0
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754610"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>备份和还原 Active Directory 域控制器

备份 Active Directory，并确保在出现损坏、折衷或灾难的情况下进行成功的还原是 Active Directory 维护的关键部分。

本文概述了在 Azure 备份中备份和还原 Active Directory 域控制器的正确过程，无论它们是 Azure 虚拟机还是本地服务器。 本文讨论了在备份时需要将整个域控制器还原到其状态的情况。 若要查看适合你的还原方案，请参阅 [此文](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)。  

>[!NOTE]
> 本文不讨论如何从 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)还原项目。 有关还原 Azure Active Directory 用户的信息，请参阅 [此文](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore)。

## <a name="best-practices"></a>最佳做法

- 请确保至少备份一个域控制器。 如果要备份多个域控制器，请确保备份所有包含 [FSMO (灵活单主机操作) 角色](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) 的域控制器。

- 经常备份 Active Directory。 默认情况下，备份不应超过逻辑删除生存期 (60 天) ，因为比 tombstone 生存时间早的对象将被 "逻辑删除"，并且不再被视为有效。

- 有一个明确的灾难恢复计划，其中包含有关如何还原域控制器的说明。 若要准备还原 Active Directory 林，请阅读 [Active Directory 林恢复指南](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)。

- 如果需要还原域控制器，并且域中有另一个正常运行的域控制器，则可以创建新服务器，而不是从备份还原。 将 **Active Directory 域服务** 服务器角色添加到新服务器以使其成为现有域中的域控制器。 然后，Active Directory 的数据将复制到新服务器。 若要从 Active Directory 中删除以前的域控制器，请按照 [本文中](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) 的步骤执行元数据清理。

>[!NOTE]
>Azure 备份不包括 Active Directory 的项目级还原。 如果要还原已删除的对象，并且可以访问域控制器，请使用 [Active Directory 回收站](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)。 如果该方法不可用，则可以使用域控制器备份按 [此处](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)所述的 **ntdsutil.exe** 工具还原已删除的对象。
>
>有关执行 SYSVOL 的权威还原的信息，请参阅 [此文](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)。

## <a name="backing-up-azure-vm-domain-controllers"></a>备份 Azure VM 域控制器

如果域控制器是 Azure VM，则可以使用 [AZURE Vm 备份](backup-azure-vms-introduction.md)来备份服务器。

了解有关 [虚拟化域控制器的操作注意事项](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) ，以确保成功备份 (和未来的恢复) Azure VM 域控制器。

## <a name="backing-up-on-premises-domain-controllers"></a>备份本地域控制器

若要备份本地域控制器，需要备份服务器的系统状态数据。

- 如果使用的是 MARS，请遵循 [这些说明](backup-azure-system-state.md)。
- 如果使用 MABS (Azure 备份服务器) ，请按照 [以下说明](backup-mabs-system-state-and-bmr.md)操作。

>[!NOTE]
> 不支持将本地域控制器从系统状态或从 Vm) 还原到 Azure 云 (。 如果要选择从本地 Active Directory 环境故障转移到 Azure，请考虑使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory)。

## <a name="restoring-active-directory"></a>正在还原 Active Directory

可以通过以下两种模式之一还原 Active Directory 的数据： **权威** 或非 **权威**。 在权威还原中，还原的 Active Directory 数据将覆盖在林中的其他域控制器上找到的数据。

但是，在这种情况下，我们要在现有域中重建域控制器，因此应执行非 **授权** 还原。

在还原过程中，服务器将以目录服务还原模式 (DSRM) 启动。 需要为目录服务还原模式提供管理员密码。

>[!NOTE]
>如果忘记了 DSRM 密码，你可以使用 [这些说明](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))进行重置。

### <a name="restoring-azure-vm-domain-controllers"></a>还原 Azure VM 域控制器

若要还原 Azure VM 域控制器，请参阅 [还原域控制器 vm](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。

如果要还原单个域中的单个域控制器 VM 或多个域控制器 Vm，请像还原任何其他 VM 一样还原它们。 还可使用目录服务还原模式 (DSRM)，因此所有 Active Directory 恢复方案都是可行的。

如果需要还原多域配置中的单个域控制器 VM，请 [使用 PowerShell](backup-azure-vms-automation.md#restore-the-disks)还原磁盘并创建 VM。

如果要还原域中的最后一个域控制器，或在一个林中还原多个域，则建议使用 [林恢复](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。

>[!NOTE]
> Windows 2012 中的虚拟化域控制器将使用 [基于虚拟化的安全措施](https://docs.microsoft.com/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)。 利用这些安全措施，Active directory 可了解 VM 是否已还原为域控制器，并执行必要的步骤来还原 Active Directory 的数据。

### <a name="restoring-on-premises-domain-controllers"></a>还原本地域控制器

若要还原本地域控制器，请按照有关 [域控制器上系统状态恢复的特别注意事项](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)的指导，按照中的说明将系统状态还原到 Windows Server。

## <a name="next-steps"></a>后续步骤

- [Azure 备份的支持矩阵](backup-support-matrix.md)
