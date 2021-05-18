---
title: 监视 Azure FXT Edge Filer
description: 如何监视 Azure FXT Edge Filer 混合存储缓存的硬件状态
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219711"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>监视 Azure FXT Edge Filer 硬件状态

Azure FXT Edge Filer 混合存储缓存系统在机箱中内置了多个状态灯，以帮助管理员了解硬件如何工作。

## <a name="system-health-status"></a>系统运行状况

若要在较高级别监视缓存操作，请使用软件控制面板的“仪表板”页，如[控制面板仪表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)所述

## <a name="hardware-status-leds"></a>硬件状态 LED

本部分介绍了 Azure FXT Edge Filer 硬件中内置的各种状态灯。

### <a name="hard-drive-status-leds"></a>硬盘状态 LED

![水平放置的硬盘前面的照片，带标注标签 2（左上角）、1（左下角）和 3（右侧）](media/fxt-monitor/fxt-drive-callouts.png)

每个驱动器托架都有两个状态 LED：活动指示灯 (1) 和状态指示灯 (2)。

* 驱动器在使用时，活动 LED (1) 亮起。
* 状态 LED (2) 使用下表中的代码指示驱动器的状况。

| 驱动器状态 LED 状态              | 含义  |
|-------------------------------------|----------------------------------------------------------|
| 每秒闪烁绿色两次      | 正在识别驱动器或者 <br> 正在准备要移除的驱动器  |
| 熄灭（不发光）                         | 系统尚未完成启动或者 <br>驱动器已准备就绪，可以移除 |
| 闪烁绿色、琥珀色和熄灭       | 预计驱动器将发生故障   |
| 每秒闪烁琥珀色四次 | 驱动器发生故障   |
| 绿色常亮                         | 驱动器处于联机状态 |

驱动器的右端 (3) 标有驱动器的容量和其他信息。

驱动器编号显示在驱动器之间的空间上。 在 Azure FXT Edge Filer 中，驱动器 0 为左上角的驱动器，驱动器 1 位于其正下方。 编号将继续以该模式进行。

![FXT 机箱中一个硬盘槽的图片，其中显示了驱动器编号和容量标签](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左侧控制面板

左前控制面板具有各种状态 LED 指示灯 (1) 和一个大的发光的系统运行状况指示灯 (2)。

![左侧状态面板，左侧是标记为 1 的状态指示灯，右侧是标记为 2 的一个大的系统运行状况指示灯](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制面板状态指示灯

如果该系统中出现错误，则左侧的状态指示灯显示为琥珀色常亮。 下表描述了错误的可能原因和解决方案。

如果在尝试这些解决方案后仍然存在错误，请[联系支持人员](fxt-support-ticket.md)来寻求帮助。

| 图标 | 说明 | 添加状态 | 可能的解决方案 |
|----------------|---------------|--------------------|----------------------|
| ![驱动器图标](media/fxt-monitor/fxt-hd-icon.jpg) | 驱动器状态 | 驱动器错误 | 检查系统事件日志来确定驱动器是否存在错误，或者 <br>运行相应的联机诊断测试；重启系统并运行嵌入式诊断 (ePSA)，或者 <br>如果在 RAID 阵列中配置了驱动器，请重启系统并进入主机适配器配置实用工具程序 |
|![温度图标](media/fxt-monitor/fxt-temp-icon.jpg) | 温度状态 | 温度错误 - 例如，风扇出现故障或者环境温度超出范围 | 检查是否存在以下可解决的状况： <br>冷却风扇缺失或发生故障 <br>系统的盖子、空气罩、内存模块为空或背面填充托架已移除 <br>环境温度过高 <br>外部气流受阻 |
|![电气图标](media/fxt-monitor/fxt-electric-icon.jpg) | 电气状态 | 电气错误 - 例如，电压超出范围、PSU 发生故障或者电压调节器发生故障 |  检查系统事件日志或系统消息中是否有特定的问题。 如果存在 PSU 问题，请检查 PSU 状态 LED，并根据需要重新拔插 PSU。 |
|![内存图标](media/fxt-monitor/fxt-memory-icon.jpg) | 内存状态 | 内存错误 | 检查系统事件日志或系统消息来查明出故障的内存的位置；重新拔插内存模块。 |
|![PCIe 图标](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 状态 | PCIe 卡错误 | 重启系统；更新 PCIe 卡驱动程序；重新安装卡 |

### <a name="system-health-status-indicator"></a>系统运行状况指示灯

左侧控制面板右侧的大型发光按钮指示整体系统状态，并且在系统 ID 模式下用作单元定位灯。

按“系统运行状况和 ID”按钮可在系统 ID 模式与系统运行状况模式之间切换。

|系统运行状况状态 | 条件 |
|-------------------------------------------|-----------------------------------------------|
| 蓝色常亮 | 运行正常：系统已通电，运行正常，系统 ID 模式未处于活动状态。 <br/>如果要切换到系统 ID 模式，请按“系统运行状况和 ID”按钮。 |
| 蓝色闪烁 | 系统 ID 模式处于活动状态。 如果要切换到系统运行状况模式，请按“系统运行状况和系统 ID”按钮。 |
| 琥珀色常亮 | 系统处于防故障模式。 如果问题仍然存在，请[与 Microsoft 客户服务与支持部门联系](fxt-support-ticket.md)。 |
| 琥珀色闪烁 | 系统故障。 检查系统事件日志以查找特定的错误消息。 若要了解对系统组件进行监视的系统固件和代理生成的事件和错误消息，请参阅 qrl.dell.com 上的“错误代码查找”页。 |
