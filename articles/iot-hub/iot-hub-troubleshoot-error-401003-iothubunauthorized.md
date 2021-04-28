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
ms.openlocfilehash: 0e59fa2bcbc2d357857ddef39d990ddee9bc9c90
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129452"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文介绍了 **401003 IoTHubUnauthorized** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

### <a name="symptom-1"></a>症状 1

在日志中，你会看到这样一种情况：设备断开连接并出现“401003 IoTHubUnauthorized”，接着出现“404104 DeviceConnectionClosedRemotely”，然后在不久之后成功连接 。

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

SDK 的默认令牌有效期为 60 分钟；但是，对于某些 SDK，令牌有效期和令牌续订阈值是可配置的。 此外，设备在令牌续订时断开连接并重新连接时生成的错误对于每个 SDK 都是不同的。 若要了解更多信息，并了解如何确定设备在日志中使用哪个 SDK，请参阅 [Azure IoT SDK 的 MQTT 设备断开连接行为](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks)。

对于设备开发人员，如果担心错误数量太多，请切换到 C SDK，这会在 SAS 令牌过期之前续订它。 对于 AMQP，SAS 令牌可以在不断开连接的情况下进行刷新。

### <a name="solution-2"></a>解决方案 2

通常情况下，出现的错误消息应说明如何修复此错误。 如果由于某种原因无法访问错误消息详细信息，请确保：

- 所用的 SAS 或其他安全令牌未过期。
- 对于 X.509 证书身份验证，设备证书或与设备关联的 CA 证书未过期。 若要了解如何将 X.509 CA 证书注册到 IoT 中心，请参阅[在 Azure IoT 中心设置 X.509 安全性](./tutorial-x509-scripts.md)。
- 对于 X.509 证书指纹身份验证，需要向 IoT 中心注册设备证书的指纹。
- 授权凭据的格式正确，适用于所使用的协议。 若要了解详细信息，请参阅[控制 IoT 中心的访问权限](iot-hub-devguide-security.md)。
- 使用的授权规则对所请求的操作具有权限。

## <a name="next-steps"></a>后续步骤

- 为了更轻松地向 IoT 中心进行身份验证，我们建议使用 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
- 有关 IoT 中心身份验证的详细信息，请参阅[控制 IoT 中心的访问权限](iot-hub-devguide-security.md)。