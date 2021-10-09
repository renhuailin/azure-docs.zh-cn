---
title: Defender for IoT 安全代理本地配置 (C#)
description: 详细了解 Defender for IoT 安全服务、适用于 C# 的安全代理本地配置文件。
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 810ca270fed350da8beaa1c63fafe39df4ab6a61
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658259"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>了解本地配置文件（C# 代理）

Defender for IoT 安全代理使用来自本地配置文件的配置。

当代理开始运行时，安全代理会读取一次配置文件。 在本地配置文件中找到的配置包含身份验证配置和其他与代理相关的配置。

C# 安全代理使用多个配置文件：

- General.config - 与代理相关的配置。
- Authentication.config - 与身份验证相关的配置（包括身份验证详细信息）。
- SecurityIotInterface.config - 与 IoT 相关的配置。

配置文件包含默认配置。 身份验证配置是在代理安装过程中填充的，对配置文件的更改是在重新启动代理时进行的。

## <a name="configuration-file-location"></a>配置文件位置

对于 Linux：

- 操作系统配置文件位于 `/var/ASCIoTAgent` 中。

对于 Windows：

- 操作系统配置文件位于安全代理的目录中。

### <a name="generalconfig-configurations"></a>General.config 配置

| 配置名称 | 可能值 | 详细信息 |
|:-----------|:---------------|:--------|
| agentId | GUID | 代理唯一标识符 |
| readRemoteConfigurationTimeout | TimeSpan | 用于从 IoT 中心提取远程配置的时间段。 如果代理无法在指定的时间内提取配置，操作将超时。|
| schedulerInterval | TimeSpan | 内部计划程序间隔。 |
| producerInterval | TimeSpan | 事件生成者辅助角色间隔。 |
| consumerInterval | TimeSpan | 事件使用者辅助角色间隔。 |
| highPriorityQueueSizePercentage | 0 < 数字 < 1 | 高优先级消息专用的缓存占总缓存的比例。 |
| logLevel | “Off”、“Fatal”、“Error”、“Warning”、“Information”、“Debug”  | 等于或高于此严重性级别的日志消息会记录到调试控制台（Linux 中的 Syslog）。 |
| fileLogLevel |  “Off”、“Fatal”、“Error”、“Warning”、“Information”、“Debug”| 等于或高于此严重性级别的日志消息会记录到文件（Linux 中的 Syslog）。 |
| diagnosticVerbosityLevel | “None”、“Some”、“All” | 诊断事件的详细程度级别。 None - 不会发送诊断事件。 Some - 只会发送重要性较高的诊断事件。 All - 所有日志也会作为诊断事件发送。 |
| logFilePath | 文件路径 | 如果 fileLogLevel > Off，则日志将写入此文件。 |
| defaultEventPriority | “High”、“Low”、“Off” | 默认事件优先级。 |

### <a name="generalconfig-example"></a>General.config 示例

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| 配置名称 | 可能值 | 详细信息 |
|:-----------|:---------------|:--------|
| moduleName | string | Defender-IoT-micro-agent 标识的名称。 此名称必须与设备中的模块标识名称相对应。 |
| deviceId | string | 设备的 ID（已注册到 Azure IoT 中心）。 |
| schedulerInterval | 时间跨度字符串 | 内部计划程序间隔。 |
| gatewayHostname | string | Azure Iot 中心的主机名。 通常为 \<my-hub\>.azure-devices.net |
| filePath | 字符串 - 文件路径 | 包含身份验证机密的文件的路径。|
| type | “SymmetricKey”、“SelfSignedCertificate” | 用于身份验证的用户机密。 如果用户机密是对称密钥，请选择“SymmetricKey”；如果密码是自签名证书，请选择“self-signed certificate”。 |
| 标识 | “DPS”、“Module”、“Device” | 身份验证标识 - 如果通过 DPS 进行身份验证，则该值为“DPS”；如果使用模块凭据进行身份验证，则该值为“Module”；如果使用设备凭据进行身份验证，则该值为“Device”。
| certificateLocationKind |  “LocalFile”、“Store” | 如果证书存储在文件中，则该值为“LocalFile”；如果证书位于证书存储中，则该值为“Store”。 |
| idScope | string | DPS 的 ID 范围 |
| registrationId | string  | DPS 设备注册 ID。 |
|

### <a name="authenticationconfig-example"></a>Authentication.config 示例

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| 配置名称 | 可能值 | 详细信息 |
|:-----------|:---------------|:--------|
| transportType | “Ampq”、“Mqtt” | IoT 中心传输类型。 |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config 示例

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解 Defender for IoT [基于代理的解决方案体系结构](architecture-agent-based.md)
- 启用 Defender for IoT [服务](quickstart-onboard-iot-hub.md)
- 阅读 Defender for IoT 服务[常见问题解答](resources-agent-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解安全[警报](concept-security-alerts.md)
