---
title: 访问安全与建议数据
description: 了解如何在使用 Defender for IoT 时访问安全警报和建议数据。
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 8dc6f9d3deb80439d23e220c97f2a24363f0f05b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014635"
---
# <a name="access-your-security-data"></a>访问安全数据

Defender for IoT 在 Log Analytics 工作区中存储安全警报、建议和原始安全数据（如果选择保存）。

## <a name="log-analytics"></a>Log Analytics

配置要使用的 Log Analytics 工作区：

1. 打开 IoT 中心
1. 单击“安全”部分下的“设置”边栏选项卡 。
1. 单击“数据收集”，并更改 Log Analytics 工作区配置。

要在配置后访问 Log Analytics 工作区中的警报和建议：

1. 在 Defender for IoT. 中选择警报或建议。
1. 单击“进一步调查”，然后单击“若要查看哪些设备生成了此警报，请单击此处并查看 DeviceId 列” 。

有关从 Log Analytics 查询数据的详细信息，请参阅 [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。

## <a name="security-alerts"></a>安全警报

安全警报存储在为 Defender for IoT 解决方案配置的 Log Analytics 工作区中的“AzureSecurityOfThings.SecurityAlert”表中。

我们提供了许多有用的查询来帮助你开始了解安全警报。

### <a name="sample-records"></a>示例记录

选择几个随机记录

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 说明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 暴力攻击成功           | 已成功对设备执行暴力攻击        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 设备上本地登录成功      | 检测到成功的设备本地登录     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 设备上本地登录尝试失败  | 检测到失败的设备本地登录尝试 |    { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary"></a>设备摘要

获取在过去一周内检测到的不同安全警报数量，按 IoT 中心、设备、警报严重性和警报类型分组。

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 计数 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 暴力攻击成功           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 设备上本地登录尝试失败  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 设备上本地登录成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 加密硬币挖掘器                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

按 IoT 中心、警报严重性、警报类型选择上周有警报的不同设备数量

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 暴力攻击成功           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 设备上本地登录尝试失败  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 设备上本地登录成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 加密硬币挖掘器                     | 1          |

## <a name="security-recommendations"></a>安全建议

安全建议存储在为 Defender for IoT 解决方案配置的 Log Analytics 工作区中的“AzureSecurityOfThings.SecurityRecommendation”表中。

我们提供了许多有用的查询来帮助你开始了解安全建议。

### <a name="sample-records"></a>示例记录

选择几个随机记录

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 说明 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.06 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | 可用 | 在输入链中发现了宽松的防火墙规则 | 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | 可用 | 在输入链中发现了宽松的防火墙规则 | 在防火墙中发现了包含适用于许多不同 IP 地址或端口的宽松模式的规则 | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>设备摘要

获取不同的活动安全建议的数量，按 IoT 中心、设备、建议严重性和类型分组。

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | 计数 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 4   |

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT [概述](overview.md)
- 了解 Defender for IoT [什么是适用于设备构建者的基于代理的解决方案](architecture-agent-based.md)
- 知道并了解[Defender for IoT 警报](concept-security-alerts.md)
- 知道并了解[Defender for IoT 建议](concept-recommendations.md)