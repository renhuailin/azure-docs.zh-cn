---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8cbfbc0f020504b17d9c2cbe3ca7d3f807ec28cd
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403573"
---
IoT 即插即用简化了 IoT 的使用，使你无需了解底层设备实现，就能与某个设备的模型交互。 本快速入门介绍如何使用 Python 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要完成本快速入门，需要在开发计算机上使用 Python 3.7。 可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。可以使用以下命令检查 Python 版本：  

```cmd/sh
python --version
```

azure-iot-device 包将以 PIP 的形式发布。

在本地 python 环境中安装该包，如下所示：

```cmd/sh
pip install azure-iot-device
```

运行以下命令安装 azure-iot-hub 包：

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>运行示例设备

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)。

在本快速入门中，你将使用一个以 Python 编写的示例调温器作为 IoT 即插即用设备。 运行示例设备：

1. 在所选文件夹中打开一个终端窗口。 运行以下命令，将 [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 存储库克隆到此位置：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 此终端窗口将用作设备终端。 前往克隆的存储库的文件夹，然后导航到 /azure-iot-sdk-python/azure-iot-device/samples/pnp 文件夹。

1. 使用以下命令运行示例恒温器设备：

    ```cmd/sh
    python simple_thermostat.py
    ```

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请不要关闭此终端，需要在其中确认服务示例是否在正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 Python 编写的示例 IoT 解决方案与你刚刚设置的示例设备进行交互。

1. 打开另一个终端窗口用作服务终端。

1. 导航到克隆的 Python SDK 存储库的“/azure-iot-sdk-python/azure-iot-hub/samples”文件夹。

1. 打开 registry_manager_pnp_sample.py 文件并查看代码。 此示例演示如何使用 IoTHubRegistryManager 类与 IoT 即插即用设备进行交互。

> [!NOTE]
> 这些服务示例使用来自 IoT 中心服务客户端的 IoTHubRegistryManager 类 。 若要了解有关 API（包括数字孪生 API）的详细信息，请参阅[服务开发人员指南](../articles/iot-develop/concepts-developer-guide-service.md)。

### <a name="get-the-device-twin"></a>获取设备孪生

在[为 IoT 即插即用快速入门和教程设置环境](../articles/iot-develop/set-up-environment.md)中，已创建了两个环境变量以将示例配置为连接到 IoT 中心和设备：

* **IOTHUB_CONNECTION_STRING**：之前记下的 IoT 中心连接字符串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在服务终端中使用以下命令运行此示例：

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> 如果是在 Linux 上运行此示例，请使用 `export` 代替 `set`。

输出将显示设备孪生，并打印其模型 ID：

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

以下代码片段显示了 registry_manager_pnp_sample.py 中的示例代码：

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>更新设备孪生

此示例演示如何更新设备中的 `targetTemperature` 可写属性：

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

你可以在显示以下输出的设备终端中验证是否已应用更新：

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

服务终端确认修补程序已成功：

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>调用命令

然后，该示例会调用命令：

服务终端显示来自设备的确认消息：

```cmd/sh
The device method has been successfully invoked
```

在设备终端中，将显示设备收到以下命令：

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
