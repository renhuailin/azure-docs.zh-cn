---
title: 在 Azure NetApp 文件中使用客户端从快照还原文件 | Microsoft Docs
description: 介绍如何使用通过 Azure NetApp 文件装载卷的客户端从快照恢复文件。
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699004"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>在 Azure NetApp 文件中使用客户端从快照还原文件

[快照](snapshots-introduction.md)支持卷的时间点还原。 如果不想将[整个快照还原到卷](snapshots-restore-new-volume.md)，可以选择使用已装载卷的客户端从快照还原文件。  

装载的卷包含一个客户端可访问的快照目录，名为 `.snapshot`（在 NFS 客户端中）或 `~snapshot`（在 SMB 客户端中）。 快照目录包含对应于卷快照的子目录。 每个子目录都包含快照文件。 如果意外删除或覆盖了某个文件，可以通过将该文件从快照子目录复制到读写目录，将该文件还原到父读写目录。 

可以使用[“隐藏快照路径”选项](snapshots-edit-hide-path.md)控制对快照目录的访问。 此选项控制是否应在客户端中隐藏目录。 因此，它还控制对快照中的文件和文件夹的访问。  

NFSv4.1 不显示 `.snapshot` 目录 (`ls -la`)。 但是，如果未设置“隐藏快照路径”选项，则仍可使用客户端命令行中的 `cd <snapshot-path>` 命令通过 NFSv4.1 访问 `.snapshot` 目录。 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>使用 Linux NFS 客户端还原文件 

1. 使用 `ls` Linux 命令列出要从 `.snapshot` 目录还原的文件。 

    例如：

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. 使用 `cp` 命令将文件复制到父目录。  

    例如： 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>使用 Windows 客户端还原文件 

1. 如果卷的 `~snapshot` 目录处于隐藏状态，可通过在父目录中[显示隐藏项](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10)来显示 `~snapshot`。

    ![显示目录隐藏项的屏幕截图。](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. 导航到 `~snapshot` 中的子目录，找到要还原的文件。  右键单击该文件。 选择“复制”。  

    ![显示如何复制要还原的文件的屏幕截图。](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 返回到父目录。 在父目录中右键单击，并选择 `Paste` 将该文件粘贴到该目录。

    ![显示如何粘贴要还原的文件的屏幕截图。](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 还可以右键单击父目录，选择“属性”，单击“以前的版本”选项卡以查看快照列表，然后选择“还原”即可还原文件。  

    ![显示以前版本属性的屏幕截图。](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md) 
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照 101 视频](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)