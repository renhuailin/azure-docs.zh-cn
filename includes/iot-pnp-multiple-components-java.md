---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: fd927823982af140abff9f3ec3eb2703cef8a53c
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516580"
---
本教程介绍如何生成多组件示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure CLI 来查看它发送的遥测数据。 该示例应用程序以 Java 编写，包含在用于 Java 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure CLI 来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

本教程介绍如何使用组件生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 Java 编写，包含在用于 Java 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

本教程介绍以下操作：

> [!div class="checklist"]
> * 下载示例代码。
> * 生成示例代码。
> * 运行该示例设备应用程序，并验证它是否会连接到 IoT 中心。
> * 检查源代码。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

若要在 Windows 上完成本教程，需在本地 Windows 环境上安装以下软件：

* Java SE 开发工具包 8。 在[针对 Azure 和 Azure Stack 的 Java 长期支持](/java/azure/jdk/)中的“长期支持”下选择“Java 8”。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

## <a name="download-the-code"></a>下载代码

如果完成了[教程：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (Java)](../articles/iot-develop/tutorial-connect-device.md)，则已经克隆了该存储库。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT Java SDK 和库](https://github.com/Azure/azure-iot-sdk-java) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

此操作需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

在 Windows 上，导航到克隆的 Java SDK 存储库的根文件夹。 运行以下命令以生成依赖项：

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>运行设备示例

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要运行示例应用程序，请导航到“\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample”文件夹，并运行以下命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现的模型使用[多个组件](../articles/iot-develop/concepts-modeling-guide.md)。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

设备代码使用标准 `DeviceClient` 类连接到 IoT 中心。 设备发送在连接请求中实现的 DTDL 模型的模型 ID。 发送模型 ID 的设备是 IoT 即插即用设备：

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

模型 ID 存储在代码中，如以下代码片段所示：

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

设备连接到 IoT 中心后，代码将注册命令处理程序。

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

对于两个恒温器组件上的所需属性更新，有单独的处理程序：

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

示例代码从每个恒温器组件发送遥测数据：

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading` 方法使用 `PnpHhelper` 类为每个组件创建消息：

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper` 类包含可以在多组件模型中使用的其他示例方法。

使用 Azure IoT 资源管理器工具查看两个恒温器组件中的遥测数据和属性：

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Azure IoT 资源管理器中的多组件设备":::

还可以使用 Azure IoT 资源管理器工具在两个恒温器组件或默认组件中调用命令。
