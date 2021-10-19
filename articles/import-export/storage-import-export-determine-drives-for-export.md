---
title: 使用 Azure 导入/导出检查导出所需的驱动器数量 | Microsoft Docs
description: 了解使用 Azure 导入/导出服务进行导出所需的驱动器数量。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716256"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>使用 Azure 导入/导出检查导出所需的驱动器数量 

本文介绍如何确定为 Azure 导入/导出的导出命令提供多少个物理驱动器。 你将使用 Azure 导入/导出工具。 本文介绍如何安装和使用该工具。

Azure 导入/导出 v1 工具可列出要导出的所有 Blob，在考虑所有必要开销的情况下计算如何将其打包到指定大小的驱动器，然后估算保存 Blob 和驱动器使用情况信息所需的驱动器数量。

可以将 2.5 英寸固态硬盘、2.5 英寸的硬盘或 3.5 英寸硬盘用于导出命令。 有关详细信息，请参阅[支持的卷](storage-import-export-requirements.md#supported-disks)。


## <a name="prerequisite"></a>先决条件

* 需要在运行[受支持 OS 版本](storage-import-export-requirements.md#supported-operating-systems)的 Windows 系统上使用此工具。

## <a name="determine-how-many-drives-you-need"></a>确定所需的驱动器数

若要了解导出命令所需的物理磁盘数，请执行以下步骤：

1. 下载 Windows 系统上适用于 blob 的 Azure 导入/导出版本 1 工具 (WAImportExportV1.zip) 的当前版本。
  1. [下载 WAImportExport 版本 1](https://www.microsoft.com/download/details.aspx?id=42659)。 当前版本为 1.5.0.300。
  1. 解压缩到默认文件夹 `waimportexportv1`。 例如，`C:\WaImportExportV1`。

2. 使用管理员权限打开 PowerShell 或命令行窗口。 若要将目录切换到解压缩的文件夹，请运行以下命令：

   `cd C:\WaImportExportV1`

3. 若要检查选定 blob 所需的磁盘数量，请运行以下命令：

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    下表介绍了这些参数：

    |命令行参数|说明|
    |--------------------------|-----------------|
    |**/logdir:**|可选。 日志目录。 详细日志文件将写入此目录。 如果未指定，则使用当前目录作为日志目录。|
    |**/ExportBlobListFile:**|必需。 包含要导出的 Blob 的 Blob 路径列表或 Blob 路径前缀的 XML 文件的路径。 导入/导出服务 REST API 的[放置作业](/rest/api/storageimportexport/jobs)操作的 `BlobListBlobPath` 元素中使用的文件格式。|
    |**/DriveSize:**|必需。 用于导出作业的驱动器大小，例如 500 GB、1.5 TB。|

    请参阅 [PreviewExport 命令示例](#example-of-previewexport-command)。

4. 检查能否读取/写入要寄送的用于导出作业的驱动器。

## <a name="example-of-previewexport-command"></a>PreviewExport 命令示例

以下示例演示了 `PreviewExport` 命令：

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

导出 Blob 列表文件可能包含 Blob 名称和 Blob 前缀，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>有效 blob 路径示例

下表显示了与 blob 列表文件中的 &lt;BlobPath&gt; 和 &lt;BlobPathPrefix&gt; 标记一起使用的有效 blob 路径和 blob 路径前缀的示例。

* 路径会选择并筛选单个 Blob 或文件。
* 前缀会选择并筛选多个 Blob 或多个文件。

   | 选择器 | Blob 路径/前缀 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/book |导出任何容器中以前缀 book 开头的所有 Blob |
   | 开头为 |/music/ |导出容器 music 中的所有 Blob |
   | 开头为 |/music/love |导出容器 music 中以前缀 love 开头的所有 Blob |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob logo.bmp |
   | 等于 |videos/story.mp4 |导出容器 videos 中的 Blob story.mp4 |


### <a name="sample-output"></a>示例输出

下面是一个省略了信息性日志的输出示例：

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>后续步骤

- [从 Azure Blob 存储导出 Blob](storage-import-export-data-from-blobs.md)
