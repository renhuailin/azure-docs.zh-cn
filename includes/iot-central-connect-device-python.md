---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 241e7fc5797a0ebff062d8d9f814091ae3e09571
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114407810"
---
[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要准备好以下资源：

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- 装有 [Python](https://www.python.org/) 3.7 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `python --version`。 Python 适用于各种操作系统。 本教程中的说明假设在 Windows 命令提示符下运行 python 命令。

- [适用于 Python 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 存储库（包含示例代码）的本地副本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="review-the-code"></a>查看代码

在先前下载的适用于 Python 的 Microsoft Azure IoT SDK 副本中，使用文本编辑器打开 azure-iot-sdk-python/azure-iot-device/samples/pnp/simple_thermostat.py 文件。

当你运行连接到 IoT Central 的示例时，它将使用设备预配服务 (DPS) 来注册设备并生成连接字符串。 该示例从命令行环境中检索其所需的 DPS 连接信息。

`main` 函数：

* 使用 DPS 预配设备。 预配信息包括模型 ID。 IoT Central 使用模型 ID 识别或生成此设备的设备模板。 若要了解详细信息，请参阅[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 在打开连接之前，创建 `Device_client` 对象并设置 `dtmi:com:example:TemperatureController;2` 模型 ID。
* 向 IoT Central 发送初始属性值。 它使用 `pnp_helper` 创建补丁。
* 为 `getMaxMinReport` 和 `reboot` 命令创建侦听器。 每个恒温器组件都有专属的 `getMaxMinReport` 命令。
* 创建用于侦听可写属性更新的属性侦听器。
* 开始循环，每隔 8 秒从两个恒温器组件发送一次温度遥测数据，以及从默认组件发送一次工作集遥测数据。

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":
            print("Device was assigned")
            print(registration_result.registration_state.assigned_hub)
            print(registration_result.registration_state.device_id)
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":
        # ...

    # Connect the client.
    await device_client.connect()

    ################################################
    # Update readable properties from various components

    properties_root = pnp_helper.create_reported_properties(serialNumber=serial_number)
    properties_thermostat1 = pnp_helper.create_reported_properties(
        thermostat_1_component_name, maxTempSinceLastReboot=98.34
    )
    properties_thermostat2 = pnp_helper.create_reported_properties(
        thermostat_2_component_name, maxTempSinceLastReboot=48.92
    )
    properties_device_info = pnp_helper.create_reported_properties(
        device_information_component_name,
        swVersion="5.5",
        manufacturer="Contoso Device Corporation",
        model="Contoso 4762B-turbo",
        osName="Mac Os",
        processorArchitecture="x86-64",
        processorManufacturer="Intel",
        totalStorage=1024,
        totalMemory=32,
    )

    property_updates = asyncio.gather(
        device_client.patch_twin_reported_properties(properties_root),
        device_client.patch_twin_reported_properties(properties_thermostat1),
        device_client.patch_twin_reported_properties(properties_thermostat2),
        device_client.patch_twin_reported_properties(properties_device_info),
    )

    ################################################
    # Get all the listeners running
    print("Listening for command requests and property updates")

    global THERMOSTAT_1
    global THERMOSTAT_2
    THERMOSTAT_1 = Thermostat(thermostat_1_component_name, 10)
    THERMOSTAT_2 = Thermostat(thermostat_2_component_name, 10)

    listeners = asyncio.gather(
        execute_command_listener(
            device_client, method_name="reboot", user_command_handler=reboot_handler
        ),
        execute_command_listener(
            device_client,
            thermostat_1_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_command_listener(
            device_client,
            thermostat_2_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    ################################################
    # Function to send telemetry every 8 seconds

    async def send_telemetry():
        print("Sending telemetry from various components")

        while True:
            curr_temp_ext = random.randrange(10, 50)
            THERMOSTAT_1.record(curr_temp_ext)

            temperature_msg1 = {"temperature": curr_temp_ext}
            await send_telemetry_from_temp_controller(
                device_client, temperature_msg1, thermostat_1_component_name
            )

            curr_temp_int = random.randrange(10, 50)  # Current temperature in Celsius
            THERMOSTAT_2.record(curr_temp_int)

            temperature_msg2 = {"temperature": curr_temp_int}

            await send_telemetry_from_temp_controller(
                device_client, temperature_msg2, thermostat_2_component_name
            )

            workingset_msg3 = {"workingSet": random.randrange(1, 100)}
            await send_telemetry_from_temp_controller(device_client, workingset_msg3)

    send_telemetry_task = asyncio.ensure_future(send_telemetry())

    # ...
```

`provision_device` 函数使用 DPS 来预配设备并通过 IoT Central 注册设备。 该函数包括设备型号 ID，在预配有效负载中，IoT Central 使用该设备型号 ID [将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)：

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )

    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener` 函数处理命令请求，在设备收到有关恒温器组件的 `getMaxMinReport` 命令时运行 `max_min_handler` 以及在设备收到 `reboot` 命令时运行 `reboot_handler` 函数。 它使用 `pnp_helper` 模块来生成响应：

```python
async def execute_command_listener(
    device_client,
    component_name=None,
    method_name=None,
    user_command_handler=None,
    create_user_response_handler=None,
):
    while True:
        if component_name and method_name:
            command_name = component_name + "*" + method_name
        elif method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        values = command_request.payload
        print(values)

        if user_command_handler:
            await user_command_handler(values)
        else:
            print("No handler provided to execute")

        (response_status, response_payload) = pnp_helper.create_response_payload_with_status(
            command_request, method_name, create_user_response=create_user_response_handler
        )

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener` 处理恒温器组件的可写属性更新（例如 `targetTemperature`）并生成 JSON 响应。 它使用 `pnp_helper` 模块来生成响应：

```python
async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        print(patch)
        properties_dict = pnp_helper.create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

`send_telemetry_from_temp_controller` 函数将恒温器组件的遥测数据消息发送到 IoT Central。 它使用 `pnp_helper` 模块来生成消息：

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = pnp_helper.create_telemetry(telemetry_msg, component_name)
    await device_client.send_message(msg)
    print("Sent message")
    print(msg)
    await asyncio.sleep(5)
```

## <a name="get-connection-information"></a>获取连接信息

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>运行代码

若要运行示例应用程序，请打开命令行环境并导航到包含 temp_controller_with_thermostats.py 示例文件的 azure-iot-sdk-python/azure-iot-device/samples/pnp 文件夹。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

安装所需的包：

```cmd/sh
pip install azure-iot-device
```

运行示例：

```cmd/sh
python temp_controller_with_thermostats.py
```

以下输出显示设备注册并连接到 IoT Central。 该示例发送两个恒温器组件的 `maxTempSinceLastReboot` 属性，然后才开始发送遥测数据：

```cmd/sh
Device was assigned
iotc-60a.....azure-devices.net
sample-device-01
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
{"temperature": 27}
Sent message
{"temperature": 17}
Sent message
{"workingSet": 13}
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

可以看到设备如何对命令和属性更新做出响应：

```cmd/sh
{'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
the data in the desired properties patch was: {'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
Values received are :-
{'targetTemperature': 67, '__t': 'c'}
Sent message

...

Command request received with payload
2021-03-31T05:00:00.000Z
Will return the max, min and average temperature from the specified time 2021-03-31T05:00:00.000Z to the current time
Done generating
{"avgTemp": 4.0, "endTime": "2021-03-31T12:29:48.322427", "maxTemp": 18, "minTemp": null, "startTime": "2021-03-31T12:28:28.322381"}
```
