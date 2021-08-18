---
title: 配置和自定义适用于 Azure RTOS 的 Defender-IoT 微代理
description: 了解如何配置和自定义适用于 Azure RTOS 的 Defender-IoT 微代理。
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: 38e0b7cd1226d26caf45ed454c93e253a2ead240
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113138416"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos"></a>配置和自定义适用于 Azure RTOS 的 Defender-IoT 微代理

本文介绍如何为 Azure RTOS 设备配置 Defender IoT 微代理，以满足网络、带宽和内存要求。

请务必从 `netxduo/addons/azure_iot/azure_iot_security_module/configs` 目录中选择具有 `*.dist` 扩展名的目标分发文件。  

使用 CMake 编译环境时，必须针对所选值将命令行参数设置为 `IOT_SECURITY_MODULE_DIST_TARGET`。 例如 `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`。

在 IAR 或其他非 CMake 编译环境中，必须将 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` 路径添加到任何已知的包含的路径中。 例如 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`。

使用以下文件配置设备行为。

netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h

在 CMake 编译环境中，必须通过编辑 `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` 文件来更改默认配置。 对于所有其他环境，请使用 `set(ASC_XXX ON)` CMake 格式，或 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` 文件。 例如 `#define ASC_XXX`。

下面的表提供了每个配置的默认行为： 

## <a name="general"></a>常规

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | 字符串 | defender-iot-micro-agent | 设备的唯一标识符。  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | Number | 3.2.1 | 版本。 |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Number  | 3 | Defender IoT 微代理在失败后发送安全消息所需的时间。 （以秒为单位） |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | Defender IoT 微代理挂起时间（秒）。 如果超过该时间，则状态将更改为“挂起”。 |

## <a name="collection"></a>集合

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Number  | 30  | 收集器的启动收集间隔偏移量。 在启动过程中，该值将会添加到系统的集合中，以免同时从多个设备发送消息。  |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | 收集器的高优先级组间隔（秒）。 |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | 收集器的中等优先级组间隔（秒）。 |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145,440  | 收集器的低优先级组间隔（秒）。 |

#### <a name="collector-network-activity"></a>收集器网络活动

若要自定义收集器网络活动配置，请使用以下项：

| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | 筛选 `TCP` 网络活动。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | 筛选 `UDP` 网络活动事件。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | 筛选 `ICMP` 网络活动事件。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 布尔 | 是 | 仅捕获单播传入数据包。 如果设置为 false，它还将捕获广播和多播。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | 发送收集器的空事件。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | 要存储在内存中的 IPv4 网络事件的最大数目。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | 要存储在内存中的 IPv6 网络事件的最大数目。 |

### <a name="collectors"></a>Collectors
| 名称 | 类型 | 默认 | 详细信息 |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ON | 启用检测信号收集器。 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ON | 启用网络活动收集器。 |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ON | 启用系统信息收集器。  |

其他配置标志是高级标志，具有不支持的功能。 若要对此进行更改或需要了解详细信息，请联系支持部门。
 
## <a name="supported-security-alerts-and-recommendations"></a>支持的安全警报和建议

适用于 Azure RTOS 的 Defender IoT 微代理支持特定的安全警报和建议。 请确保[查看并自定义服务的相关警报和建议值](concept-rtos-security-alerts-recommendations.md)。

## <a name="log-analytics-optional"></a>Log Analytics（可选）

可以启用和配置 Log Analytics 来调查设备事件和活动。 若要了解详细信息，请阅读关于如何设置 Log Analytics 并[将 Log Analytics 与 Defender for IoT 服务配合使用](how-to-security-data-access.md#log-analytics)的文章。 

## <a name="next-steps"></a>后续步骤


- 查看并自定义适用于 Azure RTOS 的 Defender-IoT 微代理的[安全警报和建议](concept-rtos-security-alerts-recommendations.md)
- 请根据需要参阅[适用于 Azure RTOS 的 Defender-IoT 微代理 API](azure-rtos-security-module-api.md)。
