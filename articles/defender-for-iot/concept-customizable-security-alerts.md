---
title: IoT 中心的自定义安全警报
description: 了解使用适用于 IoT 中心的功能和服务的可自定义安全警报和建议的补救措施。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636520"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>用于 IoT 中心的 Defender 自定义安全警报

Defender for IoT 使用高级分析和威胁智能来持续分析 IoT 解决方案，在存在恶意活动时发出警报。

我们鼓励你根据你对预期设备行为的了解来创建自定义警报，以确保警报在你独特的组织部署和环境中充当潜在危害的最有效指标。

以下基于预期 IoT 中心行为的 IoT 警报列表可由你定义。 有关如何自定义每个警报的详细信息，请参阅 [创建自定义警报](quickstart-create-custom-alerts.md)。

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>IoT 中心内置的自定义警报

| 严重性 | 警报名称 | 数据源 | 说明 | 建议的补救措施 |
|--|--|--|--|--|
| 低 | 自定义警报-AMQP 协议中云到设备的消息数超出了允许的范围 | IoT 中心 | 特定时间范围内 (AMQP 协议) 的云到设备消息的数目超出了当前配置的和允许的范围。 |  |
| 低 | 自定义警报-AMQP 协议中被拒绝的云到设备消息的数量超出了允许的范围 | IoT 中心 | 特定时间范围内设备拒绝的云到设备消息 (AMQP protocol) 的数目超出了当前配置的和允许的范围。 |  |
| 低 | 自定义警报-AMQP 协议中的设备到云消息的数量超出允许范围 | IoT 中心 | 特定时间范围内的设备到云消息（AMQP 协议）数量超出了配置的允许范围。 |  |
| 低 | 自定义警报-直接方法调用的数目超出了允许范围 | IoT 中心 | 特定时间范围内的直接方法调用数量超出了当前配置的允许范围 |  |
| 低 | 自定义警报-文件上传的数目超出了允许的范围 | IoT 中心 | 特定时间范围内的文件上传数量超出了当前配置的允许范围 |  |
| 低 | 自定义警报-HTTP 协议中云到设备的消息数超出了允许的范围 | IoT 中心 | 时间窗口中云到设备的消息（HTTP 协议）数量不在配置的允许范围内 |
| 低 | 自定义警报-HTTP 协议中拒绝的云到设备消息数量不在允许的范围内 | IoT 中心 | 特定时间范围内的云到设备消息（HTTP 协议）数量超出了配置的允许范围。 |
| 低 | 自定义警报-HTTP 协议中的设备到云的消息数超出了允许的范围 | IoT 中心 | 特定时间范围内 (HTTP 协议) 的设备到云消息数量超出了当前配置的和允许的范围。 |  |
| 低 | 自定义警报-MQTT 协议中云到设备的消息数超出了允许的范围 | IoT 中心 | 特定时间范围内的云到设备消息（MQTT 协议）数量超出了配置的允许范围。 |  |
| 低 | 自定义警报-MQTT 协议中被拒绝的云到设备消息的数量超出了允许的范围 | IoT 中心 | 设备在特定时间范围内拒绝的云到设备消息（MQTT 协议）数量超出了配置的允许范围。 |
| 低 | 自定义警报-MQTT 协议中的设备到云消息的数量超出允许范围 | IoT 中心 | 特定时间范围内的设备到云消息（MQTT 协议）数量超出了配置的允许范围。 |
| 低 | 自定义警报-超出允许范围的命令队列清除的数目 | IoT 中心 | 特定时间范围内的命令队列清除数量超出了当前配置的允许范围。 |  |
| 低 | 自定义警报-模块克隆更新的数目超出了允许的范围 | IoT 中心 | 特定时间范围内的模块孪生更新数量超出了当前配置的允许范围。 |
| 低 | 自定义警报-未授权操作的数量超出允许范围 | IoT 中心 | 特定时间范围内的未授权操作数量超出了当前配置的允许范围。 |

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义警报](quickstart-create-custom-alerts.md)
- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
