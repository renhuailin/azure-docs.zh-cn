---
title: 基于代理的建议
description: 了解安全建议的概念，以及如何在 Defender for IoT 设备中使用这些建议。
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014571"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT 设备的安全建议

Defender for IoT 会扫描你的 Azure 资源和 IoT 设备，并提供安全建议以减少你的攻击面。
安全建议是可操作的，旨在帮助客户遵循安全最佳做法。

本文提供了可在 IoT 设备上触发的建议列表。

## <a name="agent-based-recommendations"></a>基于代理的建议

设备建议提供用于改进设备安全状况的见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 中 | 设备上有开放端口 | 典型 Defender-IoT-micro-agent| 在设备上发现了侦听终结点。 |
| 中 | 在其中一个链中发现了宽松的防火墙策略。 | 典型 Defender-IoT-micro-agent| 发现了允许的防火墙策略（输入/输出）。 默认情况下，防火墙策略应拒绝所有流量，并定义规则以允许与设备之间的必要通信。 |
| 中 | 在输入链中发现了宽松的防火墙规则 | 典型 Defender-IoT-micro-agent| 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则。 |
| 中 | 在输出链中发现了宽松的防火墙规则 | 典型 Defender-IoT-micro-agent| 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则。 |
| 中 | 操作系统基线验证失败 | 典型 Defender-IoT-micro-agent| 设备不符合 [CIS Linux 基准](https://www.cisecurity.org/cis-benchmarks/)。 |

### <a name="agent-based-operational-recommendations"></a>基于代理的操作建议

操作建议提供用于改进安全代理配置的见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 低 | 代理发送未利用的消息 | 典型 Defender-IoT-micro-agent | 在过去 24 小时内，10% 或更高比例的安全消息小于 4 KB。 |
| 低 | 安全孪生体配置并非最佳 | 典型 Defender-IoT-micro-agent | 安全孪生体配置并非最佳。 |
| 低 | 安全孪生体配置冲突 | 典型 Defender-IoT-micro-agent | 在安全孪生体配置中识别出了冲突。 |

## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
