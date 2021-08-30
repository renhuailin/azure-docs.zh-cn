---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 8ba4091bcb023f33d7bb435616bc6d63c2f105d2
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397919"
---
## <a name="model-id-announcement"></a>模型 ID 公告

若要公布模型 ID，设备必须将其包含在连接信息中：

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

新的 `ClientOptions` 重载在用于初始化连接的所有 `DeviceClient` 方法中可用。

> [!TIP]
> 对于模块和 IoT Edge，使用 `ModuleClient` 代替 `DeviceClient`。

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

默认组件不需要任何特殊属性。

使用嵌套组件时，设备必须使用组件名称设置消息属性：

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

## <a name="read-only-properties"></a>只读属性

从默认组件报告属性不需要任何特殊构造：

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

使用下一个报告的属性更新设备孪生：

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

使用嵌套组件时，必须在组件名称中创建属性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

使用下一个报告的属性更新设备孪生：

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

## <a name="writable-properties"></a>可写属性

这些属性可以由设备设置或通过解决方案更新。 如果解决方案更新属性，客户端会接收到通知，即在 `DeviceClient` 或 `ModuleClient` 中的回调。 若要遵循 IoT 即插即用约定，设备必须通知服务属性已成功接收。

### <a name="report-a-writable-property"></a>报告可写属性

设备报告可写属性时，必须包含约定中定义的 `ack` 值。

从默认组件报告可写属性：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

使用下一个报告的属性更新设备孪生：

```json
{
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

若要从嵌套组件报告可写属性，孪生必须包括标记：

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

使用下一个报告的属性更新设备孪生：

```json
{
  "reported": {
    "thermostat1": {
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

### <a name="subscribe-to-desired-property-updates"></a>订阅所需的属性更新

服务可以更新所需的属性，从而在连接的设备上触发通知。 此通知包括更新后的所需属性，其中包括用于标识更新的版本号。 设备必须使用与报告的属性相同的 `ack` 消息进行响应。

默认组件会查看单个属性，并使用收到的版本创建报告的 `ack`：

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

设备孪生在所需部分和报告部分显示属性：

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

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

嵌套组件的设备孪生显示了所需部分和报告部分，如下所示：

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

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

### <a name="request-and-response-payloads"></a>请求和响应有效负载

命令使用类型定义其请求和响应有效负载。 设备必须反序列化传入的输入参数并串行化响应。 下面的示例演示如何实现具有在有效负载中定义的复杂类型的命令：

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

下面的代码片段演示了设备如何实现此命令定义，其中包括用于启用序列化和反序列化的类型：

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 请求和响应名称不存在于通过网络传输的串行化有效负载中。
