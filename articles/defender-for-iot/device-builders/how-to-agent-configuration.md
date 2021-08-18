---
title: 配置安全代理
description: 了解如何将 Defender for IoT 安全代理配置为用于 Defender for IoT 安全服务。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 5ac9a5cdb4cc13ed91c0e5e29447b9cdbe71bcce
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014524"
---
# <a name="tutorial-configure-security-agents"></a>教程：配置安全代理

本文介绍 Defender for IoT 安全代理，并详细说明如何对其进行更改和配置。

> [!div class="checklist"]
> * 配置安全代理
> * 通过编辑孪生属性更改代理行为
> * 发现默认配置

## <a name="agents"></a>代理

Defender for IoT 安全代理从 IoT 设备收集数据，并执行安全措施以缓解检测到的漏洞。 使用一组可自定义的模块孪生属性，可以控制安全代理配置。 通常情况下，很少对这些属性进行次要更新。

Defender for IoT 的安全代理孪生配置对象是 JSON 格式的对象。 配置对象是一组可控制的属性，你可以定义这些属性来控制代理的行为。

这些配置可帮助你针对所需的每个方案自定义代理。 例如，通过配置这些属性，可以自动排除某些事件，或将能耗保持在最低水平。

使用 Defender for IoT 安全代理配置[架构](https://aka.ms/iot-security-github-module-schema)进行更改。

## <a name="configuration-objects"></a>配置对象

与每个 Defender for IoT 安全代理相关的属性位于 azureiotsecurity 模块的所需属性部分的代理配置对象中。

若要修改配置，请在 azureiotsecurity 模块孪生标识中创建并修改此对象。

如果 azureiotsecurity 模块孪生中不存在代理配置对象，则所有安全代理属性值均设置为默认值。

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>配置架构和验证

确保根据此[架构](https://aka.ms/iot-security-github-module-schema)验证代理配置。 如果配置对象与架构不匹配，则代理不会启动。

如果在代理运行时，配置对象变为无效配置（配置与架构不匹配），则代理将忽略无效配置，继续使用当前配置。

### <a name="configuration-validation"></a>配置验证

Defender for IoT 安全代理在 azureiotsecurity 模块孪生标识的报告属性部分中报告其当前配置。
代理将报告所有可用属性，如果用户未设置某个属性，代理将报告默认配置。

若要验证配置，请将所需部分中设置的值与报告部分中报告的值进行比较。

如果所需属性与报告属性不匹配，则代理无法分析配置。

根据[架构](https://aka.ms/iot-security-github-module-schema)验证所需属性，修复错误并再次设置所需属性！

> [!NOTE]
> 如果代理无法分析所需配置，则会从代理触发配置错误警报。
> 对所需部分和报告部分进行比较，了解该警报是否仍然适用

## <a name="editing-a-property"></a>编辑属性

必须在 azureiotsecurity 模块孪生的代理配置对象中设置所有自定义属性。
若要使用默认属性值，请从配置对象中删除该属性。

### <a name="setting-a-property"></a>设置属性

1. 在 IoT 中心找到并选择要更改的设备。

1. 单击设备，然后单击 azureiotsecurity 模块。

1. 单击“模块标识孪生”。

1. 在 Defender-IoT-micro-agent 中编辑要更改的属性。

   例如，若要将连接事件配置为高优先级，并每隔 7 分钟收集一次高优先级事件，请使用以下配置。

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. 单击“保存” 。

### <a name="using-a-default-value"></a>使用默认值

若要使用默认属性值，请从配置对象中删除该属性。

## <a name="default-properties"></a>默认属性

下表包含 Defender for IoT 安全代理的可控制属性。

[GitHub](https\://aka.ms/iot-security-module-default) 的适当架构中提供了默认值。

| 名称| 状态 | 有效值| 默认值| 说明 |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|必需：false |有效值：ISO 8601 格式的持续时间 |默认值：PT7M |发送高优先级消息前的最大时间间隔。|
|lowPriorityMessageFrequency |必需：false|有效值：ISO 8601 格式的持续时间 |默认值：PT5H |发送低优先级消息前的最长时间。|
|snapshotFrequency |必需：false|有效值：ISO 8601 格式的持续时间 |默认值 PT13H |创建设备状态快照的时间间隔。|
|maxLocalCacheSizeInBytes |必需：false |有效值： |默认值：2560000，大于 8192 | 代理的消息缓存所允许的最大存储量（以字节为单位）。 发送消息之前，设备上可用于存储消息的最大空间量。|
|maxMessageSizeInBytes |必需：false |有效值：正数，大于 8192，小于 262144 |默认值：204800 |代理到云消息的最大允许大小。 此设置控制每个消息中发送的最大数据量。 |
|eventPriority${EventName} |必需：false |有效值：高、低、关 |默认值： |代理生成的每个事件的优先级 |

### <a name="supported-security-events"></a>支持的安全事件

|事件名称| PropertyName | 默认值| 快照事件| 详细信息状态  |
|----------|-|---------|----|----|
|诊断事件|eventPriorityDiagnostic| Off| False| 与代理相关的诊断事件。 使用此事件进行详细日志记录。|
|配置错误 |eventPriorityConfigurationError |低 |False |代理无法分析配置。 根据架构验证配置。|
|已删除事件的统计信息 |eventPriorityDroppedEventsStatistics |低 |True|与代理相关的事件的统计信息。 |
|连接的硬件|eventPriorityConnectedHardware |低 |True |连接到设备的所有硬件的快照。|
|侦听端口|eventPriorityListeningPorts |高 |True |设备上打开的所有侦听端口的快照。|
|进程创建 |eventPriorityProcessCreate |低 |False |审核设备上的进程创建。|
|进程终止|eventPriorityProcessTerminate |低 |False |审核设备上的进程终止。|
|系统信息 |eventPrioritySystemInformation |低 |True |系统信息（例如：OS 或 CPU）的快照。|
|本地用户| eventPriorityLocalUsers |高 |True|系统中已注册的本地用户的快照。 |
|登录|  eventPriorityLogin |高|False|审核设备的登录事件（本地和远程登录）。|
|连接创建 |eventPriorityConnectionCreate|低|False|审核与设备之间建立的 TCP 连接。 |
|防火墙配置| eventPriorityFirewallConfiguration|低|True|设备防火墙配置（防火墙规则）的快照。 |
|OS 基线| eventPriorityOSBaseline| 低|True|设备 OS 基线检查的快照。|
|

## <a name="next-steps"></a>后续步骤

- [了解 Defender for IoT 建议](concept-recommendations.md)
- [探索 Defender for IoT 警报](concept-security-alerts.md)
- [访问原始安全数据](how-to-security-data-access.md)
