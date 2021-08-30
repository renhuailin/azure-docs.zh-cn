---
title: 适用于 Azure 虚拟桌面的 Azure Monitor 中有哪些新增功能？
description: Azure 虚拟桌面代理的新增功能和产品更新。
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450870"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>适用于 Azure 虚拟桌面的 Azure Monitor 中有哪些新增功能？

本文介绍我们对每个适用于 Azure 虚拟桌面的 Azure Monitor 版本所做的更改。

如果你不确定当前使用的是哪个 Azure Monitor 版本，可以在“见解”页的右下角或者在配置工作簿中找到所用版本。 若要访问工作簿，请转到 [https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi)。

## <a name="how-to-read-version-numbers"></a>如何解读版本号

每个适用于 Azure 虚拟桌面的 Azure Monitor 版本号包含三个数字。 下面是每个数字的含义：

- 第一个数字是主要版本，通常用于主要发行版。

- 第二个数字是次要版本。 次要版本用于发布后向兼容的更改，例如新功能和弃用通知。

- 第三个数字是补丁版本，该版本用于发布细微更改以修复错误的行为或 bug。

例如，版本号为 1.2.31 的发行版表示它是第一个主要发行版的第二个次要版本，补丁版本号为 31。

递增其中一个数字时，其后面的所有数字也必须更改。 一个发行版有一个版本号。 但是，并非所有版本号都用于跟踪发布。 例如，补丁版本号可能有点随意。

## <a name="version-100"></a>版本 1.0.0

发行日期：2021 年 3 月 21 日。

在此版本中，我们进行了以下更改：

- 我们在主机诊断页上的 Azure 虚拟桌面代理事件日志中引入了新的视觉指示器，用于指示影响性较大的错误和警告。

- 我们从默认配置中删除了五个高开销的进程性能计数器。 有关详细信息，请参阅博客文章 [Updated guidance on Azure Monitor for Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173)（更新了有关适用于 Azure 虚拟桌面的 Azure Monitor 的指导）。

- 现在可以自动为配置工作簿完成 Windows 事件日志的设置过程。

- 配置工作簿现在支持自动部署建议的 Windows 事件日志。

- 配置工作簿现在可为资源组所在区域外部的会话主机安装 Log Analytics 代理和采用首选设置的工作区。

- 配置工作簿现在为设置过程提供选项卡式布局。

- 我们在此次更新中引入了版本控制。

## <a name="next-steps"></a>后续步骤

有关一般的“新增功能”页，请参阅 [Azure 虚拟桌面中的新增功能](whats-new.md)。

若要详细了解适用于 Azure 虚拟桌面的 Azure Monitor，请参阅[使用适用于 Azure 虚拟桌面的 Azure Monitor 监视部署](azure-monitor.md)。
