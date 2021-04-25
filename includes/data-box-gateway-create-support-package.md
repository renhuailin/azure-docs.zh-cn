---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581608"
---
如果遇到任何设备问题，可通过系统日志创建支持包。 Microsoft 支持部门使用此包来解决此问题。 按照以下步骤创建支持包：

1. [连接到设备的 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsNodeSupportPackage` 命令创建支持包。 此 cmdlet 的用法如下所示：

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    cmdlet 从设备收集日志，并将这些日志复制到指定的网络或本地共享。

    使用的参数如下：

    - `-Path` - 指定要将支持包复制到的网络或本地路径。 （必需）
    - `-Credential` - 指定用于访问受保护路径的凭据。
    - `-Zip` - 指定该参数以生成 zip 文件。
    - `-Include` - 指定该参数以包含要包含在支持包中的组件。 如果未指定，则假定为 `Default`。
    - `-IncludeArchived` - 指定该参数以在支持包中包含存档日志。
    - `-IncludePeriodicStats` - 指定该参数以在支持包中包含定期统计日志。

    
