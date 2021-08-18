---
title: 取消预配 Azure 文件同步服务器终结点 | Microsoft Docs
description: 有关如何根据用例取消预配 Azure 文件同步服务器终结点的指南
author: mtalasila
ms.service: storage
ms.topic: how-to
ms.date: 6/01/2021
ms.author: mtalasila
ms.subservice: files
ms.openlocfilehash: 597a3dfce5d647359b23e732f74dc6949078c000
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798732"
---
# <a name="deprovision-your-azure-file-sync-server-endpoint"></a>取消预配 Azure 文件同步服务器终结点

删除服务器终结点意味着停止通过同一同步组中的云终结点（Azure 文件共享）与该服务器位置进行同步。 在取消预配服务器终结点之前，应采取一些步骤来维护数据的完整性和可用性。 本文将按照方案依次介绍几种预配方法及相应的操作指南。 请遵循最适合你的用例的操作步骤。

如果可以接受永久丢失当前正在同步的数据，则可以跳到直接取消预配服务器终结点。

> [!Warning]
> 不要尝试通过取消预配服务器终结点来解决同步问题。 若要寻求故障排除帮助，请参阅 [Azure 文件同步故障排除](./file-sync-troubleshoot.md)。如果删除服务器终结点时没有使服务器端或云端与另一端完全同步，则数据可能会永久丢失。 删除服务器终结点是一种破坏性操作，并且在重新创建服务器终结点后，服务器终结点中的分层文件将不会“重新连接”到 Azure 文件共享上的其位置，这将导致同步错误。 另外，存在于服务器终结点命名空间之外的分层文件可能会永久丢失。 即使从未启用云分层，分层文件也可能存在于服务器终结点中。

## <a name="scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm"></a>方案 1：打算删除服务器终结点，并停止使用本地服务器/VM

