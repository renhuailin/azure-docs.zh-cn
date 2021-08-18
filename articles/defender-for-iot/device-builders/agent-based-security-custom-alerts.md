---
title: 基于代理的安全自定义警报
description: 了解使用 Defender for IoT 设备的功能和服务的可自定义安全警报及建议的修正措施。
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 2fb1385c12cbd9d0479d8528f54aad8816393ad1
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014568"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender for IoT 设备自定义安全警报

Defender for IoT 使用高级分析和威胁智能来持续分析 IoT 解决方案，在存在恶意活动时发出警报。

我们鼓励你根据你对预期设备行为的了解来创建自定义警报，以确保警报在你独特的组织部署和环境中充当潜在危害的最有效指标。

下面列出了你可以基于预期的 IoT 设备行为进行定义的 Defender for IoT 警报。 有关如何自定义每个警报的详细信息，请参阅[创建自定义警报](quickstart-create-custom-alerts.md)。

## <a name="agent-based-security-custom-alerts"></a>基于代理的安全自定义警报

| 严重性 | 警报名称 | 数据源 | 说明 | 建议的补救措施 |
|--|--|--|--|--|
| 低 | 自定义警报 - 活动连接数目超出了允许的范围 | 典型 Defender-IoT-micro-agent、Azure RTOS | 特定时间范围内的活动连接数目超出了当前配置的允许范围。 | 调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的连接列表。 |
| 低 | 自定义警报 - 与不允许的 IP 建立了出站连接 | 典型 Defender-IoT-micro-agent、Azure RTOS | 与允许的 IP 列表以外的 IP 建立了出站连接。 | 调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的 IP 列表。 |
| 低 | 自定义警报 - 失败的本地登录次数超出了允许的范围 | 典型 Defender-IoT-micro-agent、Azure RTOS | 特定时间范围内的失败本地登录次数超出了当前配置的允许范围。 |  |
| 低 | 自定义警报 - 允许的用户列表以外的用户登录 | 典型 Defender-IoT-micro-agent、Azure RTOS | 允许的用户列表以外的本地用户登录到了设备。 | 如果你正在保存原始数据，请导航到 Log Analytics 帐户，并使用该数据调查设备，确定来源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。 |
| 低 | 自定义警报 - 执行了不允许的进程 | 典型 Defender-IoT-micro-agent、Azure RTOS | 在设备上执行了某个不允许的进程。 | 如果你正在保存原始数据，请导航到 Log Analytics 帐户，并使用该数据调查设备，确定来源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。 |
|

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义警报](quickstart-create-custom-alerts.md)
- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
