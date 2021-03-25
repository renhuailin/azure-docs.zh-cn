---
title: Azure Batch 分析
description: 批处理分析中的主题包含可用于批处理服务资源的事件和警报的参考信息。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849505"
---
# <a name="batch-analytics"></a>批处理分析

本部分中的主题包含可用于批处理服务资源的事件和警报的参考信息。

有关启用和使用批处理诊断日志的详细信息，请参阅 [Azure Batch 诊断日志记录](./batch-diagnostics.md)。

## <a name="diagnostic-logs"></a>诊断日志

Azure Batch 服务会在某些批处理资源的生命周期内生成以下诊断日志事件。

### <a name="service-log-events"></a>服务日志事件

- [池创建](batch-pool-create-event.md)
- [池删除启动](batch-pool-delete-start-event.md)
- [池删除完成](batch-pool-delete-complete-event.md)
- [池调整大小启动](batch-pool-resize-start-event.md)
- [池调整大小完成](batch-pool-resize-complete-event.md)
- [池自动缩放](batch-pool-autoscale-event.md)
- [任务启动](batch-task-start-event.md)
- [任务完成](batch-task-complete-event.md)
- [任务失败](batch-task-fail-event.md)
- [任务计划失败](batch-task-schedule-fail-event.md)
