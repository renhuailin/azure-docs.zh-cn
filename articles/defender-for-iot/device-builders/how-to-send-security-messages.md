---
title: 发送 Defender for IoT 设备安全消息
description: 了解如何使用 Defender for IoT 发送安全消息。
ms.topic: how-to
ms.date: 2/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: 791e49c4e8f0e503c67f24e440fc229998b7b9da
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680486"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

本操作指南介绍了当你选择在不使用 Defender for IoT 代理的情况下收集和发送设备安全消息时使用的 Defender for IoT 服务功能，并说明了其用法。

本指南介绍如何：

> [!div class="checklist"]
> * 使用 Azure IoT C SDK 发送安全消息
> * 使用 Azure IoT C# SDK 发送安全消息
> * 使用 Azure IoT Python SDK 发送安全消息
> * 使用 Azure IoT Node.js SDK 发送安全消息
> * 使用 Azure IoT Java SDK 发送安全消息

## <a name="defender-for-iot-capabilities"></a>Defender for IoT 功能

Defender for IoT 可以处理和分析任何类型的安全消息数据，前提是发送的数据符合 Defender for IoT 架构并且消息已设置为安全消息。

## <a name="security-message"></a>安全消息

Defender for IoT 使用以下条件定义安全消息：

- 如果消息是使用 Azure IoT SDK 发送的
- 如果消息符合安全消息架构
- 如果消息在发送之前已设置为安全消息

每个安全消息都包含发送方的元数据，如 `AgentId`、`AgentVersion`、`MessageSchemaVersion` 和一个安全事件列表。
架构定义安全消息的有效属性和必需属性，包括事件类型。

> [!NOTE]
> 发送的消息如果不符合该架构，则将被忽略。 在开始发送数据之前，请务必验证架构，因为当前不会存储已忽略的消息。

> [!NOTE]
> 未使用 Azure IoT SDK 设置为安全消息的已发送消息将不会路由到 Defender for IoT 管道。

## <a name="valid-message-example"></a>有效消息示例

下面的示例显示了有效的安全消息对象。 该示例包含消息元数据和一个 `ProcessCreate` 安全事件。

一旦设置为安全消息并已发送，此消息将由 Defender for IoT 处理。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>发送安全消息

通过使用 [Azure IoT C 设备 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)、[Azure IoT C# 设备 SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)、[Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node)、[Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) 或 [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java)（而不使用 Defender for IoT 代理）发送安全消息。

若要从设备发送设备消息以供 Defender for IoT 处理，请使用以下 API 之一来标记消息，以便将消息正确路由到 Defender for IoT 处理管道。

即使已使用正确的标头进行标记，发送的所有数据也必须符合 Defender for IoT 消息架构。

### <a name="send-security-message-api"></a>发送安全消息 API

“发送安全消息”API 目前可在 C 和 C#、Python、Node.js 以及 Java 中使用。

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

若要使用 Python API，需要安装 [azure-iot-device](https://pypi.org/project/azure-iot-device/) 包。

使用 Python API 时，可以使用唯一设备或模块连接字符串，通过模块或通过设备发送安全消息。 使用以下 Python 脚本示例时，如果与之配合使用的是设备，请使用 IoTHubDeviceClient；如果与之配合使用的是模块，请使用 IoTHubModuleClient。

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>后续步骤

- 阅读 Defender for IoT 服务[概述](overview.md)
- 详细了解 Defender for IoT [什么是基于代理的设备生成器解决方案](architecture-agent-based.md)
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读 [Azure Defender for IoT 代理常见问题解答](resources-agent-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
