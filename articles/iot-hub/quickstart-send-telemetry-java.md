---
title: 快速入门：使用 Java 将遥测数据发送到 Azure IoT 中心
description: 本快速入门将运行两个示例 Java 应用程序，从而向 IoT 中心发送模拟遥测数据，并读取 IoT 中心的遥测数据，在云中进行处理。
author: wesmc7777
manager: lizross
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 01/27/2021
ms.openlocfilehash: df6673f6e8e448b0208ee53c17cf86f55d6d9f8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219159"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>快速入门：将遥测数据发送到 Azure IoT 中心并使用 Java 应用程序读取它

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

在本快速入门中，我们将遥测数据发送到 Azure IoT 中心并使用 Java 应用程序读取它。 IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门使用两个预先编写的 Java 应用程序：一个用于发送遥测数据，一个用于读取中心的遥测数据。 运行这两个应用程序前，请先创建 IoT 中心并在中心注册设备。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* Java SE 开发工具包 8。 在[针对 Azure 和 Azure Stack 的 Java 长期支持](/java/azure/jdk/)中的“长期支持”下选择“Java 8”。

    可以使用以下命令验证开发计算机上 Java 的当前版本：

    ```cmd/sh
    java -version
    ```

* [Apache Maven 3](https://maven.apache.org/download.cgi)。

    可以使用以下命令验证开发计算机上 Maven 的当前版本：

    ```cmd/sh
    mvn --version
    ```

* 使用 [azure-iot-samples-java 存储库页](https://github.com/Azure-Samples/azure-iot-samples-java)上的“代码”按钮下载或克隆 azure-iot-samples-java 存储库。 

    本文会使用存储库中的 [simulated-device](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/simulated-device) 和 [read-d2c-messages](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) 示例。

* 端口 8883 在防火墙中处于打开状态。 本快速入门中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyJavaDevice**：这是所注册的设备的名称。 建议使用 **MyJavaDevice**，如图所示。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的 _设备连接字符串_：

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

3. 还需要使用与事件中心兼容的终结点、与事件中心兼容的路径和 IoT 中心的服务主密钥，确保后端应用程序能连接到 IoT 中心并检索消息  。 以下命令可检索 IoT 中心的这些值：

     **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    记下这三个值，稍后会在快速入门中用到这些值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 在本地终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

2. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java 文件。

    将 `connString` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 **SimulatedDevice.java**。

    ```java
    public class SimulatedDevice {
      // The device connection string to authenticate the device with your IoT hub.
      // Using the Azure CLI:
      // az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table

      //private static String connString = "{Your device connection string here}";    
      private static String connString = "HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}";    
     ```

3. 在本地终端窗口中，运行以下命令以安装所需的库，并生成模拟设备应用程序：

    ```cmd/sh
    mvn clean package
    ```

4. 在本地终端窗口中，运行以下命令，以便运行模拟设备应用程序：

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![设备发送到 IoT 中心的遥测的输出](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

后端应用程序会连接到 IoT 中心上的服务端“事件”终结点。 应用程序会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

1. 在另一个本地终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 iot-hub\Quickstarts\read-d2c-messages 文件夹。

2. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java 文件。 更新以下变量并保存对文件所做的更改。

    | 变量 | 值 |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | 将变量的值替换为之前记下的与事件中心兼容的终结点。 |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | 将变量的值替换为之前记下的与事件中心兼容的路径。 |
    | `IOT_HUB_SAS_KEY`                | 将变量的值替换为之前记下的服务主密钥。 |

    ```java
    public class ReadDeviceToCloudMessages {
    
      private static final String EH_COMPATIBLE_CONNECTION_STRING_FORMAT = "Endpoint=%s/;EntityPath=%s;"
          + "SharedAccessKeyName=%s;SharedAccessKey=%s";
    
      // az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_ENDPOINT = "{your Event Hubs compatible endpoint}";
    
      // az iot hub show --query properties.eventHubEndpoints.events.path --name {your IoT Hub name}
      private static final String EVENT_HUBS_COMPATIBLE_PATH = "{your Event Hubs compatible name}";
    
      // az iot hub policy show --name service --query primaryKey --hub-name {your IoT Hub name}
      private static final String IOT_HUB_SAS_KEY = "{your service primary key}";    
    ```


3. 在本地终端窗口中运行以下命令，以安装所需的库并生成后端应用程序：

    ```cmd/sh
    mvn clean package
    ```

4. 在本地终端窗口中，运行以下命令，以便运行终端应用程序：

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了后端应用程序接收模拟设备发送到 IoT 中心的遥测数据后的输出：

    ![后端应用程序接收发送到 IoT 中心的遥测时的输出](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 Java 应用程序发送了模拟遥测数据到中心，并使用简单的后端应用程序读取中心的遥测数据。

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-java.md)
