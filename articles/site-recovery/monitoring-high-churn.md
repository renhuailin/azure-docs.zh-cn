---
title: 监视虚拟机上的代码改动模式
description: 了解如何在使用 Azure Site Recovery 保护的虚拟机上监视代码改动模式
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359860"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>监视虚拟机上的代码改动模式

本文概述了可用于监视虚拟机上的代码改动模式的各种工具。 通过使用适当的工具，可轻松精准地找出导致大量改动的应用程序，然后可对该应用程序执行进一步的操作。

## <a name="for-azure-virtual-machines-windows-or-linux"></a>对于 Azure 虚拟机（Windows 或 Linux）

如果计算机托管在 Azure 中，并且使用托管或非托管磁盘进行存储，则可通过跟踪磁盘指标轻松跟踪性能。 这样，你能够密切监视并根据应用程序使用模式选择适当的磁盘。 你还可用它来创建警报、诊断和生成自动化。 [了解详细信息](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

使用 Azure Site Recovery 保护计算机后，可使用 Azure Monitor 日志和 Log Analytics 监视计算机。 [了解详细信息](./monitor-log-analytics.md)。

还有一些特定于操作系统的工具可供使用。

## <a name="for-windows-machines"></a>对于 Windows 计算机

如果有一台运行 Windows 操作系统的计算机（无论是否在本地），则有其他一些工具可供使用。

除了在任务管理器上检查磁盘使用情况之外，还始终可参考资源监视器和性能监视器 。 Windows 计算机上已存在这些工具。

### <a name="resource-monitor"></a>资源监视器

资源监视器实时显示硬件和软件资源使用情况的相关信息。 若要在 Windows 计算机上运行资源监视器，请执行以下步骤 -

1. 按 Win + R，然后键入“resmon”。
1. resmon（即资源监视器）窗口打开后，请切换到“磁盘”选项卡。它提供以下视图 -

    ![资源监视器的“磁盘”选项卡](./media/monitoring-high-churn/resmon-disk-tab.png)

1. 必须连续监视此选项卡一段时间，才能获得清晰的图像。 在上面的示例中，我们发现 wmiprv.exe 改动量大。

一旦识别出导致计算机改动量大的应用程序，就可采取必要的措施来解决与这些应用程序相关的改动。

### <a name="performance-monitor"></a>性能监视器

性能监视器会监视计算机上的各种活动，例如 CPU 或内存使用情况。 若要在 Windows 计算机上运行性能监视器，请执行以下步骤 -

1. 按 Win + R，然后键入“perfmon”。
1. perfmon（即性能监视器）窗口打开后，将提供以下视图 -

    ![性能监视器步骤 1](./media/monitoring-high-churn/perfmon-step1.png)

1. 展开右侧的“监视工具”文件夹，然后单击“性能监视器”。 这将打开以下视图，让你了解当前性能的实时信息 -

    ![性能监视器步骤 2](./media/monitoring-high-churn/perfmon-step1.png)

1. 该图当前仅监视一个监视器，即“处理器时间百分比”，如该图正下方的表格所示。 可单击工具顶部的“+”符号，添加更多要监视的项。
1. 下面直观显示了在向其添加更多计数器后性能监视器的外观 -

    ![性能监视器步骤 3](./media/monitoring-high-churn/perfmon-step3.png)

在[此处](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)详细了解性能监视器。

## <a name="for-linux-machines"></a>对于 Linux 计算机

如果有一台运行 Linux 操作系统的计算机（无论是否在本地），则可使用其他一些工具来监视改动模式。

### <a name="iotop"></a>Iotop

最常用的工具之一是 iotop。 它是用于显示实时磁盘活动的实用工具。 它可列出正在执行 I/O 的进程及其正在使用的磁盘带宽。

打开命令提示符并运行命令 `iotop`。

### <a name="iostat"></a>IoStat

IoStat 是一款简单的工具，用于收集和显示系统输入和输出存储设备统计信息。 此工具通常用于跟踪存储设备性能问题，包括设备、本地磁盘和远程磁盘。

打开命令提示符并运行命令 `iostat`。

## <a name="next-steps"></a>后续步骤

了解如何使用 [Azure Monitor](monitor-log-analytics.md) 进行监视。