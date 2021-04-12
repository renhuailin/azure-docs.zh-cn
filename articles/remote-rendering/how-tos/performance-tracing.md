---
title: 创建客户端性能跟踪
description: 借助 WPF 进行客户端性能分析的最佳做法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "92204089"
---
# <a name="create-client-side-performance-traces"></a>创建客户端性能跟踪

出于很多原因，Azure 远程渲染的性能可能并不尽如人意。 除了在云服务器上的渲染性能之外，网络连接质量也对体验产生了尤其重大的影响。 若要分析服务器性能，请参阅[服务器端性能查询](../overview/features/performance-queries.md)一章。

本章重点介绍如何通过 *:::no-loc text="performance traces":::* 识别潜在的客户端瓶颈。

## <a name="getting-started"></a>入门

如果你不熟悉 Windows :::no-loc text="performance tracing":::功能，本部分会介绍最基本的术语和应用程序方便你开始使用它。

### <a name="installation"></a>安装

使用 ARR 进行跟踪的应用程序是可用于 Windows 所有开发的通用工具。 它们通过 [Windows 性能工具包](/windows-hardware/test/wpt/)提供。 若要获取此工具包，请下载 [Windows 评估和部署工具包](/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>术语

搜索有关性能跟踪的信息时，必然会接触到一系列术语。 其中最重要的术语包括：

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** 代表 Windows 事件跟踪。[  ](/windows/win32/etw/about-event-tracing) 简单来说，它是 Windows 中内置的高效内核级跟踪工具的总体名称。 它被称为事件跟踪，是因为支持 ETW 的应用程序会发出事件以记录可能有助于跟踪性能问题的操作。 默认情况下，操作系统已发出诸如磁盘访问、任务切换等事件。 像 ARR 这样的应用程序还会发出自定义事件，例如删除的帧、网络延迟等。

**ETL** 表示事件跟踪日志记录。   简单来说，它表明已收集（记录）跟踪，因此通常用作跟踪数据存储文件的文件扩展名。 因此，执行跟踪时，后面通常会有一个 \* .etl 文件。

**WPR** 表示 Windows 性能记录器[  ](/windows-hardware/test/wpt/windows-performance-recorder)，是启动和停止事件跟踪记录的应用程序的名称。 WPR 使用配置文件 (\*.wprp) 来配置要记录的确切事件。 此类 `wprp` 文件随 ARR SDK 一起提供。 在台式电脑上执行跟踪时，可以直接启动 WPR。 在 HoloLens 上执行跟踪时，通常会改由 Web 界面来操作。

**WPA** 表示 Windows 性能分析器[  ](/windows-hardware/test/wpt/windows-performance-analyzer)，是 GUI 应用程序的名称，使用该应用程序可以打开 \* .etl 文件并筛选数据以识别性能问题。 通过 WPA，你可以按不同条件对数据进行排序，以多种方式显示数据，深入了解详细信息并关联信息。

尽管可以在任何 Windows 设备（本地 PC、HoloLens、云服务器等）上创建 ETL 跟踪，但通常会将其保存到台式电脑磁盘，并使用 WPA 进行分析。 可以向其他开发人员发送 ETL 文件供其查看。 但请注意，可能会在 ETL 跟踪中捕获到敏感信息（如文件路径和 IP 地址）。 可以通过两种方式使用 ETW：记录跟踪或分析跟踪。 记录跟踪简单直接，只需要很少的设置。 而分析跟踪则相反，它需要你透彻了解 WPA 工具和正在调查的问题。 下面给出了用于了解 WPA 的常规资料，以及如何解释 ARR 特定跟踪的指南。

## <a name="recording-a-trace-on-a-local-pc"></a>在本地 PC 上记录跟踪

若要识别 ARR 性能问题，你应该更愿意直接在 HoloLens 上执行跟踪，因为这是获取真正性能特征快照的唯一方法。 但是，如果明确想要在不受 HoloLens 性能限制的情况下执行跟踪，或者只想了解如何使用 WPA 而不需要进行实际跟踪，请参阅以下操作说明。

### <a name="wpr-configuration"></a>WPR 配置

1. 从“开始”菜单启动 [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder)。
1. 展开“更多选项”
1. 单击“添加配置文件...”
1. 选择 AzureRemoteRenderingNetworkProfiling.wprp 文件。 可以在 ARR SDK 的 Tools/ETLProfiles 下找到此文件。
   现在，WPR 的“自定义度量”下将列出此配置文件。 请确保它是唯一启用的配置文件。
1. 展开“第一级会审”：
    * 如果只想捕获 ARR 网络事件的快速跟踪数据，请“禁用”此选项。
    * 如果需要将 ARR 网络事件与其他系统特征（如 CPU 或内存使用情况）相关联，请“启用”此选项。
    * 如果启用此选项，则跟踪的数据很可能达到千兆字节量，需要很长时间才能在 WPA 中保存和打开。

之后，WPR 配置应如下所示：

![WPR 配置](./media/wpr.png)

### <a name="recording"></a>录制

单击“开始”以开始记录跟踪。 可以随时启动和停止记录；在执行此操作之前，无需关闭应用程序。 正如所见，你无需指定要跟踪的应用程序，因为 ETW 将始终记录对整个系统的跟踪。 `wprp` 文件指定要记录的事件类型。

单击“保存”停止记录，并指定 ETL 文件的存储位置。

现在你拥有一个 ETL 文件，你可以直接在 WPA 中打开它，也可以将其发送给其他人。

## <a name="recording-a-trace-on-a-hololens"></a>在 HoloLens 上记录跟踪

若要在 HoloLens 上记录跟踪，请启动设备，并将其 IP 地址输入到浏览器中以打开“设备门户”。

![设备门户](./media/wpr-hl.png)

1. 在左侧，导航到“性能 > 性能跟踪”。
1. 选择“自定义配置文件”
1. 单击 **:::no-loc text="Browse...":::**
1. 选择 AzureRemoteRenderingNetworkProfiling.wprp 文件。 可以在 ARR SDK 的 Tools/ETLProfiles 下找到此文件。
1. 单击“开始跟踪”
1. HoloLens 现在正在记录跟踪。 请确保触发要调查的性能问题。 然后单击“停止跟踪”。
1. 该跟踪将列于网页底部。 单击右侧的磁盘图标以下载 ETL 文件。

现在你拥有一个 ETL 文件，你可以直接在 WPA 中打开它，也可以将其发送给其他人。

## <a name="analyzing-traces-with-wpa"></a>使用 WPA 分析跟踪

### <a name="wpa-basics"></a>WPA 基础知识

Windows 性能分析器是用于打开 ETL 文件和检查跟踪的标准工具。 本文将不介绍 WPA 的工作原理。 若要开始使用，请查看以下资源：

* 观看[介绍性视频](/windows-hardware/test/wpt/windows-performance-analyzer)初步了解 WPA。
* WPA 本身提供“入门”选项卡，其中介绍了常用步骤。 查看可用主题。 尤其是在“查看数据”下，你可以快速了解如何为特定数据创建图形。
* [此网站](https://randomascii.wordpress.com/2015/09/24/etw-central/)提供十分有用的信息，但并非所有内容适合初学者。

### <a name="graphing-data"></a>图形数据

若要开始使用 ARR 跟踪，请注意以下几个方面。

![性能图形](./media/wpa-graph.png)

上图显示了跟踪数据表格和相同数据的图形表示。

在底部的表格中，注意黄色（金黄）条和蓝色条。 你可以拖动它们并将其放置在任何位置。

黄色条左侧的所有列都解释为键。 键用于构建左上方窗口中的树。 此处包含两个键列：“提供程序名称”和“任务名称”。 因此，左上方窗口显示两级树结构。 如果对列进行重新排序，或在键区域中添加或删除列，树视图中的结构将随之更改。

蓝条右侧的列用于右上方窗口中的图形显示。 大多数情况下，只使用第一列，但某些图形模式需要使用多列数据。 要显示线条图，必须为该列设置“聚合模式”。 使用“平均值”或“最大值”。 当某个像素范围涵盖多个事件时，使用聚合模式来确定图形在给定像素的值。 可以通过将聚合设置为“总和”并缩放来观察此数据。

中间的列没有任何特殊含义。

![“事件”视图](./media/wpa-event-view.png)

在“通用事件视图编辑器”中，你可以配置要显示的所有列、聚合模式、排序方式以及用作键或用于图形的列。 在上面的示例中，字段 2 处于启用状态，字段 3 至 6 处于禁用状态。 字段 2 通常是 ETW 事件的第一个自定义数据字段，因而对于 ARR “FrameStatistics” 事件而言，该字段表示网络延迟值。 启用其他“字段”列以查看此事件的更多值。

### <a name="presets"></a>预设

若要正确分析跟踪，需要确定自己的工作流和首选的数据显示。 但是，若要快速了解 ARR 特定的事件，需要将 Windows 软件保护平台配置文件和预设文件置于 Tools/ETLProfiles 文件夹中。 若要加载完整的配置文件，请从 WPA 菜单栏中选择“配置文件 > 应用...”，或打开“我的预设”面板（“窗口 > 我的预设”）并选择“导入”。 前者将设置一个完整的 WPA 配置，如下图所示。 后者只为可用的各种视图配置设置预设，并支持快速打开视图来查看特定的 ARR 事件数据。

![预设](./media/wpa-arr-trace.png)

上图显示了各种 ARR 特定事件的视图以及总体 CPU 利用率的视图。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](../overview/features/performance-queries.md)