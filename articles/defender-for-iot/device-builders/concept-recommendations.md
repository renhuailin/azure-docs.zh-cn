---
title: IoT 中心的安全建议
description: 了解安全建议的概念，以及如何在 Defender for IoT Hub 中使用这些建议。
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014550"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT 中心的安全建议

Defender for IoT 会扫描你的 Azure 资源和 IoT 设备，并提供安全建议以减少你的攻击面。
安全建议是可操作的，旨在帮助客户遵循安全最佳做法。

本文提供了可在 IoT 中心上触发的建议列表。

## <a name="built-in-recommendations-in-iot-hub"></a>IoT 中心的内置建议

“建议”警报会针对用于改进环境安全状况的操作提供见解和建议。

| 严重性 | 名称 | 数据源 | 说明 |
|--|--|--|--|
| 高 | 多个设备使用相同的身份验证凭据 | IoT 中心 | 多个设备使用 IoT 中心身份验证凭据。 这一过程可能表示某个非法设备模拟了合法设备。 使用相同的凭据增加了恶意行动者对设备进行模拟的风险。 |
| 中 | 默认 IP 筛选策略应为“拒绝” | IoT 中心 | “IP 筛选配置”应为允许的流量定义了规则，并应默认拒绝其他所有流量。 |
| 中 | IP 筛选规则包括很大的 IP 范围 | IoT 中心 | “允许”IP 筛选规则源 IP 范围太大。 过度宽松的规则可能会将 IoT 中心暴露给恶意行动者。 |
| 低 | 在 IoT 中心启用诊断日志 | IoT 中心 | 启用日志并将其保留长达一年的时间。 通过保留日志，可以在发生安全事件或网络遭到入侵时重新创建活动线索，以用于调查目的。 |

## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
