---
title: 向 Azure IoT 中心发送设备遥测数据快速入门 (Python)
description: 在本快速入门中，你将使用适用于 Python 的 Azure IoT 中心设备 SDK 将遥测数据从设备发送到 IoT 中心。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: ea0b161a9038666e1e7ddd5a6c6af2078afff8aa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766510"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>快速入门：将遥测数据从设备发送到 Azure IoT 中心 (Python)

**适用于**：[设备应用程序开发](about-iot-develop.md#device-application-development)

本快速入门介绍一个基本的 IoT 设备应用程序开发工作流。 使用 Azure CLI 创建 Azure IoT 中心和设备，然后使用 Azure IoT Python SDK 生成模拟的客户端设备，并将遥测数据发送到中心。 

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure CLI。 可以使用 Azure Cloud Shell（即在浏览器中运行的交互式 CLI Shell）运行本快速入门中的所有命令。 如果使用 Cloud Shell，则无需安装任何内容。 如果希望在本地使用 CLI，则本快速入门需要 Azure CLI 2.0.76 版或更高版本。 若要查找版本，请运行 az --version。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
- [Python 3.7+](https://www.python.org/downloads/)。 有关支持的其他 Python 版本，请参阅 [Azure IoT 设备功能](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)。
    
    为了确保你的 Python 版本是最新的，请运行 `python --version`。 如果你同时安装了 Python 2 和 Python 3，而使用的是 Python 3 环境，请使用 `pip3` 安装所有库。 此命令可确保将库安装到 Python 3 运行时。
    > [!IMPORTANT]
    > 在 Python 安装程序中，选择“将 Python 添加到 PATH”选项。 如果已安装 Python 3.7 或更高版本，请确认你已将 Python 安装文件夹添加到 `PATH` 环境变量。

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>使用 Python SDK 发送消息
在本部分，你要使用 Python SDK 将消息从模拟设备发送到 IoT 中心。

1. 打开新的终端窗口。 将使用此终端来安装 Python SDK 和处理 Python 示例代码。 现在应该打开了两个终端：刚刚打开的用于操作 Python 的终端，以及在前面部分用于输入 Azure CLI 命令的 CLI shell。       

1. 将 [Azure IoT Python SDK 设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)复制到本地计算机：

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. 导航到“azure-iot-sdk-python/azure-iot-device/samples/pnp”目录：

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. 安装 Azure IoT Python SDK：

    ```console
    pip install azure-iot-device
    ```
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

1. 在打开的 CLI shell 中，运行 [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) 命令以开始监视模拟 IoT 设备上的事件。  事件消息到达时会在终端中输出。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. 在 Python 终端中，运行安装的示例文件 simple_thermostat.py 的代码。 此代码访问模拟的 IoT 设备并将消息发送到 IoT 中心。

    若要在终端中运行 Python 示例：
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > 此代码示例使用 Azure IoT 即插即用，这使你无需任何手动配置即可将智能设备集成到你的解决方案中。  默认情况下，本文档中的大多数示例都使用 IoT 即插即用。 若要详细了解 IoT PnP 的优点以及使用或不使用它的案例，请参阅[什么是 IoT 即插即用？](../iot-pnp/overview-iot-plug-and-play.md)

 当 Python 代码将消息从设备发送到 IoT 中心时，该消息将显示于正在监视事件的 CLI shell 中：

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
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
在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure CLI 创建了 Azure IoT 中心和设备，然后使用 Azure IoT Python SDK 生成了模拟设备，并将遥测数据发送到了中心。 

接下来，请通过应用程序示例了解 Azure IoT Python SDK。

- [异步示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)：此目录包含适用于更多 IoT 中心方案的异步 Python 示例。
- [同步示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)：此目录包含与 Python 2.7 配合使用的 Python 示例，或者与适用于 Python 3.6+ 的同步兼容性方案配合使用的 Python 示例
- [IoT Edge 示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)：此目录包含有关使用 Edge 模块和下游设备的 Python 示例。