此方案的目标是确保数据在云终结点中保持最新。 若要改为确保完整文件集在服务器终结点中保持最新，请参阅[方案 2：打算删除服务器终结点，并停止使用此特定的 Azure 文件共享](#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)。

属于此类别的一些用例包括：
-   迁移到 Azure 文件共享
-   构建无服务器架构
-   停止使用特定服务器终结点路径，同时保持同步组的其余部分不变

在此方案中，需要在删除服务器终结点之前执行三个步骤：删除用户访问权限，启动特殊的 VSS 上传会话，并等待最终同步会话完成。

### <a name="remove-user-access-to-your-server-endpoint"></a>删除用户对服务器终结点的访问权限

在取消预配服务器终结点之前，需要确保从服务器进行的所有更改都可以同步到云。 若要实现云同步，第一步是禁止对服务器终结点上的文件和文件夹进行进一步的更改。 

删除访问权限就意味着停机。 为减少停机时间，你还可以考虑将用户访问权限重定向到云终结点。 

记录你为自己的记录删除用户访问权限的日期和时间，然后转到下一节。

### <a name="initiate-a-special-volume-snapshot-service-vss-upload-session"></a>启动特殊的卷快照服务 (VSS) 上传会话

Azure 文件同步每天都会在服务器上创建临时 VSS 快照，以同步包含打开的句柄的文件。 若要确保最终同步会话上传最新数据并减少每个项目的错误，请启动特殊的 VSS 上传会话。 这还将在拍摄快照后，触发开启特殊同步上传会话。  

为此，请在本地服务器上打开“任务计划程序”，导航到“Microsoft\StorageSync”，然后右键单击 `VssSyncScheduledTask` 任务并选择“运行”。

> [!Important]
> 记下完成此步骤的日期和时间。 在后一个部分将要用到它。

![安排 VSS 上传会话日程的屏幕截图。](media/file-sync-server-endpoint-delete/vss-task-scheduler.png)

### <a name="wait-for-a-final-sync-upload-session-to-complete"></a>等待最终同步上传会话完成

若要确保云中有最新数据，需要等待最终同步上传会话完成。 

若要检查同步会话的状态，请在本地服务器上打开“事件查看器”。 导航到遥测事件日志（应用程序和服务\Microsoft\FileSync\代理）。 确保在手动启动 VSS 上传会话后，出现包含以下内容的 9102 事件：‘sync direction’ = upload, ‘HResult’ = 0 and ‘PerItemErrorCount’ = 0。

![查看最终同步会话是否完成的屏幕截图。](media/file-sync-server-endpoint-delete/event-viewer.png)

如果 ‘PerItemErrorCount’ 大于 0，则文件将无法同步。使用 FileSyncErrorsReport.ps1 查看未能同步的文件。此 PowerShell 脚本通常位于已安装 Azure 文件同步代理的服务器上的此路径：C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1

如果这些文件不重要，则可以删除服务器终结点。 如果这些文件很重要，请修复相关错误，并等待包含以下内容的另一个 9102 事件出现，再删除服务器终结点：‘sync direction’ = upload, ‘HResult’ = 0 and ‘PerItemErrorCount’ = 0。

## <a name="scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share"></a>方案 2：打算删除服务器终结点，并停止使用此特定的 Azure 文件共享

此方案的目标是确保你的数据在本地服务器/VM 中保持最新。 若要改为确保完整文件集在云终结点中保持最新，请参阅[方案 1：打算删除服务器终结点，并停止使用本地服务器/VM](#scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm)。

在此方案中，需要在删除服务器终结点之前执行以下 4 个步骤：禁用云分层、撤回分层文件、启动云更改检测以及等待最终同步会话完成。

### <a name="disable-cloud-tiering"></a>禁用云分层
前往打算取消预配的服务器终结点的“服务器终结点属性”，导航到“云分层”部分并禁用云分层。

### <a name="recall-all-tiered-files"></a>撤回所有分层文件
即使已禁用云分层，也需要撤回所有分层文件，以确保每个文件都存储在本地。

在撤回所有文件之前，请确保本地有充足的可用空间来存储这些文件。 可用空间需约为云中 Azure 文件共享的大小与服务器上缓存大小的差额。

使用 Invoke-StorageSyncFileRecall PowerShell cmdlet，并指定 SyncGroupName 参数以撤回所有文件。 
```powershell
Invoke-StorageSyncFileRecall  -SyncGroupName “samplesyncgroupname”
```
此 cmdlet 运行完毕后，便可以转到下一节。

### <a name="initiate-cloud-change-detection"></a>启动云更改检测
在云中启动更改检测可确保最新更改已同步。

可以通过 Invoke-AzStorageSyncChangeDetection cmdlet 启动更改检测： 

```powershell
Invoke-AzStorageSyncChangeDetection -ResourceGroupName "myResourceGroup" -StorageSyncServiceName "myStorageSyncServiceName" -SyncGroupName "mySyncGroupName" -Path "Data","Reporting\Templates" 
```

此步骤可能需要一段时间才能完成。 

> [!Important]
> 此启动的云更改检测完成扫描后，立即记下完成的日期和时间。 你需要在下一节中用到这些信息。

### <a name="wait-for-a-final-sync-session-to-complete"></a>等待最终同步会话完成
若要确保数据在本地服务器上保持最新，需要等待最终同步上传会话完成。 

若要检查会话是否完成，请打开本地服务器上的“事件查看器”。 导航到遥测事件日志（应用程序和服务\Microsoft\FileSync\代理）。 确保在日期/时间云更改检测完成后，出现包含以下内容的 9102 事件：‘sync direction’ = download, ‘HResult’ = 0 and ‘PerItemErrorCount’ = 0。

![查看最终同步会话是否完成的屏幕截图。](media/file-sync-server-endpoint-delete/event-viewer.png)

如果 ‘PerItemErrorCount’ 大于 0，则文件将无法同步。使用 FileSyncErrorsReport.ps1 查看未能同步的文件。此 PowerShell 脚本通常位于已安装 Azure 文件同步代理的服务器上的此路径：C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1

如果这些文件不重要，则可以删除服务器终结点。 如果这些文件很重要，请修复相关错误，并等待包含以下内容的另一个 9102 事件出现，再删除服务器终结点：‘sync direction’ = download, ‘HResult’ = 0 and ‘PerItemErrorCount’ = 0。

## <a name="next-steps"></a>后续步骤
* [修改 Azure 文件同步拓扑](./file-sync-modify-sync-topology.md)
