---
title: 基于代理的建议
titleSuffix: Azure Defender for IoT
description: 了解安全建议的概念，以及如何将这些建议用于 IoT 设备的 Defender。
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642360"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT 设备的安全建议

Defender for IoT 会扫描你的 Azure 资源和 IoT 设备，并提供安全建议以减少你的攻击面。
安全建议是可操作的，旨在帮助客户遵守安全最佳做法。

在本文中，你将看到一个建议列表，可以在 IoT 设备上触发。

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

## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
