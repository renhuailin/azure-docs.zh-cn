---
title: 快速入门：使用 Azure PowerShell 管理 Azure 文件共享
description: 通过本快速入门了解如何使用 Azure PowerShell 管理 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d376dbde310d3abe0521e7ce52c6fe8563142d16
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117874"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建和管理 Azure 文件共享 
本指南介绍通过 PowerShell 来使用 [Azure 文件共享](storage-files-introduction.md)的基本知识。 Azure 文件共享与其他文件共享一样，只不过是存储在云中并由 Azure 平台提供支持。 Azure 文件共享支持行业标准服务器消息块 (SMB) 协议、网络文件系统 (NFS) 协议（预览版），并可以跨多个计算机、应用程序和实例进行文件共享。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要在本地安装并使用 PowerShell，则本指南需要 Azure PowerShell 模块 Az 0.7 或更高版本。 若要找出正在运行的 Azure PowerShell 模块的版本，请执行 `Get-Module -ListAvailable Az`。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以登录到 Azure 帐户。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="create-a-resource-group"></a>创建资源组
资源组是在其中部署和管理 Azure 资源的逻辑容器。 如果没有 Azure 资源组，可以使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 新建一个。 

以下示例在“美国西部 2”区域创建名为“myResourceGroup”的资源组：

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>创建存储帐户
存储帐户是一个存储共享池，可以用来部署 Azure 文件共享。 一个存储帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到存储帐户的容量限制为止。 此示例创建常规用途版本 2（GPv2 存储帐户），该版本可以在硬盘驱动器 (HDD) 旋转介质上存储标准 Azure 文件共享或其他存储资源（例如 blob 或队列）。 Azure 文件还支持高级固态磁盘驱动器 (SSD)；高级 Azure 文件共享可在 FileStorage 存储帐户中创建。

