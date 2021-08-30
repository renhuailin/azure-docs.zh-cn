---
title: 如何在 Azure IoT Central 解决方案中使用设备命令
description: 如何在 Azure IoT Central 解决方案中使用设备命令。 本教程介绍如何在 Azure IoT Central 应用程序的客户端应用中使用设备命令。
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6a1d252a6b9068697fe738d4d2b1c6bbf6ceeb5
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588266"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>如何在 Azure IoT Central 解决方案中使用命令

本操作指南介绍如何使用设备模板中定义的命令。

操作员可以使用 IoT Central UI 在设备上调用命令。 命令控制设备的行为。 例如，操作员可以调用命令来重新启动设备或收集诊断数据。

设备可以：

* 立即响应命令。
* 在收到命令时响应 IoT Central，然后在长时间运行的命令完成时通知 IoT Central。

默认情况下，命令期望设备是可以连接的，如果无法访问设备，则命令会失败。 如果在设备模板 UI 中选择“脱机时排队”选项，则可以将命令排入队列，直到设备处于联机状态。 本文将在后面的一个单独部分中介绍这些脱机命令。

## <a name="define-your-commands"></a>定义命令

标准命令被发送到设备，以指示设备执行某操作。 命令可以包含带有附加信息的参数。 例如，打开设备上阀门的命令可以有指定阀门打开程度的参数。 当设备完成命令时，命令也可以接收返回值。 例如，要求设备运行某些诊断的命令可以接收诊断报告作为返回值。

命令被定义为设备模板的一部分。 下面的屏幕截图展示了“恒温器”设备模板中的“Get Max-Min report”命令定义。 此命令同时包含请求参数和响应参数： 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="展示了“恒温器”设备模板中“Get Max-Min report”命令的屏幕截图":::

下表显示了命令功能的配置设置：

| 字段             |说明|
|-------------------|-----------|
|显示名称       |“仪表板磁贴”和“设备”窗体中使用的命令值。|
| 名称            | 命令的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。 设备代码使用此“名称”值。|
| 功能类型 | 命令。|
| 脱机时排队 | 是否让此命令成为脱机命令。 |
| 说明     | 命令功能的说明。|
| 评论     | 有关命令功能的任何注释。|
| 请求     | 设备命令的有效负载。|
| 响应     | 设备命令响应的有效负载。|

下面的代码片段展示了此命令在设备模型中的 JSON 表示形式。 在此示例中，响应值是包含多个字段的复杂对象类型：

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

> [!TIP]
> 可以在“设备模板”页中导出设备模型。

可以使用下面的字段将此命令定义与 UI 的屏幕截图相关联：

* `@type` 用于指定功能类型：`Command`
* `name` 用于命令值。

可选字段（例如显示名称和说明）允许你向接口和功能添加更多详细信息。

## <a name="standard-commands"></a>标准命令

此部分介绍了设备如何在接收到命令后立即发送响应值。

下面的代码片段展示了设备如何响应立即发送成功代码的命令：

> [!NOTE]
> 为了简单起见，本文使用 Node.js。 有关其他语言示例，请参阅[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程。

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

对 `onDeviceMethod` 的调用创建 `commandHandler` 方法。 此命令处理程序：

1. 检查命令名称。
1. 对于 `getMaxMinReport` 命令，它调用 `getMaxMinReportObject` 来检索要包含在返回对象中的值。
1. 调用 `sendCommandResponse`，以将响应发送回 IoT Central。 此响应包括指示成功的 `200` 响应代码。

下面的屏幕截图展示了成功命令响应如何显示在 IoT Central UI 中：

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="展示了如何查看标准命令的命令有效负载的屏幕截图":::

## <a name="long-running-commands"></a>长时间运行的命令

此部分介绍了设备如何延迟发送命令已完成的确认信息。

下面的代码片段展示了设备如何实现长时间运行的命令：

> [!NOTE]
> 为了简单起见，本文使用 Node.js。 有关其他语言示例，请参阅[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程。

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

对 `onDeviceMethod` 的调用创建 `commandHandler` 方法。 此命令处理程序：

1. 检查命令名称。
1. 调用 `sendCommandResponse`，以将响应发送回 IoT Central。 此响应包括指示挂起结果的 `202` 响应代码。
1. 完成长时间运行的操作。
1. 使用与命令同名的报告的属性来告知 IoT Central 命令已完成。

下面的屏幕截图展示了在收到 202 响应代码时命令响应如何显示在 IoT Central UI 中：

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="展示了来自设备的即时响应的屏幕截图":::

下面的屏幕截图展示了在收到指示命令已完成的属性更新时的 IoT Central UI：

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="展示了长时间运行的命令已完成的屏幕截图":::

## <a name="offline-commands"></a>脱机命令

此部分介绍了设备如何处理脱机命令。 如果设备处于联机状态，则可以在收到脱机命令后立即处理。 如果设备处于脱机状态，则会在下一次连接到 IoT Central 时处理脱机命令。 设备无法发送返回值来响应脱机命令。

> [!NOTE]
> 为了简单起见，本文使用 Node.js。

下面的屏幕截图展示了名为 GenerateDiagnostics 的脱机命令。 request 参数是一个对象，它包含名为 StartTime 的日期/时间属性和名为 Bank 的整数枚举属性：

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="展示了脱机命令的 UI 的屏幕截图":::

下面的代码片段展示了客户端如何侦听脱机命令并显示消息内容：

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

上面代码片段的输出显示包含 StartTime 和 Bank 值的有效负载。 属性列表包括 method-name 列表项中的命令名称：

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> 脱机命令的默认生存时间为 24 小时，超过此时间后，消息就会到期。

## <a name="next-steps"></a>后续步骤

至此，你已经学习了如何在 Azure IoT Central 应用程序中使用命令，现在可以参阅[有效负载](concepts-telemetry-properties-commands.md)来详细了解命令参数，以及[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)来查看不同语言的完整代码示例。
