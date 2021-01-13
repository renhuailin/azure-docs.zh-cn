---
title: 如何在 Azure IoT Central 解决方案中使用设备命令
description: 如何在 Azure IoT Central 解决方案中使用设备命令。 本教程介绍如何以设备开发人员的身份使用客户端应用程序中的设备命令来使用 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 52872175eb799785674c331ad4d687ff8ef427a4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134273"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>如何在 Azure IoT Central 解决方案中使用命令

本操作方法指南介绍了如何以设备开发人员的身份使用设备模板中定义的命令。

操作员可以使用 IoT Central UI 调用设备上的命令。 命令控制设备的行为。 例如，操作员可以调用命令来重新启动设备或收集诊断数据。

设备可以：

* 立即响应命令。
* 响应 IoT Central 收到命令，稍后在 *长时间运行的命令* 完成时通知 IoT Central。

默认情况下，如果无法连接到设备，命令会将设备连接到设备，并且会失败。 如果在设备模板 UI 中选择 " **脱机时排队** " 选项，则可以将命令排入队列，直到设备进入联机状态。 本文后面的单独部分中介绍了这些 *脱机命令* 。

## <a name="define-your-commands"></a>定义命令

标准命令将发送到设备，以指示设备执行某些操作。 命令可以包含带有其他信息的参数。 例如，在设备上打开一个阀的命令可能有一个参数，该参数指定了打开该阀的量。 当设备完成命令时，命令也可以接收返回值。 例如，要求设备运行一些诊断的命令可能会收到诊断报告作为返回值。

命令定义为设备模板的一部分。 以下屏幕截图显示了 **恒温器** 设备模板中的 **Get Max-Min 报表** 命令定义。 此命令包含请求和响应参数： 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="显示恒温器设备模板中的 &quot;获取最大最小报表&quot; 命令的屏幕截图":::

下表显示了命令功能的配置设置：

| 字段             |描述|
|-------------------|-----------|
|显示名称       |仪表板和窗体上使用的命令值。|
| 名称            | 命令的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。 设备代码使用此 **名称** 值。|
| 功能类型 | 命令。|
| 脱机时排队 | 是否使此命令成为 *脱机* 命令。 |
| 描述     | 命令功能的说明。|
| 评论     | 有关命令功能的任何注释。|
| 请求     | Device 命令的负载。|
| 响应     | 设备命令响应的负载。|

以下代码片段显示设备模型中的命令的 JSON 表示形式。 在此示例中，响应值是具有多个字段的复杂 **对象** 类型：

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
> 你可以从 "设备模板" 页导出设备型号。

可以使用以下字段将此命令定义与 UI 的屏幕截图关联：

* `@type` 指定功能类型： `Command`
* `name` 用于命令值。

可选字段（如显示名称和说明）使你可以向界面和功能添加更多详细信息。

## <a name="standard-commands"></a>标准命令

本部分说明设备在收到命令后立即发送响应值的方式。

以下代码段演示了设备如何响应立即发送成功代码的命令：

> [!NOTE]
> 为了简单起见，本文使用 Node.js。 有关其他语言示例，请参阅 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md) 教程。

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

用于 `onDeviceMethod` 设置方法的调用 `commandHandler` 。 此命令处理程序：

1. 检查命令的名称。
1. 对于 `getMaxMinReport` 命令，它将调用 `getMaxMinReportObject` 以检索要包含在返回对象中的值。
1. 调用将 `sendCommandResponse` 响应发送回 IoT Central。 此响应包括 `200` 指示成功的响应代码。

以下屏幕截图显示了成功的命令响应在 IoT Central UI 中的显示方式：

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="显示如何查看标准命令的命令有效负载的屏幕截图":::

## <a name="long-running-commands"></a>长时间运行的命令

本部分说明设备如何延迟发送命令成功的确认。

以下代码片段演示了设备如何实现长时间运行的命令：

> [!NOTE]
> 为了简单起见，本文使用 Node.js。 有关其他语言示例，请参阅 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md) 教程。

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

用于 `onDeviceMethod` 设置方法的调用 `commandHandler` 。 此命令处理程序：

1. 检查命令的名称。
1. 调用将 `sendCommandResponse` 响应发送回 IoT Central。 此响应包括 `202` 指示挂起结果的响应代码。
1. 完成长时间运行的操作。
1. 使用与命令同名的报告属性来告知 IoT Central 命令已完成。

以下屏幕截图显示了命令响应在收到202响应代码时在 IoT Central UI 中的显示方式：

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="显示来自设备的即时响应的屏幕截图":::

以下屏幕截图显示了在收到指示命令完成的属性更新时的 IoT Central UI：

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="显示长时间运行命令完成的屏幕截图":::

## <a name="offline-commands"></a>脱机命令

本部分说明设备如何处理脱机命令。 如果设备处于联机状态，则它可以在收到脱机命令后立即进行处理。 如果设备处于脱机状态，它将在下一次连接到 IoT Central 时处理脱机命令。 设备无法发送返回值以响应脱机命令。

> [!NOTE]
> 为了简单起见，本文使用 Node.js。

以下屏幕截图显示了名为 **GenerateDiagnostics** 的脱机命令。 Request 参数是一个对象，它具有名为 **StartTime** 的 datetime 属性和一个名为 **Bank** 的整数枚举属性：

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="显示脱机命令的 UI 的屏幕截图":::

下面的代码段演示了客户端如何侦听脱机命令并显示消息内容：

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

前面代码段的输出显示具有 **StartTime** 和 **Bank** 值的有效负载。 属性列表包括 **方法名称** 列表项中的命令名：

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中使用命令，请参阅 [有效负载](concepts-telemetry-properties-commands.md) ，了解有关命令参数的详细信息，以及 [创建客户端应用程序并将其连接到 Azure IoT Central 应用](tutorial-connect-device.md) 程序，以查看不同语言的完整代码示例。
