---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 22655f5112323c51613efb5c22b3aa1b703039c6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516696"
---
本教程介绍如何使用组件生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 C# 编写，包含在用于 C# 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

本教程介绍以下操作：

> [!div class="checklist"]
> * 下载示例代码。
> * 生成示例代码。
> * 运行该示例设备应用程序，并验证它是否会连接到 IoT 中心。
> * 检查源代码。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用示例代码克隆 SDK 存储库

如果你完成了[教程：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (C#)](../articles/iot-develop/tutorial-connect-device.md)，则已经克隆了该存储库。

为 C# GitHub 存储库克隆 Azure IoT 示例中的示例。 在所选文件夹中打开命令提示符。 运行以下命令，以克隆[用于 .NET 的 Microsoft Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库：

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，我们使用一个以 C# 编写的示例温度控制器设备作为 IoT 即插即用设备。 运行示例设备：

1. 在 Visual Studio 2019 中打开“azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj”项目文件。

1. 在 Visual Studio 中，导航到“项目”>“温度控制器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 在完成[设置环境](../articles/iot-develop/set-up-environment.md)时记下的值 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 在完成[设置环境](../articles/iot-develop/set-up-environment.md)时记下的值 |


1. 现在可以在 Visual Studio 中生成示例，并在调试模式下运行它。

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请勿关闭此 Visual Studio 实例，你需要使用它确认服务示例正常工作。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现的模型使用[多个组件](../articles/iot-develop/concepts-modeling-guide.md)。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

设备代码使用标准 `CreateFromConnectionString` 方法连接到 IoT 中心。 设备发送在连接请求中实现的 DTDL 模型的模型 ID。 发送模型 ID 的设备是 IoT 即插即用设备：

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

模型 ID 存储在代码中，如以下代码片段所示：

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

设备连接到 IoT 中心后，代码将注册命令处理程序。 `reboot` 命令是在默认组件中定义的。 `getMaxMinReport` 命令分别定义在两个恒温器组件中：

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

对于两个恒温器组件上的所需属性更新，有单独的处理程序：

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

示例代码从每个恒温器组件发送遥测数据：

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync` 方法使用 `PnpHhelper` 类为每个组件创建消息：

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` 类包含可以在多组件模型中使用的其他示例方法。

使用 Azure IoT 资源管理器工具查看两个恒温器组件中的遥测数据和属性：

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT 资源管理器中的多组件设备":::

还可以使用 Azure IoT 资源管理器工具在两个恒温器组件或默认组件中调用命令。
