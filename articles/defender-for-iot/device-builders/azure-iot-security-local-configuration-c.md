---
title: 安全代理本地配置 (C)
description: 了解适用于 C 的 Defender for IoT 代理本地配置。
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 7cd230b188c7c1d644ec03cff2d084ff7ea57139
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014562"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>了解 LocalConfiguration.json 文件 - C 代理

Defender for IoT 安全代理使用来自本地配置文件的配置。
安全代理在启动时读取一次配置。
在本地配置文件中找到的配置包含身份验证配置和其他与代理相关的配置。
该文件包含“键值”对中采用 JSON 表示法的配置，并在安装代理时填充这些配置。

默认情况下，该文件位于：/var/ASCIoTAgent/LocalConfiguration.json

代理重启时，配置文件会进行更改。

## <a name="security-agent-configurations-for-c"></a>适用于 C 的安全代理配置

| 配置名称 | 可能值 | 详细信息 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 代理唯一标识符 |
| TriggerdEventsInterval | ISO8601 字符串 | 计划程序收集已触发事件的间隔 |
| ConnectionTimeout | ISO8601 字符串 | IoThub 连接超时之前的时间段 |
| 身份验证 | JsonObject | 身份验证配置。 此对象包含针对 IoTHub 进行身份验证所需的所有信息 |
| 标识 | “DPS”、“SecurityModule”、“Device” | 身份验证标识 - 如果通过 DPS 进行身份验证，则该值为“DPS”；如果通过 Defender-IoT-micro-agent 凭据进行身份验证，则该值为“SecurityModule”；如果使用设备凭据进行身份验证，则该值为“Device” |
| AuthenticationMethod | “SasToken”、“SelfSignedCertificate” | 用于身份验证的用户机密 - 如果用户机密是对称密钥，请选择“SasToken”；如果机密是自签名证书，请选择“SelfSignedCertificate”  |
| 文件路径 | 文件路径（字符串） | 包含身份验证机密的文件的路径 |
| HostName | string | Azure IoT 中心的主机名。 通常是 <my-hub>.azure-devices.net |
| DeviceId | string | 设备的 ID（已注册到 Azure IoT 中心） |
| DPS | JsonObject | DPS 相关配置 |
| IDScope | string | DPS 的 ID 范围 |
| RegistrationId | string  | DPS 设备注册 ID |
| 日志记录 | JsonObject | 代理记录器相关配置 |
| SystemLoggerMinimumSeverity | 0 <= 数字 <= 4 | 等于或高于此严重级别的日志消息将被记录到 /var/log/syslog（0 是最低严重级别） |
| DiagnosticEventMinimumSeverity | 0 <= 数字 <= 4 | 等于或高于此严重级别的日志消息将被作为诊断事件发送（0 是最低严重级别） |

## <a name="security-agent-configurations-code-example"></a>安全代理配置代码示例

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解 Defender for IoT [基于代理的解决方案体系结构](architecture-agent-based.md)
- 启用 Defender for IoT [服务](quickstart-onboard-iot-hub.md)
- 阅读 Defender for IoT 服务[常见问题解答](resources-agent-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
