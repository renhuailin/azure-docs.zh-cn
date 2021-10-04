---
title: IoT 中心的安全建议
description: 了解安全建议的概念，以及如何在 Defender for IoT Hub 中使用这些建议。
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 6e4af6e4cadbd45e91219d2fce6d87c4fb555ece
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083327"
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
| 高 | 多个设备使用相同的身份验证凭据 | IoT 中心 | 多个设备使用 IoT 中心身份验证凭据。 这可能表明有非法设备在模拟合法设备，并且还存在恶意行动者进行设备模拟的风险。 |
| 高 | 在 IoT Edge 模型孪生体中为 IoT Edge 模块配置了高级权限 | IoT 中心 | IoT Edge 模块配置为在特权模式下运行，具有广泛的 Linux 功能或具有主机级网络访问权限（将数据发送到主机/从主机接收数据）。 |
| 中型 | 服务主体未与 ACR 存储库配合使用 | IoT 中心 | 用于从 ACR 存储库拉取 IoT Edge 模块的身份验证架构不使用服务主体身份验证。 |
| 中型 | 需要进行 TLS 加密套件升级 | IoT 中心 | 检测到不安全的 TLS 配置。 建议立即升级 TLS 密码套件。 |
| 中 | 默认 IP 筛选策略应为“拒绝” | IoT 中心 | 默认情况下，IP 筛选器配置需定义规则来允许特定的流量，并应拒绝所有其他流量。 |
| 中型 | IP 筛选器规则包含大型 IP 范围 | IoT 中心 | IP 筛选规则源的可允许 IP 范围太大。 过度宽松的规则可能会将 IoT 中心暴露给恶意行动者。 |
| 中型 | SecurityGroup 具有不一致的模块设置 | IoT 中心 | 在此设备安全组中，与安全组的其余成员相比，一个异常设备具有不一致的 IoT Edge 模块设置。 |
| 低 | IoT Edge 中心内存有待优化 | IoT 中心 | 通过禁用解决方案中 Edge 模块未使用的任何协议的协议头，来优化 IoT Edge 中心内存使用情况。 |
| 低 | 没有为 IoT Edge 模块配置日志记录 | IoT 中心 | 为此 IoT Edge 模块禁用了日志记录。 |

## <a name="next-steps"></a>后续步骤

- Defender for IoT 服务[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 详细了解如何[调查设备](how-to-investigate-device.md)
