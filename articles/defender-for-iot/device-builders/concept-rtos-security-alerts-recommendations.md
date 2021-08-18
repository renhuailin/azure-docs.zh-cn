---
title: 适用于 Azure RTOS 的 Defender-IoT-micro-agent 的内置与可自定义警报和建议
description: 使用 Azure IoT Defender-IoT-micro-agent - RTOS 了解安全警报和建议的修正措施。
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014549"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent 的安全警报和建议（预览版）

适用于 Azure RTOS 的 Defender-IoT-micro-agent 使用高级分析和威胁情报持续分析 IoT 解决方案，以提醒你注意潜在的恶意活动和可疑的系统修改。 你还可以根据对预期设备行为和基线的了解来创建自定义警报。

适用于 Azure RTOS 的 Defender-IoT-micro-agent 警报用作潜在危害指标，应进行调查和修正。 适用于 Azure RTOS 的 Defender-IoT-micro-agent 建议标识要修正和更新的薄弱安全状况。 

本文将提供内置警报和建议列表，这些警报和建议基于默认范围触发，并且可以根据预期或基线行为使用你自己的值进行自定义。 

有关警报自定义项如何在 Defender for IoT 服务中工作的详细信息，请参阅[可自定义的警报](concept-customizable-security-alerts.md)。 以下各表详细介绍了使用适用于 Azure RTOS 的 Defender-IoT-micro-agent 时可自定义的特定警报和建议。 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent 支持的安全警报

### <a name="device-related-security-alerts"></a>与设备相关的安全警报

|与设备相关的安全警报活动  |警报名称  |
|---------|---------|
|IP 地址| 检测到与可疑 IP 地址的通信|
|X.509 设备证书指纹|X.509 设备证书指纹不匹配|
|X.509 证书| X.509 证书已过期|
|SAS 令牌| SAS 令牌已过期|
|SAS 令牌| SAS 令牌签名无效|

### <a name="iot-hub-related-security-alerts"></a>与 IoT 中心相关的安全警报

|IoT 中心的安全警报活动  |警报名称  |
|---------|---------|
|添加证书    |  检测到将证书添加到 IoT 中心的尝试失败       |
|诊断设置的添加或编辑    | 检测到尝试添加或编辑 IoT 中心的诊断设置      |
|删除证书    |  检测到从 IoT 中心删除证书的尝试失败       |
|删除诊断设置    |  检测到尝试从 IoT 中心删除诊断设置      |
|已删除证书    | 检测到从 IoT 中心删除证书        |
|新建证书     |  检测到将新证书添加到 IoT 中心       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent 支持的可自定义警报

### <a name="device-related-customizable-alerts"></a>与设备相关的可自定义警报

|与设备相关的活动 |警报名称  |
|---------|---------|
|活动连接数|活动连接数目不在允许的范围内|
|MQTT 协议中的云到设备消息|MQTT 协议中的云到设备消息数目不在允许的范围内|
|出站连接| 与不允许的 IP 建立了出站连接|

### <a name="hub-related-customizable-alerts"></a>与中心相关的可自定义警报 

|与中心相关的活动  |警报名称  |
|---------|---------|
|命令队列清除     |  命令队列清除数目超出了允许的范围       |
|MQTT 协议中的云到设备消息    |  MQTT 协议中的云到设备消息数目超出了允许的范围       |
|MQTT 协议中的设备到云消息    | MQTT 协议中的设备到云消息数目超出了允许的范围        |
|直接方法调用     |  直接方法调用数目超出了允许的范围       |
|MQTT 协议中已拒绝的云到设备消息     |   MQTT 协议中已拒绝的云到设备消息数目超出了允许的范围      |
|对孪生模块的更新     |  对孪生模块的更新数目超出了允许的范围       |
|未授权的操作    |  未授权的操作数目超出了允许的范围       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent 支持的建议

### <a name="device-related-recommendations"></a>与设备相关的建议

|与设备相关的活动  |建议名称 |
|---------|---------|
|身份验证凭据    |  多个设备使用相同的身份验证凭据       |

### <a name="hub-related-recommendations"></a>与中心相关的建议

|与 IoT 中心相关的活动  |建议名称 |
|---------|---------|
|IP 筛选器策略   |  默认 IP 筛选器策略应设置为“拒绝”  |
|IP 筛选器规则| IP 筛选器规则包含大型 IP 范围|
|诊断日志|建议在 IoT 中心启用诊断日志|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>所有 Defender for IoT 警报和建议

有关与 Defender for IoT 服务相关的所有警报和建议的完整列表，请参阅 IoT [安全警报](concept-security-alerts.md)、IoT 安全[建议](concept-recommendations.md)。

## <a name="next-steps"></a>后续步骤

- [快速入门：适用于 Azure RTOS 的 Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
- [配置和自定义适用于 Azure RTOS 的 Defender-IoT-micro-agent](how-to-azure-rtos-security-module.md)
- 请参阅[适用于 Azure RTOS 的 Defender-IoT-micro-agent API](azure-rtos-security-module-api.md)