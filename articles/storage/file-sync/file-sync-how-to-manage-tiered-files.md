---
title: 如何管理 Azure 文件同步分层文件 | Microsoft Docs
description: 帮助管理分层文件的提示和 PowerShell commandlet
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0feb7f6fc9a36e3440001d3a344ada843ea54e0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645695"
---
# <a name="how-to-manage-tiered-files"></a>如何管理分层的文件

本文为遇到与管理分层文件相关问题的用户提供指导。 有关云分层的概念性问题，请参阅 [Azure 文件存储常见问题解答](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)。

## <a name="how-to-check-if-your-files-are-being-tiered"></a>如何检查是否在对文件进行分层

每小时评估一次是否需要按设置的策略对文件进行分层。 创建新服务器终结点时，可能会遇到两种情况：

首次添加新服务器终结点时，文件通常存在于该服务器位置。 需要先上传它们，然后才能开始云分层。 在完成所有文件的初始上传之前，卷可用空间策略不会开始发挥作用。 但是，一旦文件已上传，可选日期策略便会立即开始基于单独文件发挥作用。 一小时间隔也适用于此处。

添加新服务器终结点时，用户可能会将空服务器位置连接到包含数据的 Azure 文件共享。 如果选择在初次下载到服务器的过程中下载命名空间并召回内容，则在命名空间降临后，会根据上次修改时间戳召回文件，直到达到卷可用空间策略和可选日期策略限制。

有多种查看文件是否已被分层到 Azure 文件共享的方法：

   - **查看文件上的文件属性。**
     右键单击文件，转到“详细信息”，再向下滚动到“属性”属性。 分层的文件具有以下属性集：

        | 属性字母 | 属性 | 定义 |
        |:----------------:|-----------|------------|
        | A | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示该文件为稀疏文件。 稀疏文件是 NTFS 提供的专用化文件类型，用于在占用空间流上的文件几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为文件会被完全分层或部分召回。 在完全分层文件中，文件流存储在云中。 在部分召回的文件中，文件的一部分已在磁盘上。 应用程序（例如，多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 此属性仅在 Windows Server 2016 及更低版本上设置。|
        | M | 进行数据访问时召回 | 指示文件的数据未完全存在于本地存储中。 读取文件会导致至少从服务器终结点所连接到的 Azure 文件共享提取部分文件内容。 此属性仅在 Windows Server 2019 上设置。 |
        | L | 重分析点 | 指示该文件包含重分析点。 重分析点是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义 Azure 文件同步文件系统筛选器 (StorageSync.sys) 存储文件的云位置。 这样即可支持无缝访问。 用户无需知道是否使用了 Azure 文件同步或如何获取在 Azure 文件共享中的文件访问权限。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | Offline | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![文件的“属性”对话框（“详细信息”选项卡被选中）](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)


        > [!NOTE]
        > 你可以通过向文件资源管理器的表显示添加“属性”字段，查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”），选择“更多”，然后从下拉列表中选择“属性”。

        > [!NOTE]
        > 所有这些属性也将对部分召回的文件可见。

   - “使用 `fsutil` 检查文件上的重分析点”。
       如前面的选项中所述，分层的文件始终具有重分析点集。 重分析点使 Azure 文件同步文件系统筛选器驱动程序 (StorageSync.sys) 可以从未以本地方式存储在服务器上的 Azure 文件共享检索内容。

       若要查看文件是否包含重分析点，你可以在提升的命令提示符或 PowerShell 窗口中运行 `fsutil` 实用程序：

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

       如果文件包含重分析点，应能看到“重分析标记值: 0x8000001e”。 该十六进制值是 Azure 文件同步拥有的重分析点值。输出还会包含表示 Azure 文件共享中文件路径的重分析数据。

        > [!WARNING]
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 除非 Azure 文件同步工程团队要求，否则请不要执行此命令。 运行此命令可能会导致数据丢失。

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>如何从云分层中上次访问时间跟踪中排除应用程序

当应用程序访问某个文件时，该文件的上次访问时间会在云分层数据库中更新。 扫描文件系统的应用程序（如防病毒软件）会导致所有文件具有相同的上次访问时间，这会影响文件的分层。

若要从上次访问时间跟踪中排除应用程序，请将进程名称添加到位于 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下的相应注册表设置。

对于 v11 和 v12 版本，请将进程排除项添加到 HeatTrackingProcessNameExclusionList 注册表设置中。
示例：reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

对于 v13 版本及更新版本，将进程排除项添加到 HeatTrackingProcessNamesExclusionList 注册表设置中。
示例：reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNamesExclusionList /t REG_SZ /d "SampleApp.exe,AnotherApp.exe" /f

> [!NOTE]
> 重复数据删除和文件服务器资源管理器 (FSRM) 进程将默认排除。 系统每 5 分钟接受一次对进程排除列表的更改。

## <a name="how-to-access-the-heat-store"></a>如何访问热度存储

云分层使用文件的上次访问时间和访问频率来确定应进行分层的文件。 云分层筛选器驱动程序 (storagesync.sys) 会跟踪上次访问时间，并将信息记录在云分层热度存储中。 可以使用服务器-本地 PowerShell cmdlet 检索热度存储并将它保存到 CSV 文件中。

同一卷上的所有文件使用单个热度存储。 热度存储可能会变得非常大。 如果只需检索“最冷”的项数，请使用 -Limit 和一个数字，另外请考虑按子路径与卷根进行筛选。

- 导入 PowerShell 模块：`Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- 卷可用空间：若要获取使用卷可用空间策略对文件进行分层的顺序，请执行以下命令：`Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- 日期策略：若要获取使用日期策略对文件进行分层的顺序，请执行以下命令：`Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- 查找特定文件的热度存储信息：`Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- 按上次访问时间降序查看所有文件：`Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- 查看通过 PowerShell 由后台召回或按需召回来召回分层文件的顺序：`Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>如何强制对文件或目录进行分层

> [!NOTE]
> 选择要进行分层的目录时，只会对当时处于该目录中的文件进行分层。 在该时间之后创建的任何文件都不会自动进行分层。

启用云分层功能后，它会根据上次访问和修改时间自动分层文件，以实现云终结点上指定的卷可用空间百分比， 但有时你可能需要手动强制分层文件。 如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件和文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>如何将分层文件召回到磁盘

将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可被部分读取或流式传输的文件类型（如多媒体文件或 .zip 文件），仅仅打开文件不会确保下载整个文件。

若要确保将文件完整下载到本地磁盘，必须使用 PowerShell 强制完整召回文件。 在需要同时召回多个文件（例如，一个文件夹内的所有文件）的情况下，也可能适合使用此选项。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

可选参数：
- `-Order CloudTieringPolicy` 会首先召回最近修改或访问的文件，并由当前分层策略所允许。 
    * 如果配置了卷可用空间策略，则会召回文件，直到达到卷可用空间策略设置。 例如，如果卷可用空间策略设置为 20%，则一旦卷可用空间达到 20%，召回便会停止。  
    * 如果配置了卷可用空间和日期策略，则会召回文件，直到达到卷可用空间或日期策略设置。 例如，如果卷可用空间策略设置为 20%，日期策略为 7天，则一旦卷可用空间达到 20% 或是 7 天内访问或修改的所有文件都在本地，召回便会停止。
- `-ThreadCount` 确定可并行召回的文件数。
- `-PerFileRetryCount` 确定尝试召回当前被阻止的文件的频率。
- `-PerFileRetryDelaySeconds` 确定重试召回尝试之间的时间间隔（以秒为单位），并且此参数应始终与上一参数结合使用。

示例：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
```

> [!NOTE]  
> - 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。

> [!NOTE]
> 若要召回已分层的文件，网络带宽应至少为 1 Mbps。 如果网络带宽小于 1 Mbps，则文件可能无法召回，出现超时错误。

## <a name="next-steps"></a>后续步骤

- [有关 Azure 文件的常见问题解答 (FAQ)](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
