---
title: 排查 Azure 磁盘备份中的备份故障
description: 了解如何对 Azure 磁盘备份中的备份失败进行故障排除
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 0a2ef1ea20ee8d6b7a3f32e244d3e00f3add80a2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557694"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>排查 Azure 磁盘备份中的备份失败 (预览) 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅 [支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

本文提供了有关 Azure 磁盘中的备份和还原问题的疑难解答信息。 有关 [Azure 磁盘备份](disk-backup-overview.md) 区域可用性、支持的方案和限制的详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure 磁盘备份面临的常见问题

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>错误代码： UserErrorSnapshotRGSubscriptionMismatch

错误消息：为快照数据存储选择的订阅无效

建议的操作：磁盘和磁盘快照存储在同一订阅中。 你可以选择任何资源组，以将磁盘快照存储在订阅中。 选择与源磁盘相同的订阅。 有关详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

### <a name="error-code-usererrorsnapshotrgnotfound"></a>错误代码： UserErrorSnapshotRGNotFound

错误消息：无法执行操作，因为快照数据存储资源组不存在。

建议的操作：创建资源组，并在其上提供所需的权限。 有关详细信息，请参阅 [配置备份](backup-managed-disks.md#configure-backup)。

### <a name="error-code-usererrormanageddisknotfound"></a>错误代码： UserErrorManagedDiskNotFound

错误消息：无法执行此操作，因为托管磁盘不再存在。

建议的操作：备份将继续失败，因为源磁盘可能会被删除或移动到其他位置。 如果错误地删除了磁盘，请使用现有的还原点还原磁盘。 如果将磁盘移到其他位置，请为磁盘配置备份。

### <a name="error-code-usererrornotenoughpermissionondisk"></a>错误代码： UserErrorNotEnoughPermissionOnDisk

错误消息： Azure 备份服务需要磁盘的其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予对磁盘的适当权限。 请参阅 [文档](backup-managed-disks.md) ，了解备份保管库托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>错误代码： UserErrorNotEnoughPermissionOnSnapshotRG

错误消息： Azure 备份服务需要对快照数据存储资源组具有其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予对快照数据存储资源组的适当权限。 快照数据存储资源组是存储磁盘快照的位置。 请参阅 [文档](backup-managed-disks.md) 以了解哪个是资源组、备份保管库托管标识所需的权限以及如何提供该标识。

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>错误代码： UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

错误消息：磁盘无效或 Azure 备份服务需要磁盘上的其他权限才能执行此操作

建议的操作：备份将继续失败，因为源磁盘可能会被删除或移动到其他位置。 如果错误地删除了磁盘，请使用现有的还原点还原磁盘。 如果将磁盘移到其他位置，请为磁盘配置备份。 如果不删除或移动该磁盘，请向备份保管库的托管标识授予对该磁盘的适当权限。 请参阅 [文档](backup-managed-disks.md) ，了解备份保管库的托管标识所需的权限以及如何提供该标识。

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>错误代码： UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

错误消息：无法执行操作，因为快照数据存储资源组已不再存在。 或 Azure 备份服务需要对快照数据存储资源组的其他权限才能执行此操作。

建议的操作：创建资源组并向备份保管库的托管标识授予对快照数据存储资源组的适当权限。 快照数据存储资源组是存储磁盘快照的位置。 请参阅 [文档](backup-managed-disks.md) ，了解什么是资源组、备份保管库的托管标识所需的权限以及如何提供它。

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>错误代码： UserErrorDiskBackupAuthorizationFailed

错误消息：备份保管库托管标识缺少执行此操作所需的权限。

建议的操作：授予备份保管库的托管标识要备份的磁盘上的适当权限以及存储快照的快照数据存储资源组。 请参阅 [文档](backup-managed-disks.md) ，了解备份保管库的托管标识所需的权限以及如何提供该标识。

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>错误代码： UserErrorSnapshotRGOrMSIPermissionsNotPresent

错误消息：无法执行操作，因为快照数据存储资源组已不再存在。 或者，Azure 备份服务需要对快照数据存储资源组具有其他权限才能执行此操作。

建议的操作：创建资源组，并向备份保管库的托管标识授予对快照数据存储资源组的适当权限。 快照数据存储资源组是存储快照的位置。 请参阅 [文档](backup-managed-disks.md) ，了解什么是资源组、备份保管库的托管标识所需的权限，以及如何提供它。

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>错误代码： UserErrorOperationalStoreParametersNotProvided

错误消息：无法执行操作，因为未提供快照数据存储资源组参数

建议的操作：提供快照数据存储资源组参数。 快照数据存储资源组是存储磁盘快照的位置。 有关详细信息，请参阅 [文档](backup-managed-disks.md)。

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>错误代码： UserErrorInvalidOperationalStoreResourceGroup

错误消息：提供的快照数据存储资源组无效

建议的操作：为快照数据存储提供有效的资源组。 快照数据存储资源组是存储磁盘快照的位置。 有关详细信息，请参阅 [文档](backup-managed-disks.md)。

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>错误代码： UserErrorDiskBackupDiskTypeNotSupported

错误消息：不支持的磁盘类型

建议的操作：有关不受支持的方案和限制，请参阅 [支持矩阵](disk-backup-support-matrix.md) 。

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>错误代码： UserErrorSameNameDiskAlreadyExists

错误消息：无法还原，因为所选目标资源组中已存在具有相同名称的磁盘

建议的操作：为还原提供其他磁盘名称。 有关详细信息，请参阅 [还原 Azure 托管磁盘](restore-managed-disks.md)。

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>错误代码： UserErrorRestoreTargetRGNotFound

错误消息：操作失败，因为目标资源组不存在。

建议的操作：提供有效的资源组以进行还原。 有关详细信息，请参阅 [还原 Azure 托管磁盘](restore-managed-disks.md)。

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>错误代码： UserErrorNotEnoughPermissionOnTargetRG

错误消息： Azure 备份服务需要对目标资源组具有其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予对目标资源组的适当权限。 目标资源组是要在其中还原磁盘的选定位置。 请参阅 [还原文档](restore-managed-disks.md) ，了解备份保管库的托管标识需要哪些权限，以及如何提供该标识。

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>错误代码： UserErrorSubscriptionDiskQuotaLimitReached

错误消息：操作失败，因为已达到订阅的磁盘配额最大限制。

建议的操作：请参阅 [Azure 订阅和服务限制和配额文档](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) ，或联系 Microsoft 支持部门获取进一步指导。

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>错误代码： UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

错误消息：操作失败，因为目标资源组不存在。 或者，Azure 备份服务需要对目标资源组具有其他权限才能执行此操作。

建议的操作：提供有效的资源组以进行还原，并向备份保管库的托管标识授予对目标资源组的适当权限。 目标资源组是要在其中还原磁盘的选定位置。 请参阅 [还原文档](restore-managed-disks.md) ，了解备份保管库的托管标识需要哪些权限，以及如何提供该标识。

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>错误代码： UserErrorDESKeyVaultKeyDisabled

错误消息：用于磁盘加密集的密钥保管库密钥未处于启用状态。

建议的操作：启用用于磁盘加密集的密钥保管库密钥。 请参阅 [支持矩阵](disk-backup-support-matrix.md)中的限制。

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>错误代码： UserErrorMSIPermissionsNotPresentOnDES

错误消息： Azure 备份服务需要访问用于磁盘的磁盘加密集的权限。

建议的操作：向 (DES) 的磁盘加密集提供对备份保管库的托管标识的读取器访问权限。

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>错误代码： UserErrorDESKeyVaultKeyNotAvailable

错误消息：用于磁盘加密集的密钥保管库密钥不可用。

建议的操作：确保未禁用或删除用于磁盘加密集的密钥保管库密钥。

### <a name="error-code-usererrordisksnapshotnotfound"></a>错误代码： UserErrorDiskSnapshotNotFound

错误消息：此还原点的磁盘快照已被删除。

建议的操作：快照存储在订阅内的快照数据存储资源组中。 可能已从该资源组删除或移动与所选还原点相关的快照。 请考虑使用另一个恢复点进行还原。 此外，请遵循 [还原文档](restore-managed-disks.md)中所述的选择快照资源组的推荐指导原则。

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>错误代码： UserErrorSnapshotMetadataNotFound

错误消息：此还原点的磁盘快照元数据已被删除

建议的操作：考虑使用另一个恢复点进行还原。 有关详细信息，请参阅 [还原文档](restore-managed-disks.md)。

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>错误代码： UserErrorMaxConcurrentOperationLimitReached

错误消息：无法启动操作，因为已达到此操作类型的允许并发操作的最大数量。

建议的操作：等待前一个操作完成。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份支持矩阵](disk-backup-support-matrix.md)
