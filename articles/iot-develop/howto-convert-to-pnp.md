---
title: 将现有设备转换为使用 IoT 即插即用 | Microsoft Docs
description: 本文介绍如何通过创建设备型号，然后在设备连接时发送模型 ID，转换现有设备代码以使用 IoT 即插即用。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442562"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>如何将现有设备转换为 IoT 即插即用设备

本文概述了将现有设备转换为 IoT 即插即用设备时应遵循的步骤。 其中介绍了如何创建每个 IoT 即插即用设备所需的模型，以及支持设备充当 IoT 即插即用设备所需的必要代码更改。

关于代码示例，本文介绍了使用 MQTT 库连接到 IoT 中心的 C 代码。 可以将本文中所述的更改应用到使用其他语言和 SDK 实现的设备。

如何将现有设备转换为 IoT 即插即用设备：

1. 查看设备代码，了解它实现的遥测、属性和命令。
1. 创建一个用于描述设备实现的遥测、属性和命令的模型。
1. 修改设备代码，使模型连接服务时通知模型 ID。

## <a name="review-your-device-code"></a>查看设备代码

在为设备创建模型之前，需要了解设备的现有功能：

- 设备定期发送的遥测。
- 设备与服务进行同步的只读和可写属性。
- 从设备响应的服务调用的命令。

例如，查看以下实现各种设备功能的设备代码片段。 这些示例基于之前的 [PnPMQTTWin32-Before](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before) 中的示例：

以下代码片段显示了发送温度遥测的设备：

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

遥测字段的名称为 `temperature`，其类型为浮点型或双精度。 此遥测类型的模型定义类似于下面的 JSON。 若要了解模式，请参阅下面的[设计模型](#design-a-model)：

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

以下代码片段显示报告属性值的设备：

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

属性的名称为 `maxTempSinceLastReboot`，其类型为浮点型或双精度。 此属性由设备报告，设备从不从服务接收此值的更新。 此遥测类型的模型定义类似于下面的 JSON。 若要了解模式，请参阅下面的[设计模型](#design-a-model)：

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

以下代码片段显示了响应来自服务的消息的设备：

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

`$iothub/methods/POST/getMaxMinReport/` 主题接收到从服务调用的命令的请求 `getMaxMinReport`，此请求可能包含具有命令参数的有效负载。 设备发送包含 `maxTemp` 和 `minTemp` 值的有效负载的响应。

`$iothub/twin/PATCH/properties/desired/` 主题从服务接收属性更新。 此示例假设属性更新适用于 `targetTemperature` 属性。 它使用类似于 `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}` 的确认来做出响应。

概括而言，此示例实现了以下功能：

| 名称                   | 功能类型   | 详细信息 |
| ---------------------- | ----------------- | ------- |
| 温度            | 遥测         | 假定数据类型为双精度 |
| maxTempSinceLastReboot | 属性          | 假定数据类型为双精度 |
| targetTemperature      | 可写属性 | 类型为整数 |
| getMaxMinReport        | Command           | 返回类型为双精度的 `maxTemp` 和 `minTemp` 字段的 JSON |

## <a name="design-a-model"></a>设计模型

每个 IoT 即插即用设备都有一个用于描述设备的特性和功能的模型。 模型使用[数字孪生体定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)来描述设备功能。

对于映射设备现有功能的简单模型，请使用遥测、属性和命令 DTDL 元素。  

上一部分中所述示例的 DTDL 模型如以下示例所示：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max and min temperature.",
      "request": {
      },
      "response": {
        "name": "tempReport",
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
            }
          ]
        }
      }
    }
  ]
}
```

在此模型中：

- `name` 和 `schema` 值映射到设备发送和接收的数据。
- 所有功能都在一个接口中进行分组。
- `@type` 字段标识 DTDL 类型，如属性和命令 。
- `unit`、`displayName` 和 `description` 等字段提供服务使用的额外信息。 例如，IoT Central 在设备仪表板上显示数据时使用这些值。

若要了解详细信息，请参阅 [IoT 即插即用约定](concepts-convention.md)和[ IoT 即插即用建模指南](concepts-modeling-guide.md)。

## <a name="update-the-code"></a>更新代码

如果设备已在使用 IoT 中心或 IoT Central，则无需对其遥测、属性和命令功能的实现进行任何更改。 若要使设备遵循 IoT 即插即用约定，请修改设备连接到服务的方式，以便其公布你创建的模型的 ID。 然后，服务可以使用该模型来了解设备功能。 例如，IoT Central 可以使用模型 ID 自动从存储库中检索模型，并为设备生成设备模板。

IoT 设备通过设备预配服务 (DPS) 或直接使用连接字符串连接到 IoT 服务。

如果设备使用 DPS 进行连接，请在注册设备时在发送的有效负载中包含模型 ID。 对于之前所示的示例模型，有效负载如下所示：

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

若要了解详细信息，请参阅[运行时注册 - 注册设备](/rest/api/iot-dps/device/runtime-registration/register-device)。

如果设备使用 DPS 连接或直接使用连接字符串连接，则请在代码连接到 IoT 中心时包括模型 ID。 例如：

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将现有设备转换为 IoT 即插即用设备，建议的下一步是阅读 [IoT 即插即用建模指南](concepts-modeling-guide.md)。
