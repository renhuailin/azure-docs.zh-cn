---
title: 向 Azure IoT Central 发送设备遥测数据快速入门 (Python)
description: 在本快速入门中，你将使用适用于 Python 的 Azure IoT 中心设备 SDK 将遥测数据从设备发送到 IoT Central。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 04/27/2021
ms.openlocfilehash: 2464d9d4e6a945620bdbf80728aa3e7df0e2776e
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226645"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>快速入门：将遥测数据从设备发送到 Azure IoT Central (Python)

**适用对象**：[设备应用程序开发人员](about-iot-develop.md#device-application-development)<br>
**完成时间**：12 分钟

本快速入门介绍一种基本的 IoT 应用程序开发工作流。 首先，创建一个云应用程序来管理 Azure IoT Central 中的设备。 然后使用 Azure IoT Python SDK 生成一个模拟恒温器设备，将其连接到 IoT Central，并发送遥测数据。

## <a name="prerequisites"></a>先决条件
- [Python 3.7](https://www.python.org/downloads/) 或更高版本。 若要检查 Python 版本，请运行 `python --version`。 

[!INCLUDE [iot-develop-create-central-app-with-device](../../includes/iot-develop-create-central-app-with-device.md)]

## <a name="configure-a-simulated-device"></a>配置模拟设备
本部分将使用 Python SDK 示例来配置模拟恒温器设备。

1. 使用 Windows CMD、PowerShell 或 Bash（适用于 Windows 或 Linux）打开一个终端。 你将使用该终端来安装 Python SDK，更新环境变量，并运行 Python 代码示例。

1. 将 [Azure IoT Python SDK 设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)复制到本地计算机。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 导航到示例目录。

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. 安装 Azure IoT Python SDK。
    ```console
    pip3 install azure-iot-device
    ```

1. 设置以下每个环境变量，使模拟设备能够连接到 IoT Central。 对于 `IOTHUB_DEVICE_DPS_ID_SCOPE`、`IOTHUB_DEVICE_DPS_DEVICE_KEY` 和 `IOTHUB_DEVICE_DPS_DEVICE_ID`，请使用保存的设备连接值。

    **Windows CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > 在 Windows CMD 中，请不要在变量值的两边加上引号。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash（Linux 或 Windows）**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

## <a name="send-telemetry"></a>发送遥测
配置系统后，即可运行代码。 此代码创建模拟恒温器，连接到 IoT Central 应用程序和设备实例，并发送遥测数据。

1. 在终端中，运行以下代码示例。 也可在 Python IDE 中运行 Python 示例代码。
    ```console
    python temp_controller_with_thermostats.py
    ```

    模拟设备连接到 IoT Central 应用程序后，它会连接到在该应用程序中创建的设备实例，并开始发送遥测数据。 控制台中显示了连接详细信息和遥测输出： 
    
    ```output
    c:\azure-iot-sdk-python\azure-iot-device\samples\pnp>python temp_controller_with_thermostats.py
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```

1. 在 IoT Central 中，选择“设备”，单击你的设备名称，然后选择“原始数据”选项卡。此视图显示模拟设备中的原始遥测数据 。 

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central 设备遥测原始输出":::
    
    设备现已建立安全连接，并在向 Azure IoT 发送遥测数据。
    
## <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中创建的 IoT Central 资源，可以将其删除。 或者，如果你打算继续学习本指南中的文档，可以保留你创建的应用程序，以将其重复用于其他示例。

若要删除 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure IoT Central 创建了应用程序和设备，然后使用 Azure IoT Python SDK 创建了模拟设备并发送了遥测数据。 你还使用了 IoT Central 来监视遥测数据。

接下来，浏览作为托管设备解决方案的 IoT Central 和 Azure SDK Python 代码示例。

> [!div class="nextstepaction"]
> [创建 IoT Central 应用程序](../iot-central/core/quick-deploy-iot-central.md)
> [!div class="nextstepaction"]
> [异步设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)
> [!div class="nextstepaction"]
> [同步设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)
