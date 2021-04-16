---
title: 向 Azure IoT Central 发送设备遥测数据快速入门 (Python)
description: 在本快速入门中，你将使用适用于 Python 的 Azure IoT 中心设备 SDK 将遥测数据从设备发送到 IoT Central。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047161"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>快速入门：将遥测数据从设备发送到 Azure IoT Central (Python)

**适用于**：[设备应用程序开发](about-iot-develop.md#device-application-development)

本快速入门介绍一个基本的 IoT 设备应用程序开发工作流。 首先使用 Azure IoT Central 创建一个云应用程序。 然后使用 Azure IoT Python SDK 生成一个模拟设备，连接到 IoT Central，并发送设备到云的遥测数据。 

## <a name="prerequisites"></a>先决条件
- [Python 3.7+](https://www.python.org/downloads/)。 有关支持的其他 Python 版本，请参阅 [Azure IoT 设备功能](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)。
    
    为了确保你的 Python 版本是最新的，请运行 `python --version`。 如果你同时安装了 Python 2 和 Python 3，而使用的是 Python 3 环境，请使用 `pip3` 安装所有库。 运行 `pip3` 可确保将库安装到 Python 3 运行时。
    > [!IMPORTANT]
    > 在 Python 安装程序中，选择“将 Python 添加到 PATH”选项。 如果已安装 Python 3.7 或更高版本，请确认你已将 Python 安装文件夹添加到 `PATH` 环境变量。

## <a name="create-an-application"></a>创建应用程序
在本部分，你将创建一个 IoT Central 应用程序。 IoT Central 是基于门户的 IoT 应用程序平台，可帮助降低开发和管理 IoT 解决方案的复杂性与成本。

若要创建 Azure IoT Central 应用程序，请执行以下操作：
1. 浏览到 [Azure IoT Central](https://apps.azureiotcentral.com/)，并使用 Microsoft 个人帐户、工作帐户或学校帐户登录。
1. 导航到“生成”，然后选择“自定义应用”。 
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="IoT Central 起始页":::
1. 在“应用程序名称”中，输入唯一的名称或使用已生成的名称。
1. 在“URL”中，输入易记的应用程序 URL 前缀，或使用已生成的 URL 前缀。
1. 将“应用程序模板”保持设置为“自定义应用程序”。 如果你的帐户中已有任何模板，下拉列表可能会显示其他选项。
1. 选择一个“定价计划”选项。 
    - 若要免费使用该应用程序 7 天，请选择“免费”。 在免费的应用程序过期之前，可将其转换为标准定价。
    - 或者，可以选择标准定价计划。 如果选择标准定价，将会显示更多选项，并且你将需要设置“目录”、“Azure 订阅”和“位置”。   若要了解定价，请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。 
        - “目录”是将在其中创建应用程序的 Azure Active Directory  。 Azure Active Directory 包含用户标识、凭据和其他组织信息。 如果你没有 Azure Active Directory，则在你创建 Azure 订阅时，系统会创建一个。
        - 使用 **Azure 订阅** 可以创建 Azure 服务的实例。 IoT Central 将在订阅中预配资源。 如果你没有 Azure 订阅，可以[免费创建一个](https://azure.microsoft.com/free/)。 创建订阅后，返回到 IoT Central 的“新建应用程序”页。 新订阅显示在“Azure 订阅”下拉列表中。
        - “位置”是要在其中创建应用程序的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 请选择实际上距离设备最近的位置以获得最佳性能。 选择一个位置后，无法将应用程序转移到其他位置。

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="IoT Central -“新建应用程序”对话框":::
1. 选择“创建”。
    
    IoT Central 在创建应用程序后，会将你重定向到应用程序仪表板。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central -“新建应用程序”仪表板":::

## <a name="add-a-device"></a>添加设备
在本部分，你要将新设备添加到 IoT Central 应用程序。 该设备是设备模板的实例，表示要连接到应用程序的真实设备或模拟设备。 

若要创建新设备，请执行以下操作：
1. 在左窗格中，依次选择“设备”、“+新建”。  此时会打开“新建设备”对话框。
1. 将“设备模板”保持设置为“未分配”。

    > [!NOTE]
    > 为简单起见，在本快速入门中，你将连接一个使用未分配的模板的模拟设备。 如果你一直使用 IoT Central 来管理设备，那么你将会了解如何使用设备模板。 有关使用设备模板的概述，请参阅[快速入门：将模拟设备添加到 IoT Central 应用程序](../iot-central/core/quick-create-simulated-device.md)。
1. 设置易记的“设备名称”和“设备 ID”。  或者，也可以使用生成的值。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="IoT Central -“新建设备”对话框":::
1. 选择“创建”。

    创建的设备显示在“所有设备”列表中。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="IoT Central -“所有设备”列表":::
    
若要检索新设备的连接详细信息，请执行以下操作：
1. 在“所有设备”列表中，双击链接设备的名称以显示详细信息。 
1. 在顶部菜单中，选择“连接”。

    “设备连接”对话框显示了连接详细信息：:::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central 设备连接详细信息":::
1. 将“设备连接”对话框中的以下值复制到安全位置。 在下一部分，你要使用这些值将设备连接到 IoT Central。
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>发送消息并监视遥测数据
在本部分，你将使用 Python SDK 生成模拟设备，并将遥测数据发送到 IoT Central 应用程序。 

1. 使用 Windows CMD、PowerShell 或 Bash（适用于 Windows 或 Linux）打开一个终端。 你将使用该终端来安装 Python SDK，更新环境变量，并运行 Python 代码示例。

1. 将 [Azure IoT Python SDK 设备示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)复制到本地计算机。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 导航到“azure-iot-sdk-python/azure-iot-device/samples”目录。

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. 安装 Azure IoT Python SDK。

    ```console
    pip install azure-iot-device
    ```

1. 设置以下每个环境变量，使模拟设备能够连接到 IoT Central。 对于 `ID_SCOPE`、`DEVICE_ID` 和 `DEVICE_KEY`，请使用在 IoT Central“设备连接”对话框中保存的值。

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > 在 Windows CMD 中，请不要在连接字符串或其他变量值的两边加上引号。

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash（Linux 或 Windows）**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. 在终端中，运行示例文件 *simple_send_temperature.py 的代码。 此代码访问模拟的 IoT 设备并将消息发送到 IoT Central。

    若要在终端中运行 Python 示例：
    ```console
    python ./simple_send_temperature.py
    ```

    或者，可以在 Python IDE 中运行示例中的 Python 代码：
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

当 Python 代码将消息从设备发送到 IoT Central 应用程序时，消息将显示在 IoT Central 中设备的“原始数据”选项卡上。 可能需要刷新页面才能显示最近的消息。

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central 原始数据输出的屏幕截图":::

设备现已建立安全连接，并在向 Azure IoT 发送遥测数据。

## <a name="clean-up-resources"></a>清理资源
如果你不再需要本教程中创建的 IoT Central 资源，可以从 IoT Central 门户将其删除。 或者，如果你打算继续学习本指南中的文档，可以保留你创建的应用程序，以将其重复用于其他示例。

若要删除 Azure IoT Central 示例应用程序及其所有设备和资源，请执行以下操作：
1. 选择“管理” > “你的应用程序”。 
1. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure IoT Central 创建了应用程序和设备，然后使用 Azure IoT Python SDK 创建了模拟设备并发送了遥测数据。 你还使用了 IoT Central 来监视遥测数据。

接下来，请通过应用程序示例了解 Azure IoT Python SDK。

- [异步示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)：此目录包含适用于其他 IoT 中心方案的异步 Python 示例。
- [同步示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)：此目录包含与 Python 2.7 配合使用的 Python 示例，或者与适用于 Python 3.6+ 的同步兼容性方案配合使用的 Python 示例
- [IoT Edge 示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)：此目录包含有关使用 Edge 模块和下游设备的 Python 示例。
