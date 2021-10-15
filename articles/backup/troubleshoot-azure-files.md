---
title: 排查 Azure 文件共享备份问题
description: 本文提供在保护 Azure 文件共享时所发生的问题的故障排除信息。
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 942574fafe6a68b601f3ed4c428dc74fb02719a5
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532503"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>排查备份 Azure 文件共享时出现的问题

本文提供故障排除信息来帮助你解决在使用 Azure 备份服务配置 Azure 文件共享的备份或还原时遇到的任何问题。

## <a name="common-configuration-issues"></a>常见配置问题

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>找不到存储帐户，因此无法配置 Azure 文件共享的备份

- 等到发现完成。
- 检查是否已使用另一恢复服务保管库对存储帐户中的任何文件共享进行保护。

  >[!NOTE]
  >一个存储帐户中的所有文件共享只能在一个恢复服务保管库中进行保护。 可以使用[此脚本](scripts/backup-powershell-script-find-recovery-services-vault.md)查找存储帐户所注册到的恢复服务保管库。

- 确保文件共享不是在任何不受支持的存储帐户中。 可以参考 [Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)来查找支持的存储帐户。
- 请确保存储帐户和恢复服务保管库位于同一区域。
- 确保在使用新存储帐户时，存储帐户名称和资源组名称的总长度不超过 84 个字符；使用经典存储帐户时，总长度不超过 77 个字符。
- 检查存储帐户的防火墙设置，确保已授予“允许受信任服务列表中的 Azure 服务访问此存储帐户”例外。 有关授予例外的步骤，可参阅[此链接](/azure/storage/common/storage-network-security?tabs=azure-portal#manage-exceptions)。


### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>门户中的错误指出无法发现存储帐户

如果你使用的是合作伙伴订阅（已启用 CSP），请忽略该错误。 如果你的订阅未启用 CSP，且无法发现你的存储帐户，请联系支持部门。

### <a name="selected-storage-account-validation-or-registration-failed"></a>所选存储帐户验证或注册失败

重试注册。 如果问题仍然存在，请联系支持部门。

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>无法在所选存储帐户中列出或找到文件共享

- 确保存储帐户存在于资源组中，且自上次在保管库中进行验证或注册后尚未删除或移动。
- 确保未删除你要保护的文件共享。
- 确保存储帐户是支持进行文件共享备份的存储帐户。 可以参考 [Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)来查找支持的存储帐户。
- 检查是否已在同一恢复服务保管库中对文件共享进行保护。
- 检查存储帐户的网络路由设置，确保将路由首选项设置为 Microsoft 网络路由。

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>备份文件共享配置（或保护策略配置）失败

- 重试配置，看看该问题是否仍旧出现。
- 确保未删除要保护的文件共享。
- 如果你正在尝试同时保护多个文件共享时部分文件共享失败，请重试配置已失败的文件共享的备份。

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>在取消保护文件共享后无法删除恢复服务保管库

在 Azure 门户中，打开“保管库” > “备份基础结构” > “存储帐户”  。 选择“取消注册”以从恢复服务保管库中删除存储帐户。

>[!NOTE]
>若要删除某个恢复服务保管库，必须先取消注册已注册到该保管库的所有存储帐户。

## <a name="common-backup-or-restore-errors"></a>常见的备份或还原错误

>[!NOTE]
>请参阅[此文档](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup)，确保你有足够的权限执行备份或还原操作。

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound - 由于找不到文件共享，操作失败

错误代码：FileShareNotFound

错误消息：由于找不到文件共享，操作失败

确保未删除你要保护的文件共享。

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable - 存储帐户找不到或不受支持

错误代码：UserErrorFileShareEndpointUnreachable

错误消息：存储帐户找不到或不受支持

- 确保存储帐户存在于资源组中，且自上次验证后未从资源组中删除或移除。

- 确保存储帐户是支持进行文件共享备份的存储帐户。

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached - 你已达到此文件共享的最大快照数限制，在旧快照过期后才能继续生成快照

错误代码：AFSMaxSnapshotReached

错误消息：你已达到此文件共享的最大快照数限制，在旧快照过期后才能继续生成快照。

- 为文件共享创建多个按需备份时，可能发生此错误。
- 每个文件共享的快照数限制为 200 个，包括通过 Azure 备份生成的快照。 较旧的计划备份（或快照）会自动清除。 如果达到最大限制，则必须删除按需备份（或快照）。

从 Azure 文件门户删除按需备份（Azure 文件共享快照）。

>[!NOTE]
> 如果删除 Azure 备份创建的快照，会失去恢复点。

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound - 由于指定的存储帐户不再存在，操作失败

错误代码：UserErrorStorageAccountNotFound

错误消息：由于指定的存储帐户不再存在，操作失败。

确保该存储帐户仍然存在，而未被删除。

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound - 提供的存储帐户详细信息不正确

错误代码：UserErrorDTSStorageAccountNotFound

错误消息：提供的存储帐户详细信息不正确。

确保该存储帐户仍然存在，而未被删除。

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound - 资源组不存在

错误代码：UserErrorResourceGroupNotFound

错误消息：资源组不存在

选择现有资源组或创建新资源组。

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest - 针对此文件共享的备份作业已在进行中

错误代码：ParallelSnapshotRequest

错误消息：针对此文件共享的备份作业已在进行中。

- 文件共享备份不支持针对同一文件共享的并行快照请求。

- 等待现有备份作业完成，然后重试。 如果在恢复服务保管库中找不到某个备份作业，请查看同一订阅中的其他恢复服务保管库。

### <a name="usererrorstorageaccountinternetroutingnotsupported--storage-accounts-with-internet-routing-configuration-are-not-supported-by-azure-backup"></a>UserErrorStorageAccountInternetRoutingNotSupported- Azure 备份不支持具有 Internet 路由配置的存储帐户

错误代码：UserErrorStorageAccountInternetRoutingNotSupported

错误消息：Azure 备份不支持具有 Internet 路由配置的存储帐户

请确保为托管备份文件共享的存储帐户设置的路由首选项为 Microsoft 网络路由。

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling - 文件共享备份或还原因存储服务限制而失败。 这可能是因为存储服务正忙于处理给定存储帐户的其他请求

错误代码：FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

错误消息：文件共享备份或还原因存储服务限制而失败。 这可能是因为存储服务正忙于处理给定存储帐户的其他请求。

稍后尝试备份/还原操作。

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound - 找不到目标文件共享

错误代码：TargetFileShareNotFound

错误消息：找不到目标文件共享。

- 确保所选存储帐户存在，且目标文件共享未被删除。

- 确保存储帐户是支持进行文件共享备份的存储帐户。

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked - 由于存储帐户处于锁定状态，备份或还原作业失败

错误代码：UserErrorStorageAccountIsLocked

错误消息：由于存储帐户处于锁定状态，备份或还原作业失败。

删除对存储帐户的锁定，或使用删除锁定而不是读取锁定，然后重试备份或还原操作 。

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached - 由于失败的文件数超过阈值，恢复失败

错误代码：DataTransferServiceCoFLimitReached

错误消息：由于失败的文件数超过阈值，恢复失败。

- 恢复失败原因已在文件中列出（作业详细信息中提供了路径）。 解决导致失败的问题，然后只对失败的文件重试还原操作。

- 文件还原失败的常见原因：

  - 失败的文件当前正在使用中
  - 父目录中存在与失败的文件同名的目录。

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover - 由于没有可恢复的文件，恢复失败

错误代码：DataTransferServiceAllFilesFailedToRecover

错误消息：由于没有可恢复的文件，恢复失败。

- 恢复失败原因已在文件中列出（作业详细信息中提供了路径）。 解决导致失败的问题，然后只对故障文件重试还原操作。

- 文件还原失败的常见原因：

  - 失败的文件当前正在使用中
  - 父目录中存在与失败的文件同名的目录。

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - 由于源中的某个文件不存在，还原失败

错误代码：DataTransferServiceSourceUriNotValid

错误消息：由于源中的某个文件不存在，还原失败。

- 所选项不在恢复点数据中。 若要恢复这些文件，请提供正确的文件列表。
- 与恢复点对应的文件共享快照已手动删除。 请选择另一恢复点，然后重试还原操作。

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked - 正在对同一目标运行某个恢复作业

错误代码：UserErrorDTSDestLocked

错误消息：正在对同一目标运行某个恢复作业。

- 文件共享备份不支持并行恢复到同一目标文件共享。

- 等待现有恢复完成，然后再试一次。 如果在恢复服务保管库中找不到某个恢复作业，请查看同一订阅中的其他恢复服务保管库。

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull - 由于目标文件共享已满，还原操作失败

错误代码：UserErrorTargetFileShareFull

错误消息：由于目标文件共享已满，还原操作失败。

提高目标文件共享大小配额，使之能够容纳还原数据，然后重试还原操作。

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient - 目标文件共享没有足够的存储大小配额用于执行还原

错误代码：UserErrorTargetFileShareQuotaNotSufficient

错误消息：目标文件共享没有足够的存储大小配额用于执行还原

提高目标文件共享大小配额，使之能够容纳还原数据，然后重试操作

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed - 由于对与目标文件共享关联的文件同步服务资源执行预还原操作时出错，还原操作失败

错误代码：File Sync PreRestoreFailed

错误消息：由于对与目标文件共享关联的文件同步服务资源执行预还原操作时出错，还原操作失败。

请稍后尝试还原数据。 如果问题持续出现，请联系 Microsoft 支持。

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress - 正在对目标文件共享进行 Azure 文件同步服务更改检测。 上一个还原到目标文件共享的操作触发了更改检测

错误代码：AzureFileSyncChangeDetectionInProgress

错误消息：正在对目标文件共享进行 Azure 文件同步服务更改检测。 上一个还原到目标文件共享的操作触发了更改检测。

使用其他目标文件共享。 或者，可以等待 Azure 文件同步服务更改检测以完成目标文件共享，然后重试还原。

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored - 一个或多个文件无法成功恢复。 有关详细信息，请查看上面提供的路径中的失败文件列表

错误代码：UserErrorAFSRecoverySomeFilesNotRestored

错误消息：一个或多个文件无法成功恢复。 有关详细信息，请查看上面给出的路径中的故障文件列表。

- 恢复失败原因已在文件中列出（作业详细信息中提供了路径）。 解决失败原因，然后只对失败的文件重试还原操作。
- 文件还原失败的常见原因：

  - 失败的文件当前正在使用中
  - 父目录中存在与失败的文件同名的目录。

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound - 找不到对应于恢复点的 Azure 文件共享快照

错误代码：UserErrorAFSSourceSnapshotNotFound

错误消息：找不到对应于恢复点的 Azure 文件共享快照

- 确保与你要用于恢复的恢复点对应的文件共享快照仍然存在。

  >[!NOTE]
  >如果你删除由 Azure 备份创建的文件共享快照，则对应的恢复点将不可用。 建议不要删除快照以确保能够恢复。

- 尝试选择另一个还原点来恢复数据。

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget - 正在对同一目标文件共享执行另一个还原作业

错误代码：UserErrorAnotherRestoreInProgressOnSameTarget

错误消息：正在对同一目标文件共享执行另一个还原作业

使用其他目标文件共享。 或者，可进行取消或等待另一还原操作完成。

## <a name="common-modify-policy-errors"></a>常见的修改策略错误

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation - 正在对此项执行另一个配置保护操作

错误代码：BMSUserErrorConflictingProtectionOperation

错误消息：正在对此项执行另一个配置保护操作。

等待上一个修改策略操作完成，并稍后重试。

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked - 正在对所选的项执行另一操作

错误代码：BMSUserErrorObjectLocked

错误消息：正在对所选的项执行另一操作。

等待另一个正在进行的操作完成，并稍后重试。

## <a name="common-soft-delete-related-errors"></a>常见的软删除相关错误

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState - 此还原点不可用，因为与它关联的快照位于处于软删除状态的文件共享中

错误代码：UserErrorRestoreAFSInSoftDeleteState

错误消息：此还原点不可用，因为与它关联的快照位于处于软删除状态的文件共享中。

当文件共享处于软删除状态时无法执行还原操作。 在“文件”门户中取消删除文件共享，或使用[取消删除脚本](scripts/backup-powershell-script-undelete-file-share.md)，然后尝试还原。

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState - 列出的还原点不可用，因为已永久删除包含还原点快照的关联文件共享

错误代码：UserErrorRestoreAFSInDeleteState

错误消息：列出的还原点不可用，因为已永久删除包含还原点快照的关联文件共享。

检查是否删除了已备份的文件共享。 如果该文件共享处于软删除状态，请检查软删除保持期是否已过，且软删除未重新启用。 只要存在上述这两种情况之一，你就会永久丢失所有快照，且无法恢复数据。

>[!NOTE]
> 建议不要删除已备份的文件共享，或者，如果文件共享处于软删除状态，请在软删除保持期结束之前将其取消删除，以避免丢失所有还原点。

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - 由于 Azure 文件共享处于软删除状态，备份失败

错误代码：UserErrorBackupAFSInSoftDeleteState

错误消息：由于 Azure 文件共享处于软删除状态，备份失败

在“文件”门户中取消删除文件共享，或使用[取消删除脚本](scripts/backup-powershell-script-undelete-file-share.md)继续备份，并防止永久删除数据。

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState - 由于永久删除了关联的 Azure 文件共享，备份失败

错误代码：UserErrorBackupAFSInDeleteState

错误消息：由于永久删除了关联的 Azure 文件共享，备份失败

检查是否永久删除了已备份的文件共享。 如果是，请停止该文件共享的备份，以免备份反复失败。 若要了解如何停止保护，请参阅[停止保护 Azure 文件共享](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>后续步骤

有关备份 Azure 文件共享的详细信息，请参阅：

- [备份 Azure 文件共享](backup-afs.md)
- [备份 Azure 文件共享常见问题解答](backup-azure-files-faq.yml)
