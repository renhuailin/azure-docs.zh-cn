---
title: Microsoft Azure StorSimple 虚拟阵列 Update 1.2 发行说明 | Microsoft Docs
description: 介绍了 StorSimple 虚拟阵列运行 Update 1.2 时遇到的重要待解决问题和解决方法。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960183"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple 虚拟阵列 Update 1.2 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续更新。 一旦发现需要解决的关键问题，将会添加到此处。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

Update 1.2 对应于软件版本 10.0.10311.0。

> [!IMPORTANT]
> - 更新会造成中断，并且会使设备重新启动。 如果正在进行 I/O，则会导致设备停机。 有关用于应用此项更新的包的详细说明，请参阅[下载 Update 1.2](#download-update-12)。
> - 如果设备正在运行 Update 1.0 或 1.1，则仅可通过 Azure 门户获取 Update 1.2。

## <a name="whats-new-in-update-12"></a>Update 1.2 中的新增功能

此更新包含以下 bug 修复：

- 提高了处理已删除文件时的复原能力。
- 改进了代码启动路径中的异常处理，从而减少了备份、还原、云读取和自动空间回收中的故障。

## <a name="download-update-12"></a>下载 Update 1.2

若要下载此项更新，请转到 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx)服务器并下载 KB4502035 包。 此包中有以下包：

 - KB4493446，其中包含 2012 R2 至 2019 年 4 月版的累积 Windows 更新。 有关此汇总所含内容的详细信息，请参阅 [4 月月度安全性汇总](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - KB3011067，它是 Microsoft 更新独立程序包文件 WindowsTH-KB3011067-x64。 此文件用于更新设备软件。

下载 KB4502035，并按照[通过本地 Web UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)的说明操作。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修复的问题

下表提供了此版本中所修复问题的摘要。

| 不是。 | 功能 | 问题 |
| --- | --- | --- |
| 1 |删除| 在早期版本的软件中，若即使文件被删除，设备的使用也不会更改，则会出现一个问题。 在此版本中已修复了此问题。 在处理已删除的文件时，分层代码路径具有更高的复原能力。|
| 2 |异常处理| 在早期版本的软件中，系统重启后会出现问题，可能会导致备份、还原、从云中读取以及自动回收空间时出现故障。 此版本更改了在启动路径中处理异常的方式。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知问题

Update 1.2 的发行说明中未提到新的问题。 在发行说明中提到的所有问题都是以前的版本携带过来的。 若要查看以前版本中存在的已知问题的摘要，请参阅 [Update 1.1 中的已知问题](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>后续步骤

下载 KB4502035 并[通过本地 Web UI 应用更新](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>参考

查找较旧版本的发行说明？ 请转到：
* [StorSimple 虚拟阵列 Update 1.1 发行说明](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple 虚拟阵列 Update 1.0 发行说明](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple 虚拟阵列 Update 0.6 发行说明](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array 更新 0.5 发行说明](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple 虚拟阵列更新 0.4 发行说明](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 发行说明](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式版发行说明](./storsimple-virtual-array-update-06-release-notes.md)