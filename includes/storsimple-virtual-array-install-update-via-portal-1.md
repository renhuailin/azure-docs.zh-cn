---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005804"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>通过 Azure 门户安装更新

1. 转到 StorSimple 设备管理器并选择“设备”。 从连接到服务的设备列表中，选择并单击要更新的设备。

    ![在最近，将突出显示设备管理器 MySSDevManager，并选中设备，并突出显示设备 MYSSIS1103 并将其选中。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. 在“设置”边栏选项卡中，单击“设备更新”。

    ![显示 MYSSIS1103 的信息。 在 "设置" 中，将突出显示设备更新。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. 如果有可用的软件更新，会显示一条消息。 若要检查更新，还可以单击“扫描”。 记下运行的软件版本。 

    !["设备更新" 窗格中显示 "新的更新可用"，以及 "上次扫描/11/01/2017 10:56 AM/Software 版本/10.0.10289.0"。 将突出显示该版本。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    在扫描开始和成功完成时，系统会发出通知。

    ![通知显示 "扫描设备 MySVAFS110 的更新 ..."。 10:57 AM/正在进行操作。 "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 在扫描更新后，单击“下载更新”。

    ![在 "设备更新" 窗格中，显示 "新的更新可用" 消息。 将突出显示 "下载更新" 按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. 在“新更新”边栏选项卡中，查看发行说明。 另请注意，在下载更新之后，需确认安装。 单击“确定”。

    !["新的更新" 窗格显示 "下载更新后，你将需要确认安装"。 突出显示 "确定" 按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. 在上传开始和成功完成时，系统会发出通知。

     ![通知显示 "正在下载设备 MySVAFS 的更新 .。。 11:07 AM "。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. 在“设备更新”边栏选项卡中，单击“安装”。

     ![设备更新显示 "确认安装更新"。 "安装" 按钮将突出显示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. 在“新的更新”边栏选项卡中，会警告该更新会造成中断。 因为虚拟阵列是单节点设备，所以设备在更新后会重新启动。 这会中断正在进行的任何 IO。 单击“确定”以安装更新。

    !["新的更新" 窗格中的消息是 "你的设备将在安装这些更新时出现停机时间"。 突出显示 "确定" 按钮。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. 当安装作业开始时，系统会发出通知。

    ![通知显示 "正在安装设备 MySVAFS110 的更新 ..."。 11:09 AM "。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  在安装作业成功完成后，单击“设备更新”边栏选项卡中的“查看作业”链接来监视安装。 

    ![在 "设备更新" 窗格中，有一个标记为 "安装更新" 的链接。 查看作业 "。 "安装" 按钮将突出显示。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    此操作将转到“安装更新”边栏选项卡。 可以在此处查看关于该作业的详细信息。

    !["安装更新" 窗格包含安装信息，包括设备、状态、持续时间、开始时间和停止时间。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 如果使用的是运行软件版本为 Update 0.6 (10.0.10293.0) 的虚拟数组，则正在运行 Update 1 且已完成操作。 可以跳过其余步骤。 如果使用的虚拟数组运行的是 Update 0.6 (10.0.10293.0) 之前的软件版本，则现已更新到 Update 0.6。 将再显示一条消息，指出更新可用。 重复步骤 4-8 以安装 Update 1。

    !["设备更新" 窗格中显示 "新的更新可用"，以及 "上次扫描/11/1/2017 10:56 AM/Software 版本/10.0.10293.0"。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

