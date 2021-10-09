---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 14522e6395e40e93c0eb558597ad090521a480a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580442"
---
## <a name="model-id-announcement"></a>模型 ID 公告

若要公布模型 ID，设备必须将其包含在连接信息中：

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> 对于模块和 IoT Edge，使用 `IoTHubModuleClient` 代替 `IoTHubDeviceClient`。

> [!TIP]
> 设备只有这一次机会设置模型 ID，设备连接后将无法更新模型 ID。

## <a name="dps-payload"></a>DPS 有效负载

使用[设备预配服务 (DPS)](../articles/iot-dps/about-iot-dps.md) 的设备可以包含要在使用以下 JSON 有效负载的预配过程中使用的 `modelId`。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="use-components"></a>使用组件

如[了解 IoT 即插即用模型中的组件](../articles/iot-develop/concepts-modeling-guide.md)一文中所述，设备构建者必须决定是否要使用组件来描述其设备。 使用组件时，设备必须遵循以下各部分所述的规则。

## <a name="telemetry"></a>遥测

默认组件不需要向遥测消息添加任何特殊属性。

使用嵌套组件时，设备必须使用组件名称设置消息属性：

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

## <a name="read-only-properties"></a>只读属性

从默认组件报告属性不需要任何特殊构造：

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

使用嵌套组件时，属性必须在组件名称中创建并包括标记：

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

## <a name="writable-properties"></a>可写属性

这些属性可以由设备设置或通过解决方案更新。 如果解决方案更新属性，客户端会接收到通知，即在 `IoTHubDeviceClient` 或 `IoTHubModuleClient` 中的回调。 若要遵循 IoT 即插即用约定，设备必须通知服务属性已成功接收。

如果属性类型为 `Object`，则服务必须将完整的对象发送到设备，即使它只更新对象字段的子集，也是如此。 设备发送的确认也必须是完整的对象。

### <a name="report-a-writable-property"></a>报告可写属性

设备报告可写属性时，必须包含约定中定义的 `ack` 值。

从默认组件报告可写属性：

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

若要从嵌套组件报告可写属性，孪生必须包括标记：

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>订阅所需的属性更新

服务可以更新所需的属性，从而在连接的设备上触发通知。 此通知包括更新后的所需属性，其中包括用于标识更新的版本号。 设备必须在发送回服务的 `ack` 消息中包含此版本号。

默认组件会查看单个属性，并使用收到的版本创建报告的 `ack`：

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

嵌套组件的设备孪生显示了所需部分和报告部分，如下所示：

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

嵌套组件接收用组件名称包装的所需属性，并且应报告 `ack` 报告的属性：

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

组件的设备孪生显示了所需部分和报告部分，如下所示：

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

## <a name="commands"></a>命令

默认组件接收服务调用的命令名称。

嵌套组件接收带有组件名称和 `*` 分隔符前缀的命令名称。

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

### <a name="request-and-response-payloads"></a>请求和响应有效负载

命令使用类型定义其请求和响应有效负载。 设备必须反序列化传入的输入参数并串行化响应。 下面的示例演示如何实现具有在有效负载中定义的复杂类型的命令：

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

下面的代码片段演示了设备如何实现此命令定义，其中包括用于启用序列化和反序列化的类型：

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> 请求和响应名称不存在于通过网络传输的串行化有效负载中。
