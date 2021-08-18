---
title: 如何在设备与 Azure 设备预配服务之间传输有效负载
description: 本文档介绍如何在设备与设备预配服务 (DPS) 之间传输有效负载。
author: wesmc7777
ms.author: wesmc
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: ff8157a938c3b754a35c5a588ac5590be2432971
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739242"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>如何在设备和 DPS 之间传输有效负载
有时，DPS 需要设备中的更多数据才能正常地将设备预配到适当的 IoT 中心，而这些数据需由设备提供。 反之，DPS 可将数据返回给设备，以便为客户端逻辑提供辅助。 

## <a name="when-to-use-it"></a>何时使用
此功能可用作[自定义分配](./how-to-use-custom-allocation-policies.md)的增强功能。 例如，你希望在无需人工干预的情况下，根据设备型号分配设备。 在这种情况下，需使用[自定义分配](./how-to-use-custom-allocation-policies.md)。 可以在[注册设备调用](/rest/api/iot-dps/device/runtime-registration/register-device)过程中将设备配置为报告型号信息。 DPS 会将设备的有效负载传入自定义分配 Webhook。 当函数收到设备型号信息时，可以确定此设备要转到哪个 IoT 中心。 同样，如果 Webhook 希望将某些数据返回给设备，它会在 Webhook 响应中以字符串的形式传回数据。  

## <a name="device-sends-data-payload-to-dps"></a>设备将数据有效负载发送到 DPS
当设备向 DPS 发送[注册设备调用](/rest/api/iot-dps/device/runtime-registration/register-device)，可以增强注册调用以获取正文中的其他字段。 正文如下所示： 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS 将数据返回给设备
如果自定义分配策略 Webhook 希望将某些数据返回给设备，它会在 Webhook 响应中以字符串的形式传回数据。 下面的 payload 节中提供了相关更改。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 支持
此功能在 C、C#、JAVA 和 Node.js [客户端 SDK](./index.yml) 中可用。  

## <a name="next-steps"></a>后续步骤
* 使用适用于 Azure IoT 中心和 Azure IoT 中心设备预配服务的 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)进行开发
