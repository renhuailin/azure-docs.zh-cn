---
title: 向 Azure IoT 中心发送设备遥测数据快速入门 (Node.js)
description: 在本快速入门中，你将使用适用于 Node.js 的 Azure IoT 中心设备 SDK 将遥测数据从设备发送到 IoT 中心。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876969"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>快速入门：将遥测数据从设备发送到 IoT 中心 (Node.js)

**适用于**：[设备应用程序开发](about-iot-develop.md#device-application-development)

本快速入门介绍一个基本的 IoT 设备应用程序开发工作流。 使用 Azure CLI 创建 Azure IoT 中心和模拟设备，然后使用 Azure IoT Node.js SDK 访问该设备，并将遥测数据发送到中心。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure CLI。 可以使用 Azure Cloud Shell（即在浏览器中运行的交互式 CLI Shell）运行本快速入门中的所有命令。 如果使用 Cloud Shell，则无需安装任何内容。 如果希望在本地使用 CLI，则本快速入门需要 Azure CLI 2.0.76 版或更高版本。 若要查找版本，请运行 az --version。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
- [Node.js 10+](https://nodejs.org)。 如果使用的是 Azure Cloud Shell，请勿更新已安装的 Node.js 版本。 Azure Cloud Shell 已安装最新版本的 Node.js。

    使用以下命令验证开发计算机上 Node.js 的当前版本：

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>使用 Node.js SDK 发送消息
在本部分，你要使用 Node.js SDK 将消息从模拟设备发送到 IoT 中心。 

1. 打开新的终端窗口。 将使用此终端来安装 Node.js SDK 和处理 Node.js 示例代码。 现在应该打开了两个终端：刚刚打开的用于操作 Node.js 的终端，以及在前面部分中用于输入 Azure CLI 命令的 CLI shell。

1. 将 [Azure IoT Node.js SDK 设备示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)复制到本地计算机：

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 导航到“azure-iot-sdk-node/device/samples/pnp”目录：

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. 安装 Azure IoT Node.js SDK 和所需的依赖项：

    ```console
    npm install
    ```

    此命令按照设备示例目录下 *package.json* 文件中的指定安装适当的依赖项。

1. 设置以下两个环境变量，使模拟设备能够连接到 Azure IoT。
    * 设置名为 `IOTHUB_DEVICE_CONNECTION_STRING` 的环境变量。 对于变量值，请使用上一部分保存的设备连接字符串。
    * 设置名为 `IOTHUB_DEVICE_SECURITY_TYPE` 的环境变量。 对于变量，请使用文本字符串值 `connectionString`。

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > 对于 Windows CMD，每个变量的字符串值的两边没有引号。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash（Linux 或 Windows）**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. 在打开的 CLI shell 中，运行 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 命令以开始监视模拟 IoT 设备上的事件。  事件消息抵达后，会在终端中输出。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. 在 Node.js 终端中，运行已安装示例文件 simple_thermostat.js 的代码。 此代码访问模拟的 IoT 设备并将消息发送到 IoT 中心。

    若要在终端中运行 Node.js 示例：
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > 此代码示例使用 Azure IoT 即插即用，这使你无需任何手动配置即可将智能设备集成到你的解决方案中。  默认情况下，本文档中的大多数示例都使用 IoT 即插即用。 若要详细了解 IoT PnP 的优点以及使用或不使用它的案例，请参阅[什么是 IoT 即插即用？](../iot-pnp/overview-iot-plug-and-play.md)

当 Node.js 代码将模拟的遥测消息从设备发送到 IoT 中心时，该消息将显示于正在监视事件的 CLI shell 中：

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

设备现已安全建立连接，并在向 Azure IoT 中心发送遥测数据。

## <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中创建的 Azure 资源，可以使用 Azure CLI 将其删除。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

若要按名称删除资源组，请执行以下操作：
1. 运行 [az group delete](/cli/azure/group#az_group_delete) 命令。 此命令将删除创建的资源组、IoT 中心和设备注册。

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. 运行 [az group list](/cli/azure/group#az_group_list) 命令，确认资源组是否已删除。  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure CLI 创建了 IoT 中心和模拟设备，然后使用 Azure IoT Node.js SDK 访问了该设备，并将遥测数据发送到了中心。 

接下来，请通过应用程序示例了解 Azure IoT Node.js SDK。

- [更多 Node.js 示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)：此目录包含 Node.js SDK 存储库中用于展示 IoT 中心方案的更多示例。