---
title: 在现有 Azure NetApp 文件 SMB 卷上启用持续可用性 | Microsoft Docs
description: 介绍如何在现有 Azure NetApp 文件 SMB 卷上启用 SMB 持续可用性。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/18/2021
ms.author: b-juche
ms.openlocfilehash: d9af43bad8f6db6b50070368be732f20fb1fbde8
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429818"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>在现有 SMB 卷上启用连续可用性

可以在[新建 SMB 卷](azure-netapp-files-create-volumes-smb.md#continuous-availability)时启用 SMB 持续可用性 (CA) 功能。 还可以在现有 SMB 卷上启用 SMB CA；这篇文章介绍了如何执行此操作。

## <a name="considerations"></a>注意事项

* [“隐藏快照路径”](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option)选项当前对启用 CA 的 SMB 卷没有任何影响。  

* `~snapshot` 目录（可用于在其他 SMB 卷中遍历）对于启用 CA 的 SMB 卷不可见。 仍可以手动键入 `~snapshot\<snapshotName>` 来访问快照。

## <a name="steps"></a>步骤

1. 确保已[注册 SMB 持续可用性共享](https://aka.ms/anfsmbcasharespreviewsignup)功能。  
2. 单击要启用 SMB CA 的 SMB 卷。 然后单击“编辑”。  
3. 在出现的“编辑”窗口中，选中“启用持续可用性”复选框。   
    ![显示“启用持续可用性”选项的快照。](../media/azure-netapp-files/enable-continuous-availability.png)

4. 重新启动服务器。   

    > [!NOTE]
    > 仅选择“启用持续可用性”选项不会自动使现有 SMB 会话具备持续可用性。 选择该选项后，请务必重新启动服务器，使更改生效。  

5. 使用以下命令验证在安装卷的系统上启用并使用了 CA：

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    可能需要安装最新的 PowerShell 版本。 

    如果知道服务器名称，可以将 `-ServerName` 参数与命令一起使用。 请参阅 [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true) powershell 命令详细信息。

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md)