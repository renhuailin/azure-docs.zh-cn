---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: 阅读 Azure 文件同步代理的发行说明，该代理可实现将组织的文件共享集中在 Azure 文件存储中。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 9/13/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 0662431d950e0b65cce749697597e5ef9e9e8f3f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589219"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
支持以下 Azure 文件同步代理版本：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| V13 版本 - [KB4588753](https://support.microsoft.com/topic/632fb833-42ed-4e4d-8abd-746bd01c1064)| 13.0.0.0 | 2021 年 7 月 12 日 | 支持 |
| V12.1 版本 - [KB4588751](https://support.microsoft.com/topic/497dc33c-d38b-42ca-8015-01c906b96132)| 12.1.0.0 | 2021 年 5 月 20 日 | 支持 |
| V12 版本 - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 2021 年 3 月 26 日 | 支持 |
| V11.3 版本- [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 2021 年 4 月 7 日 | 支持 |
| V11.2 版本 - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2021 年 2 月 2 日 | 支持 |
| V11.1 版本 - [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 2020 年 11 月 4 日 | 支持 |

## <a name="unsupported-versions"></a>不支持的版本
以下 Azure 文件同步代理版本已到期，不再受支持：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| V10 版本 | 10.0.0.0 - 10.1.0.0 | 空值 | 不支持 - 代理版本已于 2021 年 6 月 28 日到期 |
| V9 版本 | 9.0.0.0 - 9.1.0.0 | 空值 | 不支持 - 代理版本已于 2021 年 2 月 16 日到期 |
| V8 版本 | 8.0.0.0 | 空值 | 不支持 - 代理版本已于 2021 年 1 月 12 日到期 |
| V7 版本 | 7.0.0.0 - 7.2.0.0 | 空值 | 不支持 - 代理版本已于 2020 年 9 月 1 日到期 |
| V6 版本 | 6.0.0.0 - 6.3.0.0 | 空值 | 不支持 - 代理版本已于 2020 年 4 月 21 日到期 |
| V5 版本 | 5.0.2.0 - 5.2.0.0 | 空值 | 不支持 - 代理版本已于 2020 年 3 月 18 日到期 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | 空值 | 不支持 - 代理版本已于 2019 年 11 月 6 日到期 |
| V3 版本 | 3.1.0.0 - 3.4.0.0 | 空值 | 不支持 - 代理版本已于 2019 年 8 月 19 日到期 |
| 预正式发布代理 | 1.1.0.0 - 3.0.13.0 | 空值 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-13000"></a>代理版本 13.0.0.0
以下发行说明适用于 Azure 文件同步代理版本 13.0.0.0（于 2021 年 7 月 12 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题
- 权威上传  
    - 权威上传是在同步组中创建第一个服务器终结点时可用的新模式。 此模式适用于以下场景：云（Azure 文件共享）具有部分/大部分数据，但这些数据已过时，需要与新服务器终结点上的最新数据同步。 例如，DataBox 等脱机迁移方案就是这种情况。 当 DataBox 装满数据并发送到 Azure 时，本地服务器的用户将在本地服务器上不断更改/添加/删除文件。 这使得 DataBox 和 Azure 文件共享中的数据稍微有些过时。 通过权威上传，你现在可以告知服务器和云如何解决这种情况，以及如何使云随着服务器上的最新更改无缝更新。 

        无论数据如何到达云，如果数据来自服务器上匹配的位置，此模式都可以更新 Azure 文件共享。 请确保避免在向云进行初始复制和通过权威上传更新之间进行大型目录重构。 这将确保你仅传输更新。 更改目录名称将导致这些重命名目录中的所有文件再次被上传。 此功能与“RoboCopy /MIRROR = 镜像源到目标”的语义类似，包括删除源上不再存在的目标上的文件。 
        
        权威上传通过暂存共享将 DataBox 集成的“脱机数据传输”功能替换成了 Azure 文件同步。 使用 DataBox 不再需要暂存共享了。 无法再使用 AFS V13 代理启动新的脱机数据传输作业。 服务器上的现有作业仍将继续运行，即使升级到代理版本 13 也是如此。

- 查看云更改枚举和同步进度的门户改进  
    - 创建新的同步组后，任何连接的服务器终结点只能在云更改枚举完成时开始同步。  如果文件已经存在于此同步组的云终结点（Azure文件共享）中，云中的内容更改枚举可能需要一些时间。 命名空间中存在的项（文件和文件夹）越多，此过程所花的时间就越长。 管理员现在可以在 Azure 门户中获取云更改枚举进度，从而估计从服务器开始的完成/同步的 ETA。

- 支持服务器重命名  
    - 如果重命名了已注册的服务器，Azure 文件同步将在门户中显示新的服务器名称。 如果在 v13 版本之前重命名了服务器，门户中的服务器名称现在将更新为显示正确的服务器名称。

- 对 Windows Server 2022 的支持  
    - Windows Server 2022 现在支持 Azure 文件同步代理。

    > [!Note]  
    > Windows Server 2022 添加了对 TLS 1.3 的支持，而 TLS 1.3 目前不受 Azure 文件同步的支持。如果通过组策略管理 [TLS 设置](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)，则必须将服务器配置为支持 TLS 1.2。 

- 其他改进
    - 同步、云分层和云更改枚举的可靠性改进。
    - 如果在服务器上更改了大量文件，现在将从 VSS 快照执行同步上传，这可以减少每项的错误和同步会话失败。 
    - Invoke-StorageSyncFileRecall cmdlet 现在将召回与服务器终结点关联的所有分层文件，即使该文件已移到服务器终结点位置之外。 
    - Explorer.exe 现在被排除在云分层上次访问时间跟踪之外。
    - 新增了遥测（事件 ID 6664），用于监视在删除启用了云分层的服务器终结点之后孤立分层文件清理进度。


### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](file-sync-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](file-sync-planning.md)和[如何部署 Azure 文件同步](file-sync-deployment-guide.md)。

- 如果代理版本低于版本 12.0，则需要重启现已安装 Azure 文件同步代理的服务器。
- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 此代理仅在 Windows Server 2019、Windows Server 2016、Windows Server 2012 R2 和 Windows Server 2022 上受支持。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。 有关详细信息，请参阅[推荐使用的系统资源](file-sync-planning.md#recommended-system-resources)。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](file-sync-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](file-sync-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 存储同步服务和/或存储帐户可以移动到不同的资源组、订阅或 Azure AD 租户。 存储同步服务或存储帐户移动后，你需要向 Microsoft.StorageSync 应用程序授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步有权访问存储帐户](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)）。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-12100"></a>代理版本 12.1.0.0
以下发行说明适用于 2021 年 5 月 20 日发布的 Azure 文件同步代理版本 12.1.0.0。 这些说明是对针对版本 12.0.0.0 列出的发行说明的补充。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题 
v12.0 代理版本有两个 bug 在此版本中已修复：
- 代理自动更新无法将代理更新到更高版本。
- FileSyncErrorsReport.ps1 脚本不提供单项错误的列表。

## <a name="agent-version-12000"></a>代理版本 12.0.0.0
以下发行说明适用于 Azure 文件同步代理版本 12.0.0.0（2021 年 3 月 26 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题
- 配置网络访问策略和专用终结点连接的新门户体验
    - 你现在可使用门户来禁用对存储同步服务公共终结点的访问，并批准、拒绝和删除专用终结点连接。 若要配置网络访问策略和专用终结点连接，请打开“存储同步服务”门户，转到“设置”部分，然后单击“网络”。
 
- 针对大小大于 64KiB 的卷群集的云分层支持
    - 现在，云分层在服务器 2019 上支持大小高达 2MiB 的卷群集。 若要了解详细信息，请参阅[要分层的文件的最小文件大小是多少？](./file-sync-choose-cloud-tiering-policies.md#minimum-file-size-for-a-file-to-tier)。
 
- 测量 Azure 文件同步服务和存储帐户的带宽和延迟
    - Test-StorageSyncNetworkConnectivity cmdlet 现在可用于测量 Azure 文件同步服务和存储帐户的延迟和带宽。 运行该 cmdlet 时，默认测量 Azure 文件同步服务和存储帐户的延迟。  在使用“-MeasureBandwidth”参数时测量存储帐户的上传和下载带宽。
 
        例如，若要测量 Azure 文件同步服务和存储帐户的带宽和延迟，请运行以下 PowerShell 命令：
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- 改进了服务器终结点创建失败时门户中显示的错误消息
    - 我们听取了你们的反馈，已改进在服务器终结点创建失败时显示的错误消息和指导。
 
- 其他性能和可靠性提升
    - 提高了更改检测性能，可检测 Azure 文件共享中已更改的文件。
    - 提高了协调同步会话的性能。 
    - 改进了同步，可减少 ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED 和 ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED 错误。
    - 修复了在启用云分层并使用 Robocopy 和 /B 参数复制分层文件时导致数据损坏的 bug。
    - 修复了在卷上启用重复数据删除时可能导致文件无法在服务器 2019 上分层的 bug。
    - 修复了在文件大于 2GiB 时可能导致 AFSDiag 无法压缩文件的 bug。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](file-sync-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](file-sync-planning.md)和[如何部署 Azure 文件同步](file-sync-deployment-guide.md)。

- 如果服务器现已安装 Azure 文件同步代理，则需要重启。
- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。 有关详细信息，请参阅[推荐使用的系统资源](file-sync-planning.md#recommended-system-resources)。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](file-sync-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](file-sync-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 存储同步服务和/或存储帐户可以移动到不同的资源组、订阅或 Azure AD 租户。 存储同步服务或存储帐户移动后，你需要向 Microsoft.StorageSync 应用程序授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步有权访问存储帐户](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)）。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-11300"></a>代理版本 11.3.0.0
以下发行说明适用于 2021 年 4 月 7 日发布的 Azure 文件同步代理版本 11.3.0.0。 这些说明是对针对版本 11.1.0.0 列出的发行说明的补充。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题 
修复了在启用云分层并使用 Robocopy 和 /B 参数复制分层文件时导致数据损坏的 bug。

## <a name="agent-version-11200"></a>代理版本 11.2.0.0
以下发行说明适用于 Azure 文件同步代理版本 11.2.0.0（2021 年 2 月 2 日发布）。 这些说明是对针对版本 11.1.0.0 列出的发行说明的补充。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题 
- 如果同步会话因每项错误的数量过多而取消，并且如果 Azure 文件同步服务确定需要使用自定义同步会话来更正每项错误，同步可能会在启动新会话时进行调节。
- 使用 Register-AzStorageSyncServer cmdlet 注册服务器可能会失败，并出现“未经处理的异常”错误。
- 新的 PowerShell cmdlet (Add-StorageSyncAllowedServerEndpointPath) 可在服务器上配置允许的服务器终结点路径。 如果 Azure 文件同步部署由云解决方案提供商 (CSP) 或服务提供商管理，并且客户希望在服务器上配置允许的服务器终结点路径，则此 cmdlet 非常有用。 创建服务器终结点时，如果指定的路径不在允许列表中，则服务器终结点的创建将失败。 请注意，这是一项可选功能，创建服务器终结点时，默认允许所有受支持的路径。  

    
    - 若要添加允许的服务器终结点路径，请在服务器上运行以下 PowerShell 命令：

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - 若要获取支持的路径列表，请运行以下 PowerShell 命令：
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - 若要删除某个路径，请运行以下 PowerShell 命令：
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>代理版本 11.1.0.0
以下发行说明适用于 Azure 文件同步代理版本 11.1.0.0（2020 年 11 月 4 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题
- 新的云分层模式可控制初始下载和主动召回
    - 初始下载模式：你现在可选择文件初始下载到新服务器终结点的方式。 需要按上次修改时间戳将所有文件分层或将尽可能多的文件下载到服务器上？ 可以这样做！ 不能使用云分层？ 你现在可选择在系统上不使用分层文件。 若要了解详细信息，请参阅“部署 Azure 文件同步”文档中的[创建服务器终结点](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint)部分。
    - 主动召回模式：每当创建或修改文件时，你都可以主动将其召回到在同一同步组中指定的服务器。 这样就可以在你指定的每个服务器上随时使用该文件。 全球各地的团队都在处理相同数据吗？ 启用主动召回，这样团队在第二天早上到达时，位于不同时区的团队更新的所有文件都已下载并准备就绪！ 若要了解详细信息，请参阅“部署 Azure 文件同步”文档中的[主动召回 Azure 文件共享中的新文件和更改的文件](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share)部分。

- 从云分层上次访问时间跟踪中排除应用程序。你现在可以从上次访问时间跟踪中排除应用程序。 当应用程序访问某个文件时，该文件的上次访问时间会在云分层数据库中更新。 扫描文件系统的应用程序（如防病毒软件）会导致所有文件具有相同的上次访问时间，这会影响文件的分层。

    若要从上次访问时间跟踪中排除应用程序，请将进程名称添加到位于 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 下的 HeatTrackingProcessNameExclusionList 注册表设置。

    示例：reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > 重复数据删除和文件服务器资源管理器 (FSRM) 进程默认排除（硬编码），并且进程排除列表每 5 分钟刷新一次。

- 其他性能和可靠性提升
    - 提高了更改检测性能，可检测 Azure 文件共享中已更改的文件。
    - 提高了同步上传性能。
    - 初始上传现在是从 VSS 快照执行的，这样可减少每项错误和同步会话失败。
    - 改进了某些 I/O 模式的同步可靠性。
    - 修复了一个 bug，防止在发生故障转移时同步数据库在故障转移群集上回到过去。
    - 提高了访问分层文件时的召回性能。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](../file-sync/file-sync-planning.md#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)和[如何部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持此代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。 有关详细信息，请参阅[推荐使用的系统资源](../file-sync/file-sync-planning.md#recommended-system-resources)。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](../file-sync/file-sync-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，此操作会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 若要立即同步 Azure 文件共享中已更改的文件，可使用 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 存储同步服务和/或存储帐户可以移动到不同的资源组、订阅或 Azure AD 租户。 存储同步服务或存储帐户移动后，你需要向 Microsoft.StorageSync 应用程序授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步有权访问存储帐户](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)）。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
    > [!Warning]  
    > Azure 文件同步不支持 Robocopy /B 开关。将 Robocopy /B 开关和 Azure 文件同步服务器终结点结合作为源使用可能会导致文件损坏。
