---
title: 在 StorSimple 8000 系列设备上安装 Update 5.1 | Microsoft Docs
description: 介绍如何在 StorSimple 8000 系列设备上安装 StorSimple 8000 系列 Update 5.1。
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/21/2021
ms.author: alkohli
ms.openlocfilehash: 289ffbbd3dc23be0060ee5dbe488bf80323214e2
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897210"
---
# <a name="install-update-51-on-your-storsimple-device"></a>在 StorSimple 设备上安装 Update 5.1

## <a name="overview"></a>概述

本教程介绍如何通过 Azure 门户或使用修补程序方法在运行早期软件版本的 StorSimple 设备上安装更新 5.1。

更新 5.1 包括可通过 Azure 门户或修补程序方法应用的非中断性安全更新。

如果从 Azure 门户应用更新 5.1，则将执行手动和自动预检查，以根据硬件状态和网络连接确定设备运行状况。 仅当从 Azure 门户应用更新时，才会执行这些预检查。

如果你想要使用修补程序方法，我们强烈建议你先按照[在 StorSimple 设备上安装更新 5](storsimple-8000-install-update-5.md)中的说明安装更新 5。 然后，按照下面的[安装更新 5.1 作为修补程序](#install-update-51-as-a-hotfix)中的步骤安装更新 5.1。

更新 5.1 中的安全更新程序需要大约 30 分钟的时间进行安装。

> [!IMPORTANT]
> * Update 5.1 是必需的更新，应立即安装。 有关详细信息，请参阅 [Update 5.1 发行说明](storsimple-update51-release-notes.md)。
> * 更新 5 是受支持的最低版本。

> [!NOTE]
> * 建议通过 Azure 门户安装软件更新和其他常规更新。 <!--You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device. - N/A FOR 5.1? No maintenance mode updates, and the security updates go quickly.-->
> * 如果计划使用修补程序方法安装，则在开始安装之前，必须联系 [Microsoft 支持部门](mailto:support@microsoft.com)。

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>通过 Azure 门户安装 Update 5.1

执行以下步骤，将设备更新到 [Update 5.1](storsimple-update51-release-notes.md)。

> [!NOTE]
> Microsoft 从设备中提取其他诊断信息。 因此，当运营团队确定有问题的设备以后，我们就可以更好地从设备中收集信息并诊断问题。

#### <a name="to-install-an-update-from-the-azure-portal"></a>从 Azure 门户安装更新

1. 在“StorSimple 服务”页上，选择设备。

    ![选择设备](./media/storsimple-8000-install-update-51/update1.png)

2. 导航到“设备设置” > “设备更新”。 

    ![“设置”边栏选项卡的屏幕截图，其中标出了“设备更新”选项。](./media/storsimple-8000-install-update-51/update2.png)

3. 如果有新的更新可用，则会显示一条通知。 或者，在“设备更新”边栏选项卡中，单击“扫描更新”。 已创建一个用于扫描可用更新的作业。 在作业成功完成时，系统会发出通知。

    ![“设置”边栏选项卡（其中标出了“设备更新”选项）和“设备更新”边栏选项卡（其中标出了“有新的常规更新可用”消息）的屏幕截图。](./media/storsimple-8000-install-update-51/update3.png)

4. 建议先查看版本信息，然后在设备上应用更新。 若要应用更新，请单击“安装更新”。 在“确认常规更新”边栏选项卡中，在应用更新之前，复查要完成的先决条件。 选中指示已做好更新准备的复选框，并单击“安装”。

    ![“设备更新”边栏选项卡（其中标出了“安装更新”选项）和“确认常规更新”边栏选项卡（其中标出了“同意”选项和“安装”选项）的屏幕截图。](./media/storsimple-8000-install-update-51/update4.png)

5. 此时会开始执行一系列先决条件检查。 这些检查包括：
   
   * **控制器健康状况检查**：验证两个设备控制器是否状况良好且联机。
   * **硬件组件运行状况检查**：验证 StorSimple 设备上的所有硬件组件是否状况良好。
   * **DATA 0 检查**：验证设备上是否已启用 DATA 0。 如果未启用此接口，必须将它启用，然后重试。

     只有所有检查都成功完成后才会下载并安装更新。 在进行检查的同时，系统会通知你。 如果预检查失败，系统会提供失败原因。 请解决这些问题，然后重试操作。 如果无法自行解决这些问题，可能需要联系 Microsoft 支持人员。

6. 预检查成功完成后，将创建更新作业。 成功创建更新作业后，系统会发出通知。
   
    ![屏幕截图，其中的通知显示“正在启动软件更新作业”。](./media/storsimple-8000-install-update-51/update6.png)
   
    然后，在设备上应用更新。

7. 更新需要数小时才能完成。 随时可以选择更新作业并单击“详细信息”来查看作业详细信息。

    ![“设备更新”边栏选项卡（其中标出了“正在下载并安装软件更新”选项）以及“安装更新”边栏选项卡的屏幕截图。](./media/storsimple-8000-install-update-51/update8.png)

     还可以从“设备设置”>“作业”检索更新作业的进度。 在“作业”边栏选项卡上，可以看到更新进度。

     ![“设置”边栏选项卡（其中标出了“作业”）和“作业”边栏选项卡（其中显示了更新进度）的屏幕截图。](./media/storsimple-8000-install-update-51/update7.png)

8. 在作业完成后，导航到“设备设置”>“设备更新”。 软件版本现在应当已更新。

   验证设备是否正在运行 StorSimple 8000 系列更新 5.1 (6.3.9600.17885)，以及最后更新日期是否为当天的日期。 

## <a name="install-update-51-as-a-hotfix"></a>将更新 5.1 作为修补程序安装

如果要将更新 5.1 作为修补程序安装，请在开始安装之前执行以下步骤：

* 先安装更新 5 再安装更新 5.1。 有关说明，请参阅[在 StorSimple 设备上安装更新 5](storsimple-8000-install-update-5.md)。
* 在开始安装修补程序之前，请联系 [Microsoft 支持部门](mailto:support@microsoft.com)。

修补程序方法涉及以下步骤：

1. 从 Microsoft 更新目录中下载修补程序。
2. 安装和验证常规模式修补程序。
3. 安装和验证维护模式修补程序。

#### <a name="download-updates-for-your-device"></a>为设备下载更新

必须按规定顺序将以下修补程序下载并安装到建议的文件夹。

| 订单 | KB       | 说明 | 更新类型 | 安装时间 |在文件夹中安装|
|-------|----------|------------ |-------------|--------------|----- |
|1.     |KB4542887|软件更新<br>同时下载 _HcsSoftwareUpdate.exe_ 和 _CisMSDAgent.exe_ |常规 <br></br>非中断性 |~ 25 分钟 |FirstOrderUpdate|
|3.<sup>1, 2</sup>     |KB4037263|磁盘固件|维护 <br></br>中断性|约 30 分钟|ThirdOrderUpdate|

<sup>1</sup> 更新 5.1 中没有第二位顺序更新。

<sup>2</sup> 如果之前未在更新 5 的修补程序更新上方安装磁盘固件更新，请安装第三序位更新。

执行以下步骤，下载并安装修补程序。

#### <a name="download-hotfixes"></a>下载修补程序

若要下载修补程序，请参阅[下载修补程序](storsimple-8000-install-update-5.md#to-download-hotfixes)。

#### <a name="install-and-verify-device-updates"></a>安装并验证设备更新

按照在 StorSimple 设备上安装更新 5 中“[安装和验证常规模式修补程序](storsimple-8000-install-update-5.md#to-install-and-verify-regular-mode-hotfixes)”中的步骤，在 KB4542887 中安装设备更新。

> [!IMPORTANT]
> 如果尚未与 [Microsoft 支持部门](mailto:support@microsoft.com)联系，则必须在安装修补程序之前立即执行此操作。

按照步骤安装第一序位更新。 更新 5.1 中没有第二序位更新。

对于更新 5.1，请在安装后检查这些软件版本：

 * FriendlySoftwareVersion：StorSimple 8000 系列更新 5.1
 * HcsSoftwareVersion：6.3.9600.17885
 * CisAgentVersion：1.0.9777.0
 * MdsAgentVersion：35.2.2.0
 * Lsisas2Version：2.0.78.00

#### <a name="install-and-verify-disk-firmware-updates"></a>安装并验证磁盘固件更新

如果未在安装更新 5 时安装磁盘固件更新，请按照在 StorSimple 设备上安装更新 5 中“[安装和验证常规模式修补程序](storsimple-8000-install-update-5.md#to-install-and-verify-maintenance-mode-hotfixes)”中的步骤，在 KB4037263 中安装磁盘固件更新。

如果正在运行这些固件版本，则无需安装磁盘固件更新：`XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N003`、`0107`。

若要验证是否需要磁盘固件更新，请运行 `Get-HcsFirmwareVersion` cmdlet。

## <a name="next-steps"></a>后续步骤

详细了解 [Update 5.1 版本](storsimple-update51-release-notes.md)。
