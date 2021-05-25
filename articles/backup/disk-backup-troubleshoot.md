---
title: 对 Azure 磁盘备份中的备份失败进行故障排除
description: 了解如何对 Azure 磁盘备份中的备份失败进行故障排除
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737721"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>对 Azure 磁盘备份上的备份失败进行故障排除（预览版）

>[!IMPORTANT]
>Azure 磁盘备份预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅[支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此表格](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)以便注册预览版。

本文提供了有关使用 Azure 磁盘时面临的备份和还原问题的故障排除信息。 有关 [Azure 磁盘备份](disk-backup-overview.md)区域可用性、支持的方案和限制的详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

## <a name="common-issues-faced-with-azure-disk-backup"></a>使用 Azure 磁盘备份面临的常见问题

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>错误代码：UserErrorSnapshotRGSubscriptionMismatch

错误消息：为快照数据存储选择的订阅无效

建议的操作：磁盘和磁盘快照存储在同一订阅中。 可选择订阅内的任何资源组来存储磁盘快照。 选择与源磁盘相同的订阅。 有关详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

### <a name="error-code-usererrorsnapshotrgnotfound"></a>错误代码：UserErrorSnapshotRGNotFound

错误消息：无法执行此操作，因为快照数据存储资源组不存在。

建议的操作：创建资源组，并在资源组上提供所需的权限。 有关详细信息，请参阅[配置备份](backup-managed-disks.md#configure-backup)。

### <a name="error-code-usererrormanageddisknotfound"></a>错误代码：UserErrorManagedDiskNotFound

错误消息：无法执行此操作，因为托管磁盘已不存在。

建议的操作：备份将继续失败，因为源磁盘可能会被删除或移动到其他位置。 如果错误地删除了磁盘，请使用现有的还原点还原磁盘。 如果将磁盘移动到其他位置，请配置磁盘的备份。

### <a name="error-code-usererrornotenoughpermissionondisk"></a>错误代码：UserErrorNotEnoughPermissionOnDisk

错误消息：Azure 备份服务需要磁盘的其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予磁盘的相应权限。 请参阅[文档](backup-managed-disks.md)，了解备份保管库托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>错误代码：UserErrorNotEnoughPermissionOnSnapshotRG

错误消息：Azure 备份服务需要快照数据存储资源组的其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予快照数据存储资源组的相应权限。 快照数据存储资源组是存储磁盘快照的位置。 请参阅[文档](backup-managed-disks.md)了解什么是资源组、备份保管库托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>错误代码：UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

错误消息：磁盘无效或 Azure 备份服务需要磁盘的其他权限才能执行此操作

建议的操作：备份将继续失败，因为源磁盘可能会被删除或移动到其他位置。 如果错误地删除了磁盘，请使用现有的还原点还原磁盘。 如果将磁盘移动到其他位置，请配置磁盘的备份。 如果不删除或移动磁盘，请向备份保管库的托管标识授予磁盘的相应权限。 请参阅[文档](backup-managed-disks.md)，了解备份保管库的托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>错误代码：UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

错误消息：无法执行此操作，因为快照数据存储资源组已不存在。 或 Azure 备份服务需要快照数据存储资源组的其他权限才能执行此操作。

建议的操作：创建资源组并向备份保管库的托管标识授予快照数据存储资源组的相应权限。 快照数据存储资源组是存储磁盘快照的位置。 请参阅[文档](backup-managed-disks.md)，了解什么是资源组、备份保管库的托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>错误代码：UserErrorDiskBackupAuthorizationFailed

错误消息：备份保管库托管标识缺少执行此操作所需的权限。

建议的操作：向备份保管库的托管标识授予要备份的磁盘的相应权限以及存储快照的快照数据存储资源组的相应权限。 请参阅[文档](backup-managed-disks.md)，了解备份保管库的托管标识所需的权限以及如何提供这些权限。

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>错误代码：UserErrorSnapshotRGOrMSIPermissionsNotPresent

错误消息：无法执行此操作，因为快照数据存储资源组已不存在。 或者，Azure 备份服务需要快照数据存储资源组的其他权限才能执行此操作。

建议的操作：创建资源组，并向备份保管库的托管标识授予快照数据存储资源组的相应权限。 快照数据存储资源组是存储快照的位置。 请参阅[文档](backup-managed-disks.md)，了解什么是资源组、备份保管库的托管标识所需的权限，以及如何提供这些权限。

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>错误代码：UserErrorOperationalStoreParametersNotProvided

错误消息：无法执行操作，因为未提供快照数据存储资源组参数

建议的操作：提供快照数据存储资源组参数。 快照数据存储资源组是存储磁盘快照的位置。 有关详细信息，请参阅[文档](backup-managed-disks.md)。

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>错误代码：UserErrorInvalidOperationalStoreResourceGroup

错误消息：所提供的快照数据存储资源组无效

建议的操作：为快照数据存储提供有效的资源组。 快照数据存储资源组是存储磁盘快照的位置。 有关详细信息，请参阅[文档](backup-managed-disks.md)。

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>错误代码：UserErrorDiskBackupDiskTypeNotSupported

错误消息：不支持的磁盘类型

建议的操作：有关不支持的方案和限制，请参阅[支持矩阵](disk-backup-support-matrix.md)。

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>错误代码：UserErrorSameNameDiskAlreadyExists

错误消息：无法还原，因为所选目标资源组中已有同名的磁盘

建议的操作：提供用于还原的其他磁盘名称。 有关详细信息，请参阅[还原 Azure 托管磁盘](restore-managed-disks.md)。

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>错误代码：UserErrorRestoreTargetRGNotFound

错误消息：操作失败，因为目标资源组不存在。

建议的操作：提供有效的资源组以进行还原。 有关详细信息，请参阅[还原 Azure 托管磁盘](restore-managed-disks.md)。

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>错误代码：UserErrorNotEnoughPermissionOnTargetRG

错误消息：Azure 备份服务需要目标资源组的其他权限才能执行此操作。

建议的操作：向备份保管库的托管标识授予目标资源组的相应权限。 目标资源组是还原磁盘的选定位置。 请参阅[还原文档](restore-managed-disks.md)，了解备份保管库的托管标识所需的权限，以及如何提供这些权限。

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>错误代码：UserErrorSubscriptionDiskQuotaLimitReached

错误消息：操作失败，因为已达到订阅的磁盘配额上限。

建议的操作：请参阅 [Azure 订阅和服务限制以及配额文档](../azure-resource-manager/management/azure-subscription-service-limits.md)，或联系 Microsoft 支持部门获取进一步指导。

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>错误代码：UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

错误消息：操作失败，因为目标资源组不存在。 或者，Azure 备份服务需要目标资源组的其他权限才能执行此操作。

建议的操作：提供有效的资源组以进行还原，并向备份保管库的托管标识授予目标资源组的相应权限。 目标资源组是还原磁盘的选定位置。 请参阅[还原文档](restore-managed-disks.md)，了解备份保管库的托管标识所需的权限，以及如何提供这些权限。

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>错误代码：UserErrorDESKeyVaultKeyDisabled

错误消息：用于磁盘加密集的密钥保管库密钥没有处于“已启用”状态。

建议的操作：启用用于磁盘加密集的密钥保管库密钥。 请参阅[支持矩阵](disk-backup-support-matrix.md)中的限制。

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>错误代码：UserErrorMSIPermissionsNotPresentOnDES

错误消息：Azure 备份服务需要访问用于磁盘的磁盘加密集所需的权限。

建议的操作：向备份保管库的托管标识提供对磁盘加密集 (DES) 的“读取者”访问权限。

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>错误代码：UserErrorDESKeyVaultKeyNotAvailable

错误消息：无法使用用于磁盘加密集的密钥保管库密钥。

建议的操作：确保用于磁盘加密集的密钥保管库密钥没有被禁用或删除。

### <a name="error-code-usererrordisksnapshotnotfound"></a>错误代码：UserErrorDiskSnapshotNotFound

错误消息：此还原点的磁盘快照已被删除。

建议的操作：快照存储在订阅内的快照数据存储资源组中。 与所选还原点相关的快照可能已被删除或从该资源组中移出。 请考虑使用另一个恢复点进行还原。 此外，请遵循[还原文档](restore-managed-disks.md)中所述的选择快照资源组的推荐指导原则。

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>错误代码：UserErrorSnapshotMetadataNotFound

错误消息：此还原点的磁盘快照元数据已被删除

建议的操作：请考虑使用另一个恢复点进行还原。 有关详细信息，请参阅[还原文档](restore-managed-disks.md)。

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>错误代码：BackupAgentPluginHostValidateProtectionError

错误消息：在尝试配置保护的备份保管库的区域中，还无法使用磁盘备份。

建议的操作：备份保管库必须位于预览版支持的区域中。 有关区域可用性，请参阅[支持矩阵](disk-backup-support-matrix.md)。

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>错误代码：UserErrorDppDatasourceAlreadyHasBackupInstance

错误消息：尝试配置备份的磁盘已受保护。 磁盘已与备份保管库中的备份实例相关联。

建议的操作：此磁盘已与备份保管库中的备份实例相关联。 如果要重新保护此磁盘，请从当前受保护的备份保管库中删除备份实例，并在其他任何保管库中重新保护该磁盘。

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>错误代码：UserErrorDppDatasourceAlreadyProtected

错误消息：尝试配置备份的磁盘已受保护。 磁盘已与备份保管库中的备份实例相关联。

建议的操作：此磁盘已与备份保管库中的备份实例相关联。 如果要重新保护此磁盘，请从当前受保护的备份保管库中删除备份实例，并在其他任何保管库中重新保护该磁盘。

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>错误代码：UserErrorMaxConcurrentOperationLimitReached

错误消息：无法启动操作，因为已达到允许的最大并发备份数。

建议的操作：等待上一个运行的备份完成。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份支持矩阵](disk-backup-support-matrix.md)