---
title: Azure IoT 中心设备管理入门 (Python) | Microsoft Docs
description: 如何使用 IoT 中心设备管理进行远程设备重启。 使用适用于 Python 的 Azure IoT SDK 实现包含直接方法的模拟设备应用和调用直接方法的服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1739e06e1d3da24a069ef2508d8e2ce9f8164675
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835794"
---
# <a name="get-started-with-device-management-python"></a>设备管理入门 (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。

* 创建包含重新启动该设备的直接方法的模拟设备应用。 直接方法是从云中调用的。

* 创建一个 Python 控制台应用，它通过 IoT 中心调用模拟设备应用中的重新启动直接方法。

在本教程结束时，会获得两个 Python 控制台应用：

* **dmpatterns_getstarted_device.py**，它使用先前创建的设备标识连接到 IoT 中心，接收重新启动直接方法，模拟物理重新启动，并报告上次重新启动的时间。

* **dmpatterns_getstarted_service.py**，它调用模拟设备应用中的直接方法，显示响应，并显示更新后的报告属性。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分的操作：

* 创建一个 Python 控制台应用，用于响应通过云调用的直接方法

* 模拟设备重新启动

* 通过报告的属性，设备孪生查询可标识设备及设备上次重新启动的时间

1. 在命令提示符处，运行以下命令以安装 **azure-iot-device** 包：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文本编辑器，在工作目录中创建名为 dmpatterns_getstarted_device.py 的文件。

3. 在 **dmpatterns_getstarted_device.py** 文件开头添加以下 `import` 语句。

    ```python
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. 添加 CONNECTION_STRING 变量。 将 `{deviceConnectionString}` 占位符值替换为设备连接字符串。 以前在[在 IoT 中心内注册新设备](#register-a-new-device-in-the-iot-hub)中复制了此连接字符串。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. 添加以下函数以实例化为设备上的直接方法配置的客户端。

    ```python
    def create_client():
        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        # Define the handler for method requests
        def method_request_handler(method_request):
            if method_request.name == "rebootDevice":
                # Act on the method by rebooting the device
                print("Rebooting device")
                time.sleep(20)
                print("Device rebooted")

                # ...and patching the reported properties
                current_time = str(datetime.datetime.now())
                reported_props = {"rebootTime": current_time}
                client.patch_twin_reported_properties(reported_props)
                print( "Device twins updated with latest rebootTime")

                # Create a method response indicating the method request was resolved
                resp_status = 200
                resp_payload = {"Response": "This is the response from the device"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            
            else:
                # Create a method response indicating the method request was for an unknown method
                resp_status = 404
                resp_payload = {"Response": "Unknown method"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)

            # Send the method response
            client.send_method_response(method_response)

        try:
            # Attach the handler to the client
            client.on_method_request_received = method_request_handler
        except:
            # In the event of failure, clean up
            client.shutdown()

        return client
    ```

6. 启动直接方法示例并等待。

    ```python
    def main():
        print ("Starting the IoT Hub Python sample...")
        client = create_client()

        print ("Waiting for commands, press Ctrl-C to exit")
        try:
            # Wait for program exit
            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()

    if __name__ == '__main__':
        main()
    ```

7. 保存并关闭 **dmpatterns_getstarted_device.py** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动

此部分将创建一个 Python 控制台应用，以使用直接方法在设备上启动远程重新启动。 该应用使用设备孪生查询来搜索该设备的上次重新启动时间。

1. 在命令提示符处，运行以下命令以安装 **azure-iot-hub** 包：

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 使用文本编辑器，在工作目录中创建名为 dmpatterns_getstarted_service.py 的文件。

3. 在 **dmpatterns_getstarted_service.py** 文件开头添加以下 `import` 语句。

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. 添加以下变量声明。 将 `{IoTHubConnectionString}` 占位符值替换为以前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。 将 `{deviceId}` 占位符值替换为在[在 IoT 中心内注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. 添加以下函数以调用设备方法重新启动目标设备，然后查询设备孪生并获取上次重新启动时间。

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. 保存并关闭 **dmpatterns_getstarted_service.py** 文件。

## <a name="run-the-apps"></a>运行应用

现在可以运行应用了。

1. 在命令提示符处，运行以下命令以开始侦听重新启动直接方法。

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. 在另一个命令提示符处，运行以下命令以触发远程重新启动并查询设备孪生以查找上次重新启动时间。

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. 可在控制台查看对直接方法的设备响应。

   下面显示了对重新启动直接方法的设备响应：

   ![模拟设备应用输出](./media/iot-hub-python-python-device-management-get-started/device.png)

   下面显示服务调用重新启动直接方法并轮询设备孪生以获取状态：

   ![触发重新启动服务输出](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
