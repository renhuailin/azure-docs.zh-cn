---
title: 微代理配置（预览版）
description: 在进行任何配置更改后，收集器会立即发送所有当前数据。 然后会应用所做的更改。
ms.date: 10/04/2021
ms.topic: conceptual
ms.openlocfilehash: 43a26a73f97c9d424998a4ff35d1304c0baf6fc0
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428438"
---
# <a name="micro-agent-configurations-preview"></a>微代理配置（预览版）

本文介绍了微代理支持的各种类型的配置。 客户可以配置微代理来满足其设备和网络环境的需求。  

微代理的行为是通过一组模块孪生属性配置的。 你可以配置微代理以最好地满足你的需求。 例如，你可以自动排除事件，最大限度地降低功耗并降低网络带宽。

在配置发生更改后，收集器会立即发送所有未发送的事件数据。 发送数据后，将应用所做的更改，并且所有收集器都会重启。

> [!Note]
> 聚合模式的设置受支持，但不可配置。

## <a name="event-based-collectors-configurations"></a>基于事件的收集器配置

这些配置包括进程和网络活动收集器。

| 设置名称 | 设置选项 | 描述 | 默认设置 |
|--|--|--|--|
| 间隔 | 高、中或低 | 定义发送频率。 | 中等 |
| 聚合模式 | True 或 False | 是否处理相同事件的事件聚合。  | True |
| **缓存大小** | 循环 FIFO | 在两次数据发送之间收集的事件数。 | 256 |
| 禁用收集器 | True 或 False | 收集器是否可以运行。 | 错误 |

## <a name="trigger-based-collectors-configurations"></a>基于触发器的收集器配置

这些配置包括系统信息和基线收集器。

| 设置名称 | 设置选项 | 描述 | 默认设置 |
|--|--|--|--|
| 间隔 | 高、中或低 | 数据的发送频率。 | 低 |
| 禁用收集器 | True 或 False | 收集器是否可以运行。 | 错误 |

## <a name="general-configuration"></a>常规配置

定义每个优先级级别发送消息的频率。 下面列出了默认值：

| 频率 | 时间段（分钟） |
|--|--|
| 低 | 1440（24 小时） |
| 中等 | 120（2 小时） |
| 高 | 30（0.5 小时） |

若要减少发送到云的消息数量，应将每个优先级设置为比它低的优先级的倍数。 例如，高：60 分钟、中：120 分钟、低：480 分钟。

## <a name="next-steps"></a>后续步骤

了解[微代理事件收集（预览版）](concept-event-aggregation.md)。
