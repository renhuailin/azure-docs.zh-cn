---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6fa30a56b3fb6e8b16a50bff7fda4c9349348633
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403572"
---
IoT 即插即用简化了 IoT 的使用，使你无需了解底层设备实现，就能与某个设备的功能交互。 本快速入门介绍如何使用 Java 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

要完成本快速入门，需在本地 Windows 环境上安装以下软件：

* Java SE 开发工具包 8。 在[针对 Azure 和 Azure Stack 的 Java 长期支持](/java/azure/jdk/)中的“长期支持”下选择“Java 8”。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用示例代码克隆 SDK 存储库

如果你完成了[教程：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (Java)](../articles/iot-develop/tutorial-connect-device.md)，则已经克隆了该存储库。

在所选目录中打开命令提示符。 执行以下命令，将[用于 Java 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 存储库克隆到此位置：

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>生成并运行示例设备

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md)。

在本快速入门中，你将使用一个以 Java 编写的示例恒温器设备作为 IoT 即插即用设备。 运行示例设备：

1. 打开终端窗口，然后导航到包含从 GitHub 克隆的用于 Java 的 Microsoft Azure IoT SDK 存储库的本地文件夹。

1. 此终端窗口将用作设备终端。 转到克隆存储库的根文件夹。 运行以下命令安装所有依赖项：

1. 运行以下命令以生成示例设备应用程序：

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. 若要运行示例设备应用程序，请导航到“device\iot-device-samples\pnp-device-sample\thermostat-device-sample”文件夹，并运行以下命令：

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例设备处于运行状态。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在[为 IoT 即插即用快速入门和教程设置环境](../articles/iot-develop/set-up-environment.md)中，已创建了两个环境变量以将示例配置为连接到 IoT 中心和设备：

* **IOTHUB_CONNECTION_STRING**：之前记下的 IoT 中心连接字符串。
* **IOTHUB_DEVICE_ID**：`"my-pnp-device"`。

在本快速入门中，你将使用以 Java 编写的示例 IoT 解决方案与刚设置的示例设备进行交互。

> [!NOTE]
> 此示例使用来自 IoT 中心服务客户端的“com.microsoft.azure.sdk.iot.service”命名空间 。 若要了解有关 API（包括数字孪生 API）的详细信息，请参阅[服务开发人员指南](../articles/iot-develop/concepts-developer-guide-service.md)。

1. 打开另一个终端窗口用作服务终端。

1. 在克隆的 Java SDK 存储库中，导航到“service\iot-service-samples\pnp-service-sample\thermostat-service-sample”文件夹。

1. 要运行示例服务应用程序，请运行以下命令：

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>获取设备孪生

以下代码片段演示了如何在服务中检索设备孪生：

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>更新设备孪生

以下代码片段演示了如何使用补丁通过设备孪生来更新属性：

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

设备输出将显示设备如何响应此属性更新。

### <a name="invoke-a-command"></a>调用命令

以下代码片段演示了如何在设备上调用命令：

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

设备输出将显示设备如何响应此命令。
