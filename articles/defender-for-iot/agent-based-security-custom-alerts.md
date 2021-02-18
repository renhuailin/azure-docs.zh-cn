---
title: 基于代理的安全自定义警报
titleSuffix: Azure Defender for IoT
description: 了解使用适用于 IoT 设备功能和服务的可自定义安全警报和建议的补救措施。
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
ms.openlocfilehash: e885566dd067d70fd4800ca96b8729494464da85
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642357"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>用于 IoT 设备的 Defender 自定义安全警报

Defender for IoT 使用高级分析和威胁智能来持续分析 IoT 解决方案，在存在恶意活动时发出警报。

我们鼓励你根据你对预期设备行为的了解来创建自定义警报，以确保警报在你独特的组织部署和环境中充当潜在危害的最有效指标。

根据预期的 IoT 设备行为，可根据预期的 iot 设备行为来定义以下用于 IoT 警报的 Defender 列表。 有关如何自定义每个警报的详细信息，请参阅 [创建自定义警报](quickstart-create-custom-alerts.md)。

## <a name="agent-based-security-custom-alerts"></a>基于代理的安全自定义警报

| 严重性 | 警报名称 | 数据源 | 说明 | 建议的补救措施 |
|--|--|--|--|--|
| 低 | 自定义警报-活动连接数超出允许范围 | 经典安全模块，Azure RTO | 特定时间范围内的活动连接数目超出了当前配置的允许范围。 | 调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的连接列表。 |
| 低 | 自定义警报-创建到不允许的 IP 的出站连接 | 经典安全模块，Azure RTO | 与允许的 IP 列表以外的 IP 建立了出站连接。 | 调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的 IP 列表。 |
| 低 | 自定义警报-失败的本地登录数超出允许范围 | 经典安全模块，Azure RTO | 特定时间范围内失败的本地登录的数目超出了当前配置的和允许的范围。 |  |
| 低 | 自定义警报-不在允许的用户列表中的用户登录 | 经典安全模块，Azure RTO | 允许的用户列表以外的本地用户登录到了设备。 | 如果要保存原始数据，请导航到 log analytics 帐户，并使用数据调查设备，确定源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。 |
| 低 | 自定义警报-执行了不允许的进程 | 经典安全模块，Azure RTO | 在设备上执行了某个不允许的进程。 | 如果要保存原始数据，请导航到 log analytics 帐户，并使用数据调查设备，确定源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。 |
|

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义警报](quickstart-create-custom-alerts.md)
- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
