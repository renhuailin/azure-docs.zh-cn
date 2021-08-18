---
title: 部署 Azure IoT Central 设备网桥 | Microsoft Docs
description: 部署 IoT Central 设备网桥以将其他 IoT 云连接到 IoT Central 应用。 其他 IoT 云包括 Sigfox、Particle Device Cloud 和 The Things Network。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 3fd74e0fddedba5f69176c83aea5a5522bc54c2a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758010"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>使用 IoT Central 设备网桥将其他 IoT 云连接到 IoT Central

IoT Central 设备网桥是一个将其他 IoT 云连接到 IoT Central 应用程序的开源解决方案。 其他 IoT 云例如 [Sigfox](https://www.sigfox.com/)、[Particle Device Cloud](https://www.particle.io/) 和 [The Things Network](https://www.thethingsnetwork.org/) 等。 设备网桥的工作原理是：将数据从连接到其他 IoT 云的设备转发到 IoT Central 应用程序。 设备网桥仅将数据转发到 IoT Central，它不会将命令或属性更新从 IoT Central 发送回设备。

设备网桥让你能够将 IoT Central 的强大功能与设备（例如连接到 Sigfox 的低能耗广域网的资产跟踪设备、Particle Device Cloud 中的空气质量监视设备或 The Things Network 中的土壤湿度监视设备）结合。 你可以使用 IoT Central 应用程序功能（例如数据的规则和分析）、在 Power Automate 和 Azure 逻辑应用中创建工作流，或导出数据。

设备网桥解决方案将多个 Azure 资源预配到你的 Azure 订阅中，并共同转换设备消息和将消息转发到 IoT Central。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南中的步骤，需要：

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="overview"></a>概述

IoT Central 设备网桥是 GitHub 中的一个开源解决方案。 它使用自定义 Azure 资源管理器模板将多个资源（包括 Azure Functions 中的函数应用）部署到 Azure 订阅。

函数应用是设备网桥的核心组成部分。 它通过一个简单的 Webhook 接收来自其他 IoT 平台的 HTTP POST 请求。 [Azure IoT Central 设备网桥](https://github.com/Azure/iotc-device-bridge)存储库包括演示如何连接 Sigfox、Particle 和 The Things Network 云的示例。 如果平台可向函数应用发送 HTTP POST 请求，你可以将此解决方案扩展为连接到自定义 IoT 云。

函数应用将数据转换为 IoT Central 接受的格式，并使用设备预配服务和设备客户端 API 转发数据：

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Azure Functions 的屏幕截图。":::

如果 IoT Central 应用程序识别转发消息中的设备 ID，则设备中的遥测数据会显示在 IoT Central 中。 如果 IoT Central 应用程序无法识别设备 ID，函数应用会尝试使用该设备 ID 注册新设备。 新设备在 IoT Central 应用程序的“设备”页上显示为“未关联的设备” 。 在“设备”页上，你可以将新设备与设备模板关联，然后查看遥测。

## <a name="deploy-the-device-bridge"></a>部署设备网桥

将设备网桥部署到订阅：

1. 在 IoT Central 应用程序中，导航到“管理”>“设备连接”页。

    1. 记下“ID 范围”。 部署设备网桥时，可以使用此值。

    1. 在同一页中，打开“SAS-IoT-Devices”注册组。 在“SAS-IoT-Devices”组页上，复制“主密钥” 。 部署设备网桥时，可以使用此值。

1. 使用下面的“部署到 Azure”按钮，打开将函数应用部署到订阅的自定义资源管理器模板。 使用上一步中的 ID 范围和主密钥 ：

    [![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

部署完成后，需要安装此函数所需的 NPM 包：

1. 在 Azure 门户中，打开部署到订阅的函数应用。 然后，转到“开发工具” > “控制台”。  在控制台中，运行以下命令安装包：

    ```bash
    cd IoTCIntegration
    npm install
    ```

    运行这些命令可能要花费几分钟的时间。 你可以放心地忽略所有警告消息。

1. 包安装完成后，请在函数应用的“概述”页上选择“重启” ：

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="重启的屏幕截图。":::

1. 函数现可供使用。 外部系统可以使用 HTTP POST 请求将设备数据通过设备网桥发送到 IoT Central 应用程序。 若要获取函数 URL，请导航到“函数”>“IoTCIntegration”>“代码 + 测试”>“获取函数 URL”：

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="获取函数 URL 的屏幕截图。":::

发送到设备网桥的消息正文必须采用以下格式：

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

`measurements` 对象中的每个键必须与 IoT Central 应用程序的设备模板中的遥测类型的名称相匹配。 此解决方案不支持在消息正文中指定接口 ID。 因此，如果两个不同的接口具有同名的遥测类型，该度量将出现在 IoT Central 应用程序中的两个遥测流中。

可以在正文中包含 `timestamp` 字段，以指定消息的 UTC 日期和时间。 此字段应为 ISO 8601 格式。 例如，`2020-06-08T20:16:54.602Z`。 如果你没有加入时间戳，系统会使用当前的日期和时间。

你可以在正文中加入 `modelId` 字段。 使用此字段在预配过程中将设备与设备模板关联。 仅 [V3 应用程序](howto-faq.yml#how-do-i-get-information-about-my-application-)支持此功能。

`deviceId` 是小写的字母数字，可包含连字符。

如果不加入 `modelId` 字段或 IoT Central 不能识别模型 ID，则具有不能识别的 `deviceId` 的消息会在 IoT Central 中新建一个未关联的设备。 操作员可以手动将设备迁移到正确的设备模板。 要了解详细信息，请参阅[在 Azure IoT Central 应用程序中管理设备 > 将设备迁移到一个模板](howto-manage-devices-individually.md)。

在 [V2 应用程序](howto-faq.yml#how-do-i-get-information-about-my-application-)中，新设备显示在“设备资源管理器”>“未关联的设备”页上。 选择“关联”，然后选择设备模板以开始从设备接收传入的遥测数据。

> [!NOTE]
> 在设备与模板关联之前，对函数的所有 HTTP 调用都将返回 403 错误状态。

若要通过 Application Insights 开启函数应用的日志记录功能，请在 Azure 门户导航到函数应用中的“监视”>“日志”。 选择“启用 Application Insights”。

## <a name="provisioned-resources"></a>预配的资源

资源管理器模板在 Azure 订阅中预配以下资源：

* 函数应用
* 应用服务计划
* 存储帐户
* 密钥保管库

密钥保管库存储 IoT Central 应用程序的 SAS 组密钥。

函数应用基于[消耗计划](https://azure.microsoft.com/pricing/details/functions/)运行。 尽管此选项不提供专用的计算资源，但它使设备网桥能以每分钟处理数以百计的设备消息，适用于较小的设备群或消息发送频繁较低的设备。 如果应用程序依赖于流式处理大量设备消息，请将消耗计划替换为专用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/windows/)。 此计划提供专用计算资源，缩短了服务器的响应时间。 使用标准应用服务计划时，在此存储库中观察到的 Azure 函数的最大性能大约为每分钟 1,500 条设备消息。 若要了解详细信息，请参阅 [Azure Functions 托管选项](../../azure-functions/functions-scale.md)。

若要使用专用应用服务计划而不是消耗计划，请在部署前编辑自定义模板。 选择“编辑模板”。

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="“编辑模板”屏幕截图。":::

替换以下段：

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

替换为

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

接下来，编辑模板，使 `"properties": {"SiteConfig": {...}}` 下的 `functionapp` 资源的配置中包含 `"alwaysOn": true`。[alwaysOn 配置](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)确保函数应用始终处于运行状态。

## <a name="examples"></a>示例

以下示例概述如何为各种 IoT 云配置设备网桥：

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>示例 1：通过设备网桥连接 Particle 设备

若要通过设备网桥将 Particle 设备连接到 IoT Central，请转到 Particle 控制台，然后创建新 Webhook 集成。 将“请求格式”设置为“JSON” 。  在“高级设置”下，使用以下自定义正文格式：

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": "{{{PARTICLE_EVENT_VALUE}}}"
  }
}
```

粘贴函数应用中的函数 URL，将看到 Particle 设备在 IoT Central 中显示为未关联的设备。 若要了解详细信息，请参阅博客文章[如何将支持 Particle 的项目与 Azure IoT Central 集成](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/)。

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>示例 2：通过设备网桥连接 Sigfox 设备

某些平台可能不允许你指定通过 Webhook 发送的设备消息的格式。 对于这类系统，必须在设备网桥处理消息有效负载之前，将其转换为预期的正文格式。 可以在运行设备网桥的函数中执行该转换。

本部分介绍如何将 Sigfox Webhook 集成的有效负载转换为设备网桥所需的正文格式。 Sigfox 云以十六进制字符串格式传输设备数据。 为方便起见，设备网桥包含一个此格式的转换函数，该函数接受 Sigfox 设备有效负载中可能的字段类型的子集：8、16、32 或 64 位的 `int` 和 `uint`，32 位或 64 位的 `float`，little-endian 和 big-endian。 若要处理来自 Sigfox Webhook 集成的消息，请对函数应用中的 IoTCIntegration/index.js 文件进行以下修改。

若要转换消息有效负载，请在第 21 行的 `handleMessage` 调用之前添加以下代码，并将 `payloadDefinition` 替换为 Sigfox 有效负载定义：

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox 设备需要 `204` 响应代码。 在第 21 行的 `handleMessage` 调用后添加以下代码：

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>示例 3：通过设备网桥从 The Things Network 连接设备

将 The Things Network 设备连接到 IoT Central：

* 将新的 HTTP 集成添加到 The Things Network 中的应用程序：“应用程序”>“集成”>“添加集成”>“HTTP 集成”。
* 确保应用程序包含一个解码器函数，该函数在将设备消息的有效负载发送到函数之前自动将其转换为 JSON：“应用程序”>“有效负载函数”>“解码器”。

下面的示例演示了一个可用于从二进制数据对常见数值类型进行解码的 JavaScript 解码器函数：

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

定义集成后，在函数应用的 IoTCIntegration/index.js 文件第 21 行的 `handleMessage` 调用之前添加以下代码。 此代码将 HTTP 集成的正文转换为所需的格式。

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>限制

设备网桥仅将消息转发到 IoT Central，不将消息发送回设备。 这就是属性和命令不适用于通过此设备网桥连接到 IoT Central 的设备的原因。 由于不支持设备孪生操作，因此不能通过设备网桥更新设备属性。 若要使用这些功能，设备必须使用一个 [Azure IoT 设备 SDK](../../iot-hub/iot-hub-devguide-sdks.md) 直接连接到 IoT Central。

## <a name="next-steps"></a>后续步骤
现在你已了解如何部署 IoT Central 设备网桥后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理设备](howto-manage-devices-individually.md)
