---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: bef7807c0df580a6763a69619cdaa3d9d29f72e6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521355"
---
还可以使用以下资源：

- [Python SDK 参考文档](/python/api/azure-iot-hub/azure.iot.hub?preserve-view=true&view=azure-python)
- [服务客户端示例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [数字孪生示例](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>IoT 中心服务客户端示例

本部分介绍使用 IoT 中心服务客户端和 **IoTHubRegistryManager** 和 **CloudToDeviceMethod** 类的 Python 示例。 使用 **IoTHubRegistryManager** 类通过设备孪生与设备状态交互。 还可以使用 **IoTHubRegistryManager** 类在 IoT 中心 [查询设备注册](../articles/iot-hub/iot-hub-devguide-query-language.md) 。 使用 **CloudToDeviceMethod** 类调用设备上的命令。 设备的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型定义设备实现的属性和命令。 在代码片段中， `device_id` 变量保存注册到 iot 中心的 iot 即插即用设备的设备 ID。

### <a name="get-the-device-twin-and-model-id"></a>获取设备克隆和模型 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的设备克隆和模型 ID，请执行以下操作：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>更新设备孪生

下面的代码段演示如何更新 `targetTemperature` 设备上的属性。 此示例演示如何在更新之前获取克隆的 `etag` 。 在设备的默认组件中定义属性：

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

以下代码片段演示如何更新组件的 `targetTemperature` 属性。 此示例演示如何在更新之前获取克隆的 `ETag` 。 在 **thermostat1** 组件中定义属性：

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

对于组件中的属性，属性修补程序类似于以下示例：

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Call 命令

以下代码片段演示如何调用 `getMaxMinReport` 默认组件中定义的命令：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

以下代码片段演示如何对组件调用 `getMaxMinReport` 命令。 命令在 **thermostat1** 组件中定义：

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中心数字输出示例

使用 **DigitalTwinClient** 类通过数字孪生与设备状态交互。 设备的 [DTDL](../articles/iot-pnp/concepts-digital-twin.md) 模型定义设备实现的属性和命令。

`device_id`变量包含注册到 iot 中心的 iot 即插即用设备的设备 ID。

### <a name="get-the-digital-twin-and-model-id"></a>获取数字克隆和型号 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的数字输出和型号 ID，请执行以下操作：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>更新数字克隆

下面的代码段演示如何更新 `targetTemperature` 设备上的属性。 在设备的默认组件中定义属性：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

以下代码片段演示如何更新组件的 `targetTemperature` 属性。 在 **thermostat1** 组件中定义属性：

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Call 命令

以下代码片段演示如何调用 `getMaxMinReport` 默认组件中定义的命令：

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

以下代码片段演示如何对组件调用 `getMaxMinReport` 命令。 命令在 **thermostat1** 组件中定义：

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>读取设备遥测

IoT 即插即用设备将在 DTDL 模型中定义的遥测发送到 IoT 中心。 默认情况下，IoT 中心会将遥测路由到事件中心终结点，你可以在其中使用该终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下面的代码段演示如何从默认事件中心终结点读取遥测数据。 此代码段中的代码来自 IoT 中心快速入门，将 [遥测数据从设备发送到 IoT 中心，并使用后端应用程序进行读取](../articles/iot-hub/quickstart-send-telemetry-python.md)：

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

以下来自前面代码的输出显示了 **恒温器** IoT 即插即用设备上只有默认组件发送的温度遥测。 " `dt-dataschema` 系统" 属性显示模型 ID：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

前面代码的以下输出显示了由多组件 **TemperatureController** IoT 即插即用设备发送的温度遥测。 " `dt-subject` 系统" 属性显示发送了遥测数据的组件的名称。 在此示例中，两个组件都是 `thermostat1` 和 `thermostat2` 在 DTDL 模型中定义的。 " `dt-dataschema` 系统" 属性显示模型 ID：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>读取设备克隆更改通知

可以将 IoT 中心配置为生成设备克隆更改通知，以路由到受支持的终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点，> 非遥测事件](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心为非组件恒温器设备生成设备克隆更改通知时，上一个 Python 代码片段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

当 IoT 中心为包含组件的设备生成设备克隆更改通知时，上一个 Python 代码片段中显示的代码将生成以下输出。 此示例显示了具有恒温器组件的温度传感器设备生成通知时的输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>读取数字克隆更改通知

可以配置 IoT 中心来生成数字克隆更改通知，以路由到受支持的终结点。 若要了解详细信息，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点，> 非遥测事件](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心生成非组件恒温器设备的数字克隆更改通知时，上一个 Python 代码片段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

当 IoT 中心为包含组件的设备生成数字克隆更改通知时，上一个 Python 代码片段中显示的代码将生成以下输出。 此示例显示了具有恒温器组件的温度传感器设备生成通知时的输出。 应用程序属性 `iothub-message-schema` ，并 `opType` 向你显示有关更改通知类型的信息：

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