此示例使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 创建存储帐户。 存储帐户名为 *mystorageaccount\<random number>*，对该存储帐户的引用存储在变量 **$storageAcct** 中。 存储帐户名称必须唯一，因此请使用 `Get-Random` 将一个数字追加到名称末尾，使之变得唯一。 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 超过 5 TiB（每个共享最多100 TiB）的共享仅适用于本地冗余 (LRS) 和区域冗余 (ZRS) 存储帐户。 若要创建异地冗余 (GRS) 或异地区域冗余 (GZRS) 存储帐户，请删除 `-EnableLargeFileShare` 参数。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
现在可以创建第一个 Azure 文件共享。 可以使用 [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet 创建文件共享。 此示例创建名为 `myshare` 的共享。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

共享名必须全部采用小写字母、数字和单个连字符，但不能以连字符开头。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="use-your-azure-file-share"></a>使用 Azure 文件共享
Azure 文件提供两种在 Azure 文件共享中使用文件和文件夹的方法：行业标准[服务器消息块 (SMB) 协议](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)和[文件 REST 协议](/rest/api/storageservices/file-service-rest-api)。 

若要通过 SMB 装载文件共享，请参阅下述基于 OS 的文档：
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>将 Azure 文件共享与文件 REST 协议配合使用 
可以使用文件 REST 协议（即手动进行 REST HTTP 调用），但最常见的使用文件 REST 协议的方式是使用 Azure PowerShell 模块、[Azure CLI](storage-how-to-use-files-cli.md) 或 Azure 存储 SDK，所有这些方式都可以在所选脚本/编程语言中为文件 REST 协议提供很好的包装器。  

大多数情况下，需要通过 SMB 协议来使用 Azure 文件共享，因为这样可以使用那些预期可以使用的现有应用程序和工具，但某些情况下，使用文件 REST API 比使用 SMB 更具优势，例如：

- 需要通过 PowerShell Cloud Shell（不能通过 SMB 来装载文件共享）来浏览文件共享。
- 需利用无服务器资源，例如 [Azure Functions](../../azure-functions/functions-overview.md)。 
- 你将创建将与许多 Azure 文件共享进行交互的增值服务，例如执行备份或防病毒扫描。

以下示例介绍如何使用 Azure PowerShell 模块通过文件 REST 协议来操作 Azure 文件共享。 `-Context` 参数用于检索存储帐户密钥，以便对文件共享执行指示的操作。 若要检索存储帐户密钥，必须在存储帐户上有 Azure 角色 `Owner`。

#### <a name="create-directory"></a>创建目录
若要在 Azure 文件共享的根目录中创建名为 *myDirectory* 的新目录，请使用 [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet。

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>上传文件
若要演示如何使用 [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet 来上传文件，首先需要在 PowerShell Cloud Shell 的暂存驱动器中创建要上传的文件。 

此示例将当前的日期和时间置于暂存驱动器的新文件中，然后将文件上传到文件共享。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

如果在本地运行 PowerShell，则应将 `~/CloudDrive/` 替换为计算机上的现有路径。

上传文件后，可以使用 [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet 进行检查，确保文件已上传到 Azure 文件共享。 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>下载文件
可以使用 [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet 下载刚上传到 Cloud Shell 的暂存驱动器的文件的副本。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

下载文件以后，可以使用 `Get-ChildItem` 来查看该文件是否已下载到 PowerShell Cloud Shell 的暂存驱动器。

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>复制文件
一项常见的任务是将文件从一个文件共享复制到另一个文件共享。 若要演示此功能，可以创建一个新的共享，然后使用 [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) cmdlet 将刚上传的文件复制到该新共享。 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

现在，如果列出新共享中的文件，则应看到已复制的文件。

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

虽然 `Start-AzStorageFileCopy` cmdlet 可以方便地用于 Azure 文件共享之间的临时文件移动，但对于迁移和更大的数据移动，我们建议在 Windows 上使用 `robocopy`，在 macOS 和 Linux 上使用 `rsync`。 `robocopy` 和 `rsync` 使用 SMB 而不是 FileREST API 来执行数据移动。

## <a name="create-and-manage-share-snapshots"></a>创建和管理共享快照
可以通过 Azure 文件共享执行的另一项有用的任务是创建共享快照。 快照保存的是 Azure 文件共享的某个时间点。 共享快照类似于你可能已经熟悉的操作系统技术，例如：

- 适用于 Windows 文件系统（例如 NTFS 和 ReFS）的[卷影复制服务 (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal)。
- 适用于 Linux 系统的[逻辑卷管理器 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照。
- 适用于 macOS 的 [Apple 文件系统 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照。 

可以在通过 [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) cmdlet 检索的文件共享的 PowerShell 对象上使用 `Snapshot` 方法来创建某个共享的共享快照。 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>浏览共享快照
可以浏览共享快照的内容，只需将快照引用 (`$snapshot`) 传递给 `Get-AzStorageFile` cmdlet 的 `-Share` 参数即可。

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>列出共享快照
可以使用以下命令查看为共享生成的快照的列表。

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>从共享快照还原
可以使用以前用过的 `Start-AzStorageFileCopy` 命令还原某个文件。 就本快速入门来说，首先请删除以前上传的 `SampleUpload.txt` 文件，这样才能将其从快照还原。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>删除共享快照
可以使用 [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) cmdlet 删除共享快照，其中的变量包含对 `-Share` 参数的 `$snapshot` 引用。

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>清理资源
完成后，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 删除资源组和所有相关的资源。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

也可逐一删除资源：

- 删除为本快速入门创建的 Azure 文件共享。

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > 删除 Azure 文件共享之前，必须删除创建的 Azure 文件共享的所有共享快照。

- 删除存储帐户本身（这会隐式删除已创建的 Azure 文件共享，以及任何其他可能已创建的存储资源，例如 Azure Blob 存储容器）。

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [什么是 Azure 文件？](storage-files-introduction.md)
