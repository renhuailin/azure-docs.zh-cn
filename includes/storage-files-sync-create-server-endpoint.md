---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 9d8d983b5ac7082ff3037d465f43ea9a2ad47fb4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300477"
---
# <a name="portal"></a>[门户](#tab/azure-portal)
要添加服务器终结点，请转到新创建的同步组，然后选择“添加服务器终结点”。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

此时“添加服务器终结点”边栏选项卡会打开，请输入以下信息以创建服务器终结点：

- **已注册的服务器**：想要创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组一部分进行同步的 Windows Server 路径。
- 云分层：启用或禁用云分层的开关。 通过云分层可以将不常使用或访问的文件分层到 Azure 文件。
- **卷可用空间**：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果在只有一个服务器终结点的卷上将卷可用空间设置为 50%，则大约一半的数据量将分层到 Azure 文件存储。 不管是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。
- 初始下载模式：这是一个可选项，如果文件存在于 Azure 文件共享中但不在服务器上，则这个选项将很有用。 例如，如果你创建服务器终结点以将另一个分支机构服务器添加到同步组中，或者在对故障服务器进行灾难恢复时，则可能会出现这种情况。 如果启用了云分层，默认情况下，最初将仅召回命名空间，而不涉及任何文件内容。 如果你认为应该由用户的访问请求来决定将哪些文件内容召回到服务器，这会很有用。 如果已禁用云分层，默认情况下，将先下载命名空间，然后将根据上次修改的时间戳召回文件，直到达到本地容量为止。 但是，可以将初始下载模式改为仅命名空间。 如果为此服务器终结点禁用了云分层，则只能使用第三种模式。 此模式避免首先召回命名空间。 文件只有在有机会完全下载时才会出现在本地服务器上。 此模式在以下情况下非常有用：应用程序需要完整文件，无法容忍其命名空间中存在分层文件。

要添加服务器终结点，请选择“创建”。 现在，文件在 Azure 文件共享和 Windows Server 之间保持保存。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
执行以下 PowerShell 命令以创建服务器终结点（请务必将 `<your-server-endpoint-path>` 和 `<your-volume-free-space>` 替换为所需值），并检查可选的[初始下载](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section)和[初始上传](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section)策略的设置。

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = "NamespaceOnly"
$initialUploadPolicy = "Merge"
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) 命令创建新的服务器终结点。

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---
