---
title: 使用 Azure IoT 中心安排作业 (Python) | Microsoft Docs
description: 如何安排 Azure IoT 中心作业对多台设备调用直接方法。 使用 Azure IoT SDK for Python 实现模拟设备应用以及用于运行作业的服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 7aac4d2fcab192d77c1629e8f53b91f5dadedd86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729837"
---
# <a name="schedule-and-broadcast-jobs-python"></a>计划和广播作业 (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT 中心是一项完全托管的服务，允许后端应用创建和跟踪用于计划和更新数百万台设备的作业。  作业可以用于以下操作：

* 更新所需属性
* 更新标记
* 调用直接方法

从概念上讲，作业包装这些操作之一，并跟踪针对一组设备执行（由设备孪生查询定义）的进度。  例如，后端应用可使用作业重启 10,000 台设备（由设备孪生查询指定并计划在将来执行）。  该应用程序随后可以在其中每个设备接收和执行重新启动方法时跟踪进度。

可在以下文章中了解有关所有这些功能的详细信息：

* 设备孪生和属性：[设备孪生入门](iot-hub-python-twin-getstarted.md)和[教程：如何使用设备孪生属性](tutorial-device-twins.md)

* 直接方法：[IoT 中心开发人员指南 - 直接方法](iot-hub-devguide-direct-methods.md)和[快速入门：直接方法](./quickstart-control-device.md?pivots=programming-language-python)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个 Python 模拟设备应用，该应用具有一种直接方法，使 lockDoor 能够由解决方案后端调用。

* 创建 Python 控制台应用，它使用作业在模拟设备应用中调用 lockDoor 直接方法，并使用设备作业更新所需属性。

在本教程结束时，会创建两个 Python 应用：

simDevice.py，它使用设备标识连接到 IoT 中心并接收 lockDoor 直接方法。

scheduleJobService.py，它调用模拟设备应用中的直接方法，并通过作业更新设备孪生的所需属性。

> [!NOTE]
> Azure IoT SDK for Python 不直接支持作业功能。 本教程中转而提供一种利用异步现成和计时器的备选解决方案。 有关进一步的更新，请参阅 [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) 页面上的 **服务客户端 SDK** 功能列表。
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分将创建一个 Python 控制台应用，用于响应通过云调用的方法，这会触发模拟 lockDoor 方法。

1. 在命令提示符处，运行以下命令以安装 **azure-iot-device** 包：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文本编辑器，在工作目录中创建一个 simDevice.py 文件。

3. 在 simDevice.py 文件的开头添加以下 `import` 语句和变量。 将 `deviceConnectionString` 替换为上述创建的设备的连接字符串：

    ```python
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
    ```

4. 定义以下处理程序函数，该函数将用于响应 lockDoor 方法：

    ```python
    def method_request_handler(method_request):
        if method_request.name == "lockDoor":
            print("Locking Door!")

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse.create_from_method_request(
                method_request=method_request,
                status=resp_status,
                payload=resp_payload
            )
            client.send_method_response(method_response)
    ```

5. 再添加一个处理程序函数来接收设备孪生更新：

    ```python
    def twin_patch_handler(twin_patch):
        print("")
        print("Twin desired properties patch received:")
        print(twin_patch)
    ```

6. 添加以下代码以注册 lockDoor 方法的处理程序以及孪生修补程序。 此外还包含 `main` 例程：

    ```python
    def iothub_jobs_sample_run():
        print("Beginning to listen for 'lockDoor' direct method invocations...")
        client.on_method_request_received = method_request_handler
        print("Beginning to listen for updates to the Twin desired properties...")
        client.on_twin_desired_properties_patch_received = twin_patch_handler

        client.connect()

        try:
            while True:
                import time
                time.sleep(100)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped!")
            client.shutdown()

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. 保存并关闭 simDevice.py 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
>

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一个在设备上调用直接方法并更新设备孪生的后端服务。 服务需要“服务连接”权限才能在设备上调用直接方法。 服务还需要“注册表读取”和“注册表写入”权限才能读取和写入标识注册表。 没有仅包含这些权限的默认共享访问策略，因此需要创建一个。

若要创建授予“服务连接”、“注册表读取”和“注册表写入”权限的共享访问策略，并获取此策略的连接字符串，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开 IoT 中心。 若要转到 IoT 中心，最简单的方法是选择“资源组”，接着选择 IoT 中心所在的资源组，然后从资源列表中选择该 IoT 中心。

2. 在 IoT 中心的左侧窗格上，选择“共享访问策略”。

3. 在策略列表上方的顶部菜单中，选择“添加”。

4. 在“添加共享访问策略”窗格中，为策略输入一个说明性名称，例如 serviceAndRegistryReadWrite。 在 **权限** 下，选择“服务连接”和“注册表写入”（选择“注册表写入”时，会自动选择“注册表读取”）。 然后选择“创建”。

    ![演示如何添加新的共享访问策略](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 回到“共享访问策略”窗格，从策略列表中选择新的策略。

6. 在“共享访问密钥”下，选择“连接字符串 - 主密钥”的复制图标并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息，请参阅[访问控制和权限](./iot-hub-dev-guide-sas.md#access-control-and-permissions)。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>安排作业，用于调用直接方法和更新设备孪生的属性

在本部分中，将创建一个 Python 控制台应用，该应用使用直接方法在设备上启动远程 lockDoor，还更新设备孪生所需的属性。

1. 在命令提示符处，运行以下命令以安装 **azure-iot-hub** 包：

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 使用文本编辑器，在工作目录中创建一个 scheduleJobService.py 文件。

3. 在 scheduleJobService.py 文件的开头添加以下 `import` 语句和变量。 将 `{IoTHubConnectionString}` 占位符替换为先前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。 将 `{deviceId}` 占位符替换为在[在 IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID：

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 添加以下用于查询设备的函数：

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. 添加以下方法，用于运行调用直接方法和设备孪生的作业：

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 添加以下代码以安排作业和更新作业状态。 此外还包含 `main` 例程：

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. 保存并关闭 scheduleJobService.py 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在工作目录的命令提示符处，运行以下命令以开始侦听重启直接方法：

    ```cmd/sh
    python simDevice.py
    ```

2. 在工作目录的另一命令提示符处运行以下命令，以触发作业进行锁门和孪生项的更新：
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 可在控制台查看针对直接方法和设备孪生的设备响应。

    ![IoT 中心作业示例 1 - 设备输出](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 中心作业示例 2 - 设备输出](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>后续步骤

在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅[如何更新固件](tutorial-firmware-update.md)。