---
title: 配置并自定义 Azure RTOS 安全模块
description: 了解如何配置并自定义 Azure RTOS 安全模块。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: fb2b7810c0829859f4a104c62b6df2ca0495bac7
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809195"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>配置并自定义 Azure RTOS 安全模块（预览版）

使用以下文件配置设备行为。

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port.h

 下面的表提供了每个配置的默认行为： 

### <a name="general"></a>常规

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | 字符串 | --- | 设备的唯一标识符  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | 安全模块挂起时间（秒）。 如果超时，状态会更改为“挂起”。 |

#### <a name="collection"></a>集合

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | 收集器高优先级组间隔（秒）。 |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | 收集器中等优先级组间隔（秒）。 |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145,440  | 收集器低优先级组间隔（秒）。 |

#### <a name="collector-network-activity"></a>收集器网络活动

若要自定义收集器网络活动配置，请使用以下项：

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | 筛选 `TCP` 网络活动 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | 筛选 `UDP` 网络活动事件 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | 筛选 `ICMP` 网络活动事件 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 布尔 | 是 | 仅捕获单播传入数据包，设置为“false”时也捕获广播和多播 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | 要存储在内存中的 IPv4 网络事件的最大数目 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | 要存储在内存中的 IPv6 网络事件的最大数目 |


## <a name="compile-flags"></a>编译标志
编译标志允许重写预定义的配置。

### <a name="collectors"></a>Collectors
| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| collector_heartbeat_enabled | Boolean | ON | 启用检测信号收集器 |
| collector_network_activity_enabled | Boolean | ON | 启用网络活动收集器 |
| collector_system_information_enabled | Boolean | ON | 启用系统信息收集器 |

## <a name="supported-security-alerts-and-recommendations"></a>支持的安全警报和建议

Azure RTOS 安全模块支持特定的安全警报和建议。 请确保[查看并自定义服务的相关警报和建议值](concept-rtos-security-alerts-recommendations.md)。

## <a name="log-analytics-optional"></a>Log Analytics（可选）

虽然是可选的而不是必需的，但是当你希望进一步调查设备事件和活动时，启用和配置 Log Analytics 会很有帮助。 阅读有关如何设置和使用 [适用于 IoT 服务的 Log Analytics 的](how-to-security-data-access.md#log-analytics) 详细信息。 

## <a name="next-steps"></a>后续步骤

- 查看并自定义 Azure RTOS 安全模块的[安全警报和建议](concept-rtos-security-alerts-recommendations.md)
- 根据需要参阅 [Azure RTOS 安全模块 API](azure-rtos-security-module-api.md)。

