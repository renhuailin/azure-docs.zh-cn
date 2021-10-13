---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4a73c636eb6e1f9848a06bf38e584bd19d2a3b47
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725750"
---
IoT 即插即用简化了 IoT 的使用，使你无需了解底层设备实现，就能与某个设备的功能交互。 本快速入门介绍如何使用 Node.js 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要完成本快速入门，需要在开发计算机上使用 Node.js。 可以从 [nodejs.org](https://nodejs.org) 下载适用于多个平台的最新推荐版本。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用示例代码克隆 SDK 存储库

从 [Node SDK 存储库](https://github.com/Azure/azure-iot-sdk-node)克隆示例。 在所选文件夹中打开一个终端窗口。 运行以下命令，以克隆[用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>运行示例设备

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/pnp/readme.md)。

在本快速入门中，使用一个以 Node.js 编写的示例恒温器设备作为 IoT 即插即用设备。 运行示例设备：

1. 打开终端窗口，然后导航到包含从 GitHub 克隆的适用于 Node.js 的 Microsoft Azure IoT SDK 存储库的本地文件夹。

1. 此终端窗口将用作设备终端。 前往克隆的存储库的文件夹，然后导航到 /azure-iot-sdk-node/device/samples/pnp 文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 使用以下命令运行示例恒温器设备：

    ```cmd/sh
    node simple_thermostat.js
    ```

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请不要关闭此终端，需要在其中确认服务示例是否在正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在[为 IoT 即插即用快速入门和教程设置环境](../articles/iot-develop/set-up-environment.md)中，已创建了两个环境变量以将示例配置为连接到 IoT 中心和设备：

* **IOTHUB_CONNECTION_STRING**：之前记下的 IoT 中心连接字符串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入门中，你将使用以 Node.js 编写的示例 IoT 解决方案与你刚刚设置的示例设备进行交互。

1. 打开另一个终端窗口用作服务终端。

1. 在克隆的 Node SDK 存储库中，导航到“/azure-iot-sdk-node/service/samples/javascript”文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>读取属性

1. 在设备终端运行示例恒温器设备时，你会看到以下消息，指示其已联机：

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. 转到 **服务** 终端，并使用以下命令来运行用于读取设备信息的示例：

    ```cmd/sh
    node twin.js
    ```

1. 在服务终端输出中，请注意设备孪生的响应。 你会看到所报告的设备的模型 ID 以及相关属性：

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. 以下代码片段显示了 twin.js 中检索设备孪生的模型 ID 的代码：

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

在本方案中，此代码输出 `Model Id: dtmi:com:example:Thermostat;1`。

> [!NOTE]
> 这些服务示例使用来自 IoT 中心服务客户端的 Registry 类 。 若要了解有关 API（包括数字孪生 API）的详细信息，请参阅[服务开发人员指南](../articles/iot-develop/concepts-developer-guide-service.md)。

### <a name="update-a-writable-property"></a>更新可写属性

1. 在代码编辑器中打开文件 twin.js。

1. 查看示例代码，它演示了更新设备孪生的两种方法。 若要使用第一种方法，请按如下所示修改 `twinPatch` 变量：

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature` 属性被定义为恒温器设备模型中的可写属性。

1. 在服务终端中，使用以下命令来运行用于更新属性的示例：

    ```cmd/sh
    node twin.js
    ```

1. 在设备终端中，将看到设备已接收更新：

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. 在服务终端中，运行以下命令以确认属性已更新：

    ```cmd/sh
    node twin.js
    ```

1. 在服务终端输出的 `reported` 属性部分，你会看到报告的已更新的目标温度。 设备可能需要花费一段时间来完成更新。 请重复此步骤，直到设备已处理属性更新为止：

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>调用命令

1. 打开文件 device_method.js，并查看代码。

1. 转到 **服务** 终端。 使用以下命令来运行用于调用该命令的示例：

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. 服务终端中的输出显示以下确认消息：

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. 在设备终端中，将看到命令已确认：

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
