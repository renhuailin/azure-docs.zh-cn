---
title: Azure 流分析开窗函数简介
description: 本文介绍在 Azure 流分析作业中使用的四个开窗函数（翻转、跳跃、滑动、会话）。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: f9d47c3c08c450000da34742459a62977e82808a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615101"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>流分析开窗函数简介

在实时流方案中，对临时窗口中包含的数据执行操作是一种常见模式。 流分析提供对开窗函数的本机支持，使开发人员能够最小的工作量创建复杂的流进程作业。

有五种类型的时态窗口可供选择：[ **“翻转”**](/stream-analytics-query/tumbling-window-azure-stream-analytics)、[ **“跳跃”**](/stream-analytics-query/hopping-window-azure-stream-analytics)、[ **“滑动”**](/stream-analytics-query/sliding-window-azure-stream-analytics)、[ **“会话”**](/stream-analytics-query/session-window-azure-stream-analytics)和 [ **“快照”**](/stream-analytics-query/snapshot-window-azure-stream-analytics)窗口。  可在流分析作业中查询语法的 [GROUP BY](/stream-analytics-query/group-by-azure-stream-analytics) 子句中使用开窗函数  。 还可以使用 [Windows() 函数](/stream-analytics-query/windows-azure-stream-analytics)聚合在多个窗口中聚合事件。

所有[开窗](/stream-analytics-query/windowing-azure-stream-analytics)操作都在窗口“结束”时输出结果  。 请注意，在启动流分析作业时，可以指定“作业输出开始时间”，系统将自动提取传入流中先前的事件，以在指定时间输出第一个窗口；例如，当你开始使用“立即”选项时，它将立即开始发出数据 。 窗口的输出是基于所用聚合函数的单个事件。 该输出事件包含窗口的结束时间戳，所有开窗函数都以固定的长度定义。 

![流分析开窗函数的概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>翻转窗口

[翻转](/stream-analytics-query/tumbling-window-azure-stream-analytics)窗口函数用于将数据流划分成不同的时间段并对其执行某个函数，如以下示例所示。 翻转窗口的主要差异在于它们会重复，不重叠，并且一个事件不能属于多个翻转窗口。

![流分析翻转窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>跳跃窗口

[跳跃](/stream-analytics-query/hopping-window-azure-stream-analytics)窗口函数在一段固定的时间内向前跳跃。 可能很容易将它们视为可以重叠且比窗口大小更频繁发出的翻转窗口。 事件可以属于多个跳跃窗口结果集。 要使跳跃窗口与翻转窗口相同，需将跃点大小指定为与窗口大小相同。 

![流分析跳跃窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>滑动窗口

[滑动](/stream-analytics-query/sliding-window-azure-stream-analytics)窗口不同于翻转或跳跃窗口，它仅在窗口内容实际更改的时间点输出事件。 换句话说，事件进入或退出窗口时。 因此，每个窗口至少有一个事件。 与跳跃窗口类似，事件可以属于多个滑动窗口。

![流分析 10 秒滑动窗口](media/stream-analytics-window-functions/sliding-window-updated.png)

## <a name="session-window"></a>会话窗口

[会话](/stream-analytics-query/session-window-azure-stream-analytics)窗口函数对差不多同时到达的事件进行分组，筛选出没有数据的时间段。 它具有三个主要参数：超时、最长持续时间和分区键（可选）。

![流分析会话窗口](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

第一个事件发生时，会话窗口开始。 如果在上一个引入事件后的指定超时期间内发生另一事件，那么窗口将扩展到包含该新事件。 反之，如果超时期间内没有发生事件，则窗口在超时时关闭。

如果指定超时期间内持续发生事件，则会话窗口将持续扩展，直到达到最长持续时间。 最长持续时间检间隔设置为与指定最长持续时间相同。 例如，如果最长持续时间为 10，则将在 t = 0、10、20、30 等时，检查窗口是否超出最长持续时间。

如果提供了分区键，则事件按该键组合在一起，会话窗口将分别应用于每个组。 在需要将不同会话窗口用于不同用户或设备时，此分区十分有帮助。

## <a name="snapshot-window"></a>快照窗口

[快照](/stream-analytics-query/snapshot-window-azure-stream-analytics)窗口对具有相同时间戳的事件进行分组。 与其他需要特定窗口函数（如 [SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics)）的窗口类型不同，可以通过将 System.Timestamp() 添加到 GROUP BY 子句来应用快照窗口。

![流分析快照窗口](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](/rest/api/streamanalytics/)
