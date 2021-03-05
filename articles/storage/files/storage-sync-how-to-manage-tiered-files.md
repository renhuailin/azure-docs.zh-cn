---
title: 如何管理 Azure 文件同步分层文件 |Microsoft Docs
description: 有助于管理分层文件的提示和 PowerShell commandlet
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204299"
---
# <a name="how-to-manage-tiered-files"></a>如何管理分层文件

本文为具有与管理分层文件相关的问题的用户提供指导。 有关云分层的概念性问题，请参阅 [Azure 文件常见问题解答](storage-files-faq.md)。

## <a name="how-to-check-if-your-files-are-being-tiered"></a>如何检查是否正在对文件进行分层

每个集策略是否需要进行分层，每小时计算一次。 创建新服务器终结点时，你可以在两种情况下发生：

首次添加新的服务器终结点时，文件通常位于该服务器位置。 在云分层开始之前，需要先上传。 在完成所有文件的初始上传之前，卷可用空间策略将不会开始工作。 但是，只要文件已上传，可选的日期策略就会开始处理单独的文件。 此处还适用于一小时的时间间隔。 

添加新的服务器终结点时，可能会将空的服务器位置连接到包含数据的 Azure 文件共享。 如果选择在初始下载到服务器的过程中下载命名空间并撤回内容，则在命名空间关闭后，将根据上次修改的时间戳撤回文件，直到达到卷可用空间策略和可选的日期策略限制。

有多种查看文件是否已被分层到 Azure 文件共享的方法：
    
   *  **查看文件上的文件属性。**
     右键单击文件，转到“详细信息”，再向下滚动到“属性”属性。 分层的文件具有以下属性集：     
        
        | 属性字母 | 属性 | 定义 |
        |:----------------:|-----------|------------|
        | A | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示该文件为稀疏文件。 稀疏文件是 NTFS 提供的专用化文件类型，用于在占用空间流上的文件几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为文件会被完全分层或部分召回。 在完全分层文件中，文件流存储在云中。 在部分召回的文件中，文件的一部分已在磁盘上。 应用程序（例如，多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 此属性仅在 Windows Server 2016 及更低版本上设置。|
        | M | 回调数据访问 | 指示文件的数据不会在本地存储上全部显示。 读取文件将导致至少从服务器终结点所连接到的 Azure 文件共享提取一些文件内容。 仅在 Windows Server 2019 上设置此属性。 |
        | L | 重分析点 | 指示该文件包含重分析点。 重分析点是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义 Azure 文件同步文件系统筛选器 (StorageSync.sys) 存储文件的云位置。 这样即可支持无缝访问。 用户无需知道是否使用了 Azure 文件同步或如何获取在 Azure 文件共享中的文件访问权限。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | Offline | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![文件的“属性”对话框（“详细信息”选项卡被选中）](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > 你可以通过向文件资源管理器的表显示添加“属性”字段，查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”），选择“更多”，然后从下拉列表中选择“属性”。
        
        > [!NOTE]
        > 所有这些属性也将对部分撤回的文件可见。
        
   * **用于 `fsutil` 检查文件中的重新分析点。**
       如前面的选项中所述，分层的文件始终具有重分析点集。 重新分析点允许 (StorageSync.sys) 的 Azure 文件同步文件系统筛选器驱动程序从本地存储在服务器上的 Azure 文件共享中检索内容。 

       若要查看文件是否包含重分析点，你可以在提升的命令提示符或 PowerShell 窗口中运行 `fsutil` 实用程序：

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       如果文件包含重分析点，应能看到“重分析标记值: 0x8000001e”。 此十六进制值是 Azure 文件同步拥有的重分析点值。输出还包含用于表示 Azure 文件共享上的文件路径的重分析数据。
        
        > [!WARNING]  
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 除非 Azure 文件同步工程团队要求，否则请不要执行此命令。 运行此命令可能会导致数据丢失。 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>如何从云分层中排除应用程序上次访问时间跟踪

使用 Azure 文件同步代理版本11.1，你现在可以从上次访问跟踪中排除应用程序。 当应用程序访问文件时，会在云分层数据库中更新文件的上次访问时间。 扫描文件系统的应用程序（如防病毒软件）会导致所有文件具有相同的上次访问时间，这会影响文件的分层。

若要从上次访问时间跟踪中排除应用程序，请将进程名称添加到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下的 HeatTrackingProcessNameExclusionList 注册表设置。

示例： reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> 默认情况下会排除 FSRM) 进程资源管理器 (的重复数据删除和文件服务器。 系统每5分钟由系统接受对进程排除列表的更改。

## <a name="how-to-access-the-heat-store"></a>如何访问热度商店

云分层使用文件的上次访问时间和访问频率来确定哪些文件应分层。 云分层筛选器驱动程序 (storagesync.sys) 跟踪上次访问时间，并将信息记录到云分层热度商店中。 你可以使用服务器本地 PowerShell cmdlet 检索热度存储并将其保存到 CSV 文件中。

同一卷上的所有文件都有一个存储。 热商店可能会变得非常大。 如果只需检索项的 "最酷" 数量，请使用-Limit 和一个数字，还应考虑按子路径与卷根进行筛选。

- 导入 PowerShell 模块：   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- 卷可用空间：若要获取使用卷可用空间策略对文件进行分层的顺序，请执行以下操作：   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- 日期策略：若要获取使用日期策略对文件进行分层的顺序：   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- 查找特定文件的热度商店信息：   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- 按上次访问时间降序查看所有文件：   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- 查看通过后台召回或按需撤回到 PowerShell 时，分层文件将被召回的顺序：   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>如何强制将文件或目录分层

> [!NOTE]
> 选择要分层的目录时，只会对目录中当前的文件进行分层。 在该时间之后创建的任何文件不会自动分层。

启用云分层功能后，它会根据上次访问和修改时间自动分层文件，以实现云终结点上指定的卷可用空间百分比， 但有时你可能需要手动强制分层文件。 如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件和文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>如何将分层文件召回到磁盘

将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可以部分读取或流式处理的文件类型（例如多媒体或 .zip 文件），只需打开文件并不能确保下载整个文件。

若要确保将文件完全下载到本地磁盘，必须使用 PowerShell 强制文件完全回调。 如果希望一次撤回多个文件，例如文件夹中的所有文件，则此选项可能也很有用。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
可选参数：
* `-Order CloudTieringPolicy` 将首先撤回最近修改或访问的文件，并由当前分层策略允许。 
    * 如果配置了卷可用空间策略，则在达到 "卷可用空间" 策略设置之前，将会重新调用文件。 例如，如果 "批量可用" 策略设置为20%，则一旦卷可用空间达到20%，撤回就会停止。  
    * 如果配置了卷可用空间和日期策略，则在达到 "卷可用空间或日期" 策略设置之前，将会重新调用文件。 例如，如果 "批量可用" 策略设置为20%，日期策略为7天，则当卷可用空间达到20% 或在7天内访问或修改的所有文件均为本地时，撤回将停止。
* `-ThreadCount` 确定可并行回调的文件数。
* `-PerFileRetryCount`确定尝试重新调用当前被阻止的文件的频率。
* `-PerFileRetryDelaySeconds`确定两次重试回调尝试之间的时间间隔（以秒为单位），并且应始终结合前面的参数使用。

示例：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

> [!Note]
> 若要撤回已分层的文件，网络带宽应至少为 1 Mbps。 如果网络带宽小于 1 Mbps，则文件可能无法重新调用，出现超时错误。

## <a name="next-steps"></a>后续步骤
* [Azure 文件常见问题解答](storage-files-faq.md)
