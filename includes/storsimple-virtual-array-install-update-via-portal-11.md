---
title: include 文件
description: include 文件
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561508"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>通过 Azure 门户安装更新

1. 转到 StorSimple 设备管理器并选择“设备”。 从连接到服务的设备列表中，选择并单击要更新的设备。

2. 在“设置”下，单击“设备更新”。  

3. 如果有可用的软件更新，会显示一条消息。 若要检查更新，还可以单击“扫描”。 记下运行的软件版本。 

    ![“设备更新”窗格显示“已有新的更新可供使用”（突出显示）和“上次扫描事件: 2018/6/22 下午 2:46；软件版本: 10.0.10296.0”。 突出显示了版本。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    在扫描开始和成功完成时，系统会发出通知。
 
4. 在扫描更新后，单击“下载更新”。 在“新的更新”下，查看发行说明。 另请注意，在下载更新之后，需确认安装。 单击 **“确定”** 。

    ![在“设备更新”中，突出显示了“下载更新”按钮。 在“新的更新”中，有一个指向发行说明的链接和一条在下载更新后确认安装的消息。 有一个“确定”按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    在上传开始和成功完成时，系统会发出通知。

5. 在“设备更新”下，单击“安装”。

     ![“设备更新”显示“确认安装更新”。 突出显示了“安装”按钮和软件版本。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. 在“新的更新”下，会警告你该更新会造成中断。 因为虚拟阵列是单节点设备，所以设备在更新后会重新启动。 这会中断正在进行的任何 IO。 单击“确定”以安装更新。

    ![“新的更新”显示“安装这些更新时，你的设备将停机”。 有一个指向发行说明的链接和一个“确定”按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    当安装作业开始时，系统会发出通知。

7.  在安装作业成功完成后，单击“查看作业”链接。 此操作将转到“安装更新”边栏选项卡。 可以在此处查看关于该作业的详细信息。 

    ![“设备更新”具有标记为“安装更新。 查看作业”的链接。](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. 如果开始使用的是运行软件版本 Update 1 (10.0.10296.0) 的虚拟阵列，则现在运行的是 Update 1.1 且已完成操作。 可以跳过其余步骤。 

    如果开始使用的是运行软件版本 Update 0.6 (10.0.10293.0) 的虚拟阵列，则现已更新到 1.0。 将再显示一条消息，指出更新可用。 重复步骤 4-7 来安装 Update 1.1。

    

