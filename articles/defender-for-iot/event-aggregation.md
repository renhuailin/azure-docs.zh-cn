---
title: 事件聚合
description: 了解 Defender for IoT 事件聚合。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2020
ms.author: mlottner
ms.openlocfilehash: c823f0034db7d5fbe1f6b46f6af74e9fa374a6de
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832363"
---
# <a name="defender-for-iot-event-aggregation"></a>Defender for IoT 事件聚合

用于 IoT 安全代理的 Defender 从本地设备收集数据和系统事件，并将此数据发送到 Azure 云进行处理和分析。 安全代理收集许多类型的设备事件，包括新进程和新连接事件。 新进程和新连接事件可能会在一秒钟内合理地在设备上频繁发生，安全代理被迫发送的消息数量可能很快就会达到或超过 IoT 中心的配额和成本限制，而这对于构建稳健而全面的安全环境很重要。 但是，这些事件包含非常有价值的安全信息，这些信息对于保护设备至关重要。

为了减少额外的配额和成本，同时保护你的设备，Defender for IoT 代理聚合了这些类型的事件。

默认情况下，事件聚合处于“打开”状态，尽管不建议这样做，但可以随时手动将其关闭。

聚合目前可用于以下类型的事件：

* ProcessCreate
* ConnectionCreate
* ProcessTerminate（仅限 Windows）

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？

当事件聚合处于“打开”状态时，Defender for IoT 代理会聚合间隔时间段或时间范围内的事件。
间隔时间段过后，代理会将聚合事件发送到 Azure 云进行进一步分析。
在发送到 Azure 云之前，聚合事件一直存储在内存中。

为了减少代理的内存占用，每当代理收集与已保存在内存中的事件相同的事件时，代理都会增加此特定事件的命中次数。 聚合时间范围过后，代理会发送已发生的每种特定事件类型的命中次数。 事件聚合其实就是收集的每种事件类型的命中次数的聚合。

仅当满足以下条件时，才将事件视为相同：

* ProcessCreate 事件 - 当 commandLine、executable、username 和 userid 相同时
* ConnectionCreate 事件 - 当 commandLine、userId、direction、local address、remote address、**protocol 和 destination port 相同时
* ProcessTerminate 事件 - 当 executable 和 exit status 相同时

### <a name="working-with-aggregated-events"></a>处理聚合事件

在聚合期间，未聚合的事件属性将被丢弃，并在 Log Analytics 中显示 0 值。

* ProcessCreate 事件 - processId 和 parentProcessId 设置为 0
* ConnectionCreate 事件 - processId 和 source port 设置为 0

## <a name="event-aggregation-based-alerts"></a>基于事件聚合的警报

进行分析后，Defender for IoT 会为可疑的聚合事件创建安全警报。 根据聚合事件创建的警报对于每个聚合事件仅显示一次。

每个事件的聚合开始时间、结束时间和命中次数均记录在 Log Analytics 的事件 ExtraDetails 字段中，以供调查期间使用。

每个聚合事件均代表 24 小时内收集的警报。 使用每个事件左上方的事件选项菜单，可以逐一消除每个聚合事件。

## <a name="event-aggregation-twin-configuration"></a>事件聚合孪生配置

在 azureiotsecurity 模块的模块孪生标识的[代理配置对象](how-to-agent-configuration.md)中，对 Defender for IoT 事件聚合的配置进行更改。

| 配置名称 | 可能值 | 详细信息 | 备注 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 为进程创建事件启用/禁用事件聚合 |
| aggregationIntervalProcessCreate | ISO8601 时间跨度字符串 | 进程创建事件的聚合间隔 |
| aggregationEnabledConnectionCreate | boolean| 为连接创建事件启用/禁用事件聚合 |
| aggregationIntervalConnectionCreate | ISO8601 时间跨度字符串 | 连接创建事件的聚合间隔 |
| aggregationEnabledProcessTerminate | boolean | 为进程终止事件启用/禁用事件聚合 | 仅限 Windows|
| aggregationIntervalProcessTerminate | ISO8601 时间跨度字符串 | 进程终止事件的聚合间隔 | 仅限 Windows|
|

## <a name="default-configurations-settings"></a>默认配置设置

| 配置名称 | 默认值 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | 是 |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | 是 |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregationEnabledProcessTerminate | 是 |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>后续步骤

本文介绍了 Defender for IoT 安全代理聚合以及可用的事件配置选项。

若要继续开始使用 Defender for IoT 部署，请使用以下文章：

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看 Defender 的 IoT [系统先决条件](quickstart-system-prerequisites.md)
- 了解如何[在 IoT 中心启用 Defender for IoT 服务](quickstart-onboard-iot-hub.md)
- 通过 [Defender for IoT 常见问题解答](resources-frequently-asked-questions.md)深入了解该服务
