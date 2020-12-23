---
title: 排查 Azure IoT 中心错误 401003 IoTHubUnauthorized
description: 了解如何修复错误 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357243"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文介绍了 **401003 IoTHubUnauthorized** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状 1

在日志中，可以看到设备的模式与 **401003 IoTHubUnauthorized** 断开连接，后跟 **404104 DeviceConnectionClosedRemotely** ，然后很快就会成功连接。

### <a name="symptom-2"></a>症状 2

对 IoT 中心的请求失败，并出现以下错误消息之一：

* 缺少 Authorization 标头
* IotHub '\*' 未包含指定的设备 '\*'
* 授权规则 '\*' 不允许访问 '\*'
* 此设备的身份验证失败，请续订令牌或证书，然后重新连接
* 指纹与配置不匹配：指纹：SHA1Hash=\*, SHA2Hash=\*；配置：PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

对于 MQTT，某些 SDK 依赖于 IoT 中心在 SAS 令牌过期时发出断开连接的指令，以便知道何时刷新它。 因此，

1. SAS 令牌过期
1. IoT 中心会注意到过期，将设备断开连接并显示 **401003 IoTHubUnauthorized**
1. 设备完成断开连接并显示 **404104 DeviceConnectionClosedRemotely**
1. IoT SDK 生成一个新的 SAS 令牌
1. 设备成功重新连接到 IoT 中心

### <a name="cause-2"></a>原因 2

IoT 中心无法对 auth 标头、规则或密钥进行身份验证。 这可能是由于症状中提到的任何原因所致。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

如果使用 IoT SDK 通过设备连接字符串进行连接，则不需要执行任何操作。 IoT SDK 会重新生成新令牌，以在 SAS 令牌过期时重新连接。

默认令牌生存期为跨 Sdk 60 分钟;但对于某些 Sdk，令牌生命周期和令牌续订阈值是可配置的。 此外，每个 SDK 的设备断开连接和重新连接时生成的错误都不同。 若要了解详细信息，以及有关如何确定设备在日志中使用的 SDK 的信息，请参阅 [MQTT device disconnect With Azure IoT sdk](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks)。

对于设备开发人员，如果错误量是一个问题，请切换到 C SDK，它会在过期之前续订 SAS 令牌。 对于 AMQP，SAS 令牌可以在不断开连接的情况下进行刷新。

### <a name="solution-2"></a>解决方案 2

通常情况下，出现的错误消息应说明如何修复此错误。 如果由于某种原因无法访问错误消息详细信息，请确保：

- 所用的 SAS 或其他安全令牌未过期。
- 对于 x.509 证书身份验证，设备证书或与设备关联的 CA 证书未过期。 若要了解如何向 IoT 中心注册 x.509 CA 证书，请参阅 [在 Azure IoT 中心设置 x.509 安全性](iot-hub-security-x509-get-started.md)。
- 对于 x.509 证书指纹身份验证，设备证书的指纹将注册到 IoT 中心。
- 授权凭据的格式正确，适用于所使用的协议。 若要了解详细信息，请参阅 [控制对 IoT 中心的访问](iot-hub-devguide-security.md)。
- 使用的授权规则对所请求的操作具有权限。

## <a name="next-steps"></a>后续步骤

- 为了更轻松地向 IoT 中心进行身份验证，我们建议使用 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
- 有关 IoT 中心身份验证的详细信息，请参阅 [控制对 Iot 中心的访问](iot-hub-devguide-security.md)。
