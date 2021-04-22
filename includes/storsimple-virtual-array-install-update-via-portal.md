---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554525"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>通过 Azure 门户安装更新

1. 转到 StorSimple 设备管理器并选择“设备”。 从连接到服务的设备列表中，选择并单击要更新的设备。 

    ![在“最新动态”中，突出显示并选中了设备管理器“MySSDevManager”、“设备”及设备“MYSSIS1103”。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. 在“设置”边栏选项卡中，单击“设备更新”。 

    ![随即显示 MYSSIS1103 的信息。 在“设置”中，突出显示了“设备更新”。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. 如果有可用的软件更新，会显示一条消息。 若要检查更新，还可以单击“扫描”。

    ![在“设备更新”窗格中，突出显示了“扫描”按钮。 其中有四行消息：上次扫描时间/未扫描/软件版本/10.0.10280.0。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    在扫描开始和成功完成时，系统会发出通知。

    ![通知显示“扫描设备 MySSIS1103 的更新。下午 2:12/已成功完成该操作”。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. 在扫描更新后，单击“下载更新”。 

    ![“设备更新”窗格显示“已有新的更新可供使用”和“上次扫描时间: 2016/11/3 下午 2:12; 软件版本: 10.0.10280.0”。 突出显示了该版本。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. 在“新的更新”边栏选项卡中，复查在下载更新后确认安装所需的信息。 单击 **“确定”** 。

    ![“新的更新”窗格显示“下载更新后，你需要确认该安装”。 突出显示了“确定”按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. 在上传开始和成功完成时，系统会发出通知。

     ![通知显示“下午 2:13，正在下载设备 MySSIS… 的更新”。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. 在“设备更新”边栏选项卡中，单击“安装”。

     ![“设备更新”窗格显示消息“确认安装更新”。 突出显示了“安装”按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. 在“新的更新”边栏选项卡中，会警告该更新会造成中断。 因为虚拟阵列是单节点设备，所以设备在更新后会重新启动。 这会中断正在进行的任何 IO。 单击“确定”以安装更新。 

    ![“新的更新”窗格中显示的消息为“安装这些更新时，你的设备将停机”。 突出显示了“确定”按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. 当安装作业开始时，系统会发出通知。 

    ![通知显示“正在安装设备 MYSSIS1103 的更新”。 下午 2:15。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  在安装作业成功完成后，单击“设备更新”边栏选项卡中的“查看作业”链接来监视安装。 

    ![在“设备更新”窗格中，突出显示了标记为“正在安装更新。 查看作业”的链接。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    这会转到“安装更新”边栏选项卡。 可以在此处查看关于该作业的详细信息。

    ![“安装更新”窗格显示安装信息，包括设备、状态、持续时间、开始时间和停止时间。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. 成功安装更新后，在“设备更新”边栏选项卡中会显示一条关于此影响的消息。 软件版本也将更改为 **10.0.10288.0**。 

    ![“设备更新”窗格显示“你的设备是最新版本”，并提供软件版本 (10.0.10288.0)。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
