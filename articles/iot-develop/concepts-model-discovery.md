---
title: 在解决方案中使用 IoT 即插即用模型 | Microsoft Docs
description: 以解决方案构建人员身份了解如何在 IoT 解决方案中使用 IoT 即插即用模型。
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 82f845fb87b5b71556dd1638c6694c62655c95a8
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406320"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>在 IoT 解决方案中使用 IoT 即插即用模型

本文介绍如何在 IoT 解决方案中标识 IoT 即插即用设备的模型 ID，然后检索其模型定义。

IoT 解决方案分为两大类：

- *根据特定用途构建的解决方案* 适用于一系列要与解决方案连接的 IoT 即插即用设备的已知模型。 在开发解决方案时会使用这些模型。

- *模型驱动* 的解决方案适用于任何 IoT 即插即用设备的模型。 构建模型驱动的解决方案更为复杂，但优点在于，解决方案适用于将来可能添加的任何设备。 模型驱动的 IoT 解决方案会检索某个模型并使用它来决定设备实现的遥测、属性和命令。

若要使用 IoT 即插即用模型，IoT 解决方案需要：

1. 标识由 IoT 即插即用设备、模块或连接到解决方案的 IoT Edge 模块实现的模型的模型 ID。

1. 使用模型 ID 从模型存储库或自定义存储中检索已连接的设备的模型定义。

## <a name="identify-model-id"></a>标识模型 ID

当 IoT 即插即用设备连接到 IoT 中心时，会向 IoT 中心注册它所实现的模型的模型 ID。

在设备连接流中，IoT 中心会将设备模型 ID 告知解决方案。

解决方案可以使用以下三种方法之一获取 IoT 即插即用设备的模型 ID：

### <a name="get-device-twin-api"></a>获取设备孪生 API

解决方案可以使用[获取设备孪生](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API 来检索 IoT 即插即用设备的模型 ID。

> [!TIP]
> 对于模块和 IoT Edge 模块，使用 [ModuleClient.getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin)。

在以下设备孪生响应代码段中，`modelId` 包含 IoT 即插即用设备的模型 ID：

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>获取数字孪生 API

解决方案可以使用[获取数字孪生](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API 来检索 IoT 即插即用设备实现的模型的模型 ID。

在以下数字孪生响应代码段中，`$metadata.$model` 包含 IoT 即插即用设备的模型 ID：

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>数字孪生更改事件通知

设备连接会生成[数字孪生更改事件](concepts-digital-twin.md#digital-twin-change-events)通知。 解决方案需要订阅此事件通知。 若要了解如何为数字孪生事件启用路由，请参阅[使用 IoT 中心消息路由将设备到云消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

解决方案可以使用以下代码片段中所示的事件来了解正在连接的 IoT 即插即用设备并获取其模型 ID：

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>检索模型定义

解决方案使用上面标识的模型 ID 来检索相应的模型定义。

解决方案可以使用以下选项之一获取模型定义：

### <a name="model-repository"></a>模型存储库

解决方案可以使用[模型存储库](concepts-model-repository.md)来检索模型。 设备构建者或解决方案构建者必须事先将其模型上传到存储库，以便解决方案可以检索到它们。

标识新设备连接的模型 ID 后，请执行以下步骤：

1. 使用模型存储库中的模型 ID 检索模型定义。 有关详细信息，请参阅[设备模型存储库](concepts-model-repository.md)。

1. 使用连接的设备的模型定义，可以枚举设备的功能。

1. 使用枚举的设备功能，可以使用户能够[与设备交互](tutorial-service.md)。

### <a name="custom-store"></a>自定义存储

解决方案可以将这些模型定义存储在本地文件系统、公共文件存储中，或使用自定义实现。

标识新设备连接的模型 ID 后，请执行以下步骤：

1. 使用自定义存储中的模型 ID 检索模型定义。

1. 使用连接的设备的模型定义，可以枚举设备的功能。 

1. 使用枚举的设备功能，可以使用户能够[与设备交互](tutorial-service.md)。  

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 IoT 解决方案中集成 IoT 即插即用模型，以下是建议执行的一些后续步骤：

- [与解决方案中的设备交互](tutorial-service.md)
- [IoT 数字孪生 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)
