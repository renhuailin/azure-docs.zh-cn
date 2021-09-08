---
title: 快速入门 - 使用 PowerShell 创建 blob
titleSuffix: Azure Storage
description: 本快速入门将在对象 (Blob) 存储中使用 Azure PowerShell。 然后，使用该 PowerShell 将一个 Blob 上传到 Azure 存储，下载一个 Blob，然后列出容器中的 Blob。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 46b36452926b02ea524b35727d54535a4ffa3f27
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467729"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>快速入门：使用 PowerShell 上传、下载和列出 blob

使用 Azure PowerShell 模块创建和管理 Azure 资源。 可以通过 PowerShell 命令行或脚本创建或管理 Azure 资源。 本指南介绍如何使用 PowerShell 在本地磁盘和 Azure Blob 存储之间传输文件。

## <a name="prerequisites"></a>先决条件

若要访问 Azure 存储，需要一个 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

此外，你还需要存储 Blob 数据参与者角色来读取、写入和删除 Azure 存储容器和 blob。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本快速入门需要 Azure PowerShell 模块 Az 版本 0.7 或更高版本。 运行 `Get-InstalledModule -Name Az -AllVersions | select Name,Version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>创建容器

始终将 Blob 上传到容器中。 可以整理 Blob 组，就像在计算机的文件夹中整理文件一样。

设置容器名称，然后使用 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 创建容器。 将权限设置为 `blob` 以允许对文件进行公共访问。 此示例中的容器名称是 quickstartblobs。

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 用于备份 IaaS VM 的 VHD 文件是页 Blob。 将追加 Blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 Blob 存储中存储的大多数文件都是块 blob。 

要将文件上传到块 blob，请获取容器引用，然后获取对该容器中的块 blob 的引用。 具备 blob 引用后，可使用 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 将数据上传到其中。 此操作将创建 Blob（如果该 Blob 不存在），或者覆盖 Blob（如果该 Blob 存在）。

以下示例将 Image001.jpg 和 Image002.png 从本地磁盘的 D:\\_TestImages 文件夹上传到创建的容器中。

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx `
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx `
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx `
  -StandardBlobTier Archive
```

上传尽可能多的文件，然后继续操作。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) 获取容器中的 blob 列表。 此示例仅显示已上传的 blob 的名称。

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>下载 Blob

将 blob 下载到本地磁盘。 对于要下载的每个 blob，请设置名称并调用 [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) 以下载 blob。

此示例将 blob 下载到本地磁盘的 D:\\_TestImages\Downloads 中。 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 传输数据

AzCopy 命令行实用程序提供适用于 Azure 存储的高性能且可编写脚本的数据传输。 可使用 AzCopy 将数据传输到 Blob 存储和 Azure 文件存储，或将数据从其中传出。 有关 AzCopy v10（最新版 AzCopy）的详细信息，请参阅 [AzCopy 入门](../common/storage-use-azcopy-v10.md)。 若要了解如何将 AzCopy v10 与 Blob 存储配合使用，请参阅[使用 AzCopy 和 Blob 存储传输数据](../common/storage-use-azcopy-v10.md#transfer-data)。

以下示例使用 AzCopy 将本地文件上传到 blob。 请务必将示例值替换为你自己的值：

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>清理资源

删除所有已创建的资产。 删除资产的最简单方法是删除资源组。 删除资源组还会删除该组中包含的所有资源。 在以下示例中，删除资源组会删除存储帐户和资源组本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在本地文件系统和 Azure Blob 存储之间传输了文件。 若要详细了解如何使用 PowerShell 操作 Blob 存储，请浏览“适用于 Blob 存储的 Azure PowerShell 示例”。

> [!div class="nextstepaction"]
> [适用于 Azure Blob 存储的 Azure PowerShell 示例](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell 存储 cmdlet 参考

* [存储 PowerShell cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
