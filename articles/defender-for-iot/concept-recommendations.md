---
title: 安全建议
description: 了解安全建议的概念，以及如何在 Defender for IoT 中使用这些建议。
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
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: d11154e26e2ffe97a1ea102a11dddb746db9433a
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809263"
---
# <a name="security-recommendations"></a>安全建议

Defender for IoT 会扫描你的 Azure 资源和 IoT 设备，并提供安全建议以减少你的攻击面。
安全建议是可操作的，旨在帮助客户遵守安全最佳做法。

在本文中，你将找到建议列表，可在 IoT 中心和/或 IoT 设备上触发。

## <a name="agent-based-recommendations"></a>基于代理的建议

设备建议提供用于改进设备安全状况的见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 中 | 设备上有开放端口 | 经典安全模块 | 在设备上找到侦听终结点。 |
| 中 | 在其中一个链中发现了宽松的防火墙策略。 | 经典安全模块 | 发现了允许的防火墙策略（输入/输出）。 默认情况下，防火墙策略应拒绝所有流量，并定义规则以允许与设备之间的必要通信。 |
| 中 | 在输入链中发现了宽松的防火墙规则 | 经典安全模块 | 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则。 |
| 中 | 在输出链中发现了宽松的防火墙规则 | 经典安全模块 | 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则。 |
| 中 | 操作系统基线验证失败 | 经典安全模块 | 设备不符合 [CIS Linux 基准](https://www.cisecurity.org/cis-benchmarks/)。 |

### <a name="agent-based-operational-recommendations"></a>基于代理的操作建议

操作建议提供用于改进安全代理配置的见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 低 | 代理发送未利用的消息 | 经典安全模块 | 在过去 24 小时内，10% 或更高比例的安全消息小于 4 KB。 |
| 低 | 安全孪生体配置并非最佳 | 经典安全模块 | 安全孪生体配置并非最佳。 |
| 低 | 安全孪生体配置冲突 | 经典安全模块 | 在安全孪生体配置中识别出了冲突。 |  |


## <a name="built-in-recommendations-in-iot-hub"></a>IoT 中心的内置建议

“建议”警报会针对用于改进环境安全状况的操作提供见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 高 | 多个设备使用相同的身份验证凭据 | IoT 中心 | 多个设备使用 IoT 中心身份验证凭据。 此过程可能指示非法设备模拟合法设备。 使用相同的凭据增加了恶意行动者对设备进行模拟的风险。 |
| 中 | 默认 IP 筛选策略应为“拒绝” | IoT 中心 | “IP 筛选配置”应为允许的流量定义了规则，并应默认拒绝其他所有流量。 |
| 中 | IP 筛选规则包括很大的 IP 范围 | IoT 中心 | “允许”IP 筛选规则源 IP 范围太大。 过度宽松的规则可能会将 IoT 中心暴露给恶意行动者。 |
| 低 | 在 IoT 中心启用诊断日志 | IoT 中心 | 启用日志并将其保留长达一年的时间。 通过保留日志，可以在发生安全事件或网络遭到入侵时重新创建活动线索，以用于调查目的。 |


## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
