---
title: Microsoft Azure StorSimple 虚拟阵列 Update 1.3 发行说明 | Microsoft Docs
description: 介绍运行 Update 1.3 的 Azure StorSimple 虚拟阵列的严重待解决问题和解决方法。
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389513"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple 虚拟阵列 Update 1.3 发行说明

以下发行说明标识了 Microsoft Azure StorSimple Virtual Array 更新待解决的重要问题和已解决的问题。

发行说明会持续更新。 一旦发现需要解决的关键问题，将会添加到此处。 部署 StorSimple Virtual Array 之前，请仔细查看发行说明中包含的信息。

Update 1.3 对应于软件版本 10.0.10319.0。

> [!IMPORTANT]
> - Update 1.3 属于关键更新。 我们强烈建议尽快安装该更新。
> - 只能在运行 Update 1.2 的设备上安装 Update 1.3。
> - 更新会造成中断，并且会使设备重新启动。 如果有 I/O 正在进行，设备将发生停机。 有关用于应用此项更新的包的详细说明，请参阅[下载 Update 1.3](#download-update-13)。

## <a name="whats-new-in-update-13"></a>Update 1.3 中的新增功能

此项更新包含以下改进：KB4540725

- 传输层安全性 (TLS) 1.2 是必需的更新，必须予以安装。 从此版本开始，TLS 1.2 将成为所有 Azure 门户通信的标准协议。
  
   如果看到以下警告，则必须更新设备上的软件，然后才能继续：

   一个或多个 StorSimple 设备运行的是较低版本的软件。 TLS 1.2 的最新可用更新是必需更新，应立即安装到这些设备上。 所有 Azure 门户通信中都要使用 TLS 1.2，如果没有此更新，设备将无法与 StorSimple 服务通信。

- 垃圾回收 bug 修复改进了当设备和存储帐户位于两个相距较远的区域时垃圾回收周期的性能。
- 修复了因 Blob 超时而导致的备份失败。
- 更新了 OS/.NET Framework 安全修补程序：
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)：2020 年 3 月 SSU（服务堆栈更新）
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)：2020 年 7 月汇总包
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)：2020 年 7 月 .NET Framework 更新

## <a name="download-update-13"></a>下载 Update 1.3

若要下载此项更新，请转到 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx)服务器并下载 KB4575898 包。 此包中有以下包。 按以下顺序安装这些包：

1. **KB4540725**，其中包含 2012 R2 至 2020 年 3 月版的累积 Windows 更新。 有关此汇总包所含内容的详细信息，请参阅 [3 月月度安全汇总](https://support.microsoft.com/help/4540725)。
1. **KB4565541**，其中包含 2012 R2 至 2020 年 7 月版的累积 Windows 更新。 有关此汇总所含内容的详细信息，请转往查看 [7 月月度安全性汇总](https://support.microsoft.com/help/4565541)。
1. **KB4565622**，其中包含直至 2020 年 7 月版的累积 .NET Framework 更新。 有关此汇总所含内容的详细信息，请转往查看 [KB4565622](https://support.microsoft.com/help/4565622)。<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067**，其中包含设备软件更新。

下载 KB4575898，并按照[通过本地 Web UI 应用更新](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)的说明操作。

## <a name="known-issues-in-update-13"></a>Update 1.3 中的已知问题
Update 1.3 的发行说明中未提到新的问题。 在发行说明中提到的所有问题都是以前的版本携带过来的。 若要查看以前版本中存在的已知问题的摘要，请参阅 [Update 1.2 中的已知问题](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)。

## <a name="next-steps"></a>后续步骤
下载 KB4575898 并[通过本地 Web UI 应用更新](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)。

## <a name="references"></a>参考
查找较旧版本的发行说明？ 请转到：

- [StorSimple 虚拟阵列 Update 1.2 发行说明](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple 虚拟阵列 Update 1.0 发行说明](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple 虚拟阵列 Update 0.6 发行说明](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array 更新 0.5 发行说明](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple 虚拟阵列更新 0.4 发行说明](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 发行说明](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array 更新 0.1 和 0.2 发行说明](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array 正式版发行说明](./storsimple-virtual-array-update-06-release-notes.md)