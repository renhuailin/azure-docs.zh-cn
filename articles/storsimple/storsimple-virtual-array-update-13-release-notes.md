---
title: Microsoft Azure StorSimple 虚拟阵列更新1.3 发行说明 |Microsoft Docs
description: 介绍运行更新1.3 的 Azure StorSimple 虚拟阵列的重要打开问题和解决方案。
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 52b30730ee8ab126521ad0760204ee48ef6a63fe
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576002"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple 虚拟阵列更新1.3 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续更新。 一旦发现需要解决的关键问题，将会添加到此处。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

更新1.3 对应于软件版本10.0.10319.0。

> [!IMPORTANT]
> - 更新1.3 是一种关键更新。 强烈建议尽快安装。
> - 只能在运行更新1.2 的设备上安装更新1.3。
> - 更新会造成中断，并且会使设备重新启动。 如果 i/o 正在进行，则设备会造成停机。 有关用于应用此更新的包的详细说明，请参阅 [下载更新 1.3](#download-update-13)。

## <a name="whats-new-in-update-13"></a>更新1.3 中的新增功能

此更新包含以下改进： KB4540725

- 传输层安全性 (TLS) 1.2 是必需更新，必须安装。 在此版本中，TLS 1.2 成为所有 Azure 门户通信的标准协议。
- 当设备和存储帐户位于两个远距离区域时，垃圾收集 bug 修复会提高垃圾回收周期的性能。
- 修复了因 blob 超时导致的备份失败。
- 更新的 OS/.NET framework 安全修补程序：
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)：2020年3月 (服务堆栈更新) 
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)：2020年7月汇总
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)：2020年7月 .NET Framework 更新

## <a name="download-update-13"></a>下载更新1。3

若要下载此更新，请前往 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx) 服务器，并下载 KB4575898 包。 此包包含以下包：

- **KB4540725**，其中包含 2012 R2 的累积 Windows 更新，最高可达3月2020。 有关此汇总中包含的内容的详细信息，请参阅 [每月3月安全汇总](https://support.microsoft.com/help/4540725)。
- **KB4565541**，其中包含 2012 R2 的累积 Windows 更新，最高可达7月2020。 有关此汇总中包含的内容的详细信息，请参阅 [每月的每月安全](https://support.microsoft.com/help/4565541)更新。
- **KB4565622**，其中包含最多7月2020版的 cumulative.NET Framework 更新。 有关此汇总中包含的内容的详细信息，请参阅 [每月的每月安全](https://support.microsoft.com/help/4565622)更新。
- **KB3011067**，其中包含设备软件更新。

下载 KB4575898，并按照这些说明 [通过本地 WEB UI 应用更新](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="known-issues-in-update-13"></a>更新1.3 中的已知问题
没有发布新问题-请在更新1.3 中注明。 所有版本记录的问题都是从以前的版本中进行的。 若要查看以前版本中包含的已知问题的摘要，请访问 [更新1.2 中的已知问题](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)。

## <a name="next-steps"></a>后续步骤
下载 KB4575898 并 [通过本地 WEB UI 应用更新](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到：

- [StorSimple 虚拟阵列更新1.2 发行说明](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple 虚拟阵列更新1.0 发行说明](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple 虚拟阵列 Update 0.6 发行说明](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple 虚拟阵列更新0.5 发行说明](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple 虚拟阵列更新0.4 发行说明](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple 虚拟阵列更新0.3 发行说明](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array 正式版发行说明](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)