---
title: 使用 Azure IoT Central 解决方案中的位置数据
description: 了解如何使用从连接到 IoT Central 应用程序的设备发送的位置数据。 在地图上绘制位置数据或创建地理围栏规则。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127997"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>使用 Azure IoT Central 解决方案中的位置数据

本文介绍如何在 IoT Central 应用程序中使用位置数据。 连接到 IoT Central 的设备可以将位置数据作为遥测流发送，也可以使用设备属性来报告位置数据。

解决方案生成器可以使用位置数据执行以下操作：

* 在地图上绘制报告的位置。
* 绘制遥测位置历史记录 om 为地图。
* 创建地理围栏规则，以便在设备进入或离开特定区域时通知操作员。

## <a name="add-location-capabilities-to-a-device-template"></a>向设备模板添加位置功能

以下屏幕截图显示了一个设备模板，其中包含使用位置数据的设备属性和遥测类型的示例。 定义使用 **location** 语义类型和 **地理** 位置架构类型：

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="显示设备模板中位置属性定义的屏幕截图":::

作为参考， [数字孪生定义语言 (DTDL ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 这些功能的) 定义如下所示：

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** 架构类型不是 [DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)的组成部分。 IoT Central 当前支持 **geopoint** 架构类型和 **location** 语义类型，以便向后兼容。

## <a name="send-location-data-from-a-device"></a>从设备发送位置数据

当设备为上一部分中所示的 **msds-devicelocation** 属性发送数据时，负载将类似于以下 JSON 代码片段：

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

当设备为上一部分中所示的 **跟踪** 遥测发送数据时，负载将类似于以下 JSON 代码片段：

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>显示设备位置

可以在 IoT Central 应用程序中的多个位置显示位置数据。 例如，在与单个设备或仪表板相关联的视图上。

为设备创建视图时，可以选择在地图上绘制位置，或显示各个值：

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="显示包含位置数据的示例视图的屏幕截图":::

你可以向仪表板添加地图磁贴，以绘制一个或多个设备的位置。 添加地图磁贴以显示位置遥测数据时，可以在某个时间段内绘制位置。 以下屏幕截图显示了在过去30分钟内由模拟设备报告的位置：

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="显示包含位置数据的示例仪表板的屏幕截图":::

## <a name="create-a-geofencing-rule"></a>创建地理围栏规则

你可以使用 "位置遥测" 创建一个地理围栏规则，该规则在设备移入或移出矩形区域时生成警报。 以下屏幕截图显示了一个规则，该规则使用四个条件来定义使用纬度值和经度值的矩形区域。 当设备移动到矩形区域时，规则将生成一封电子邮件：

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="显示地理围栏规则定义的屏幕截图":::

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的属性，请参阅：

* [Payloads](concepts-telemetry-properties-commands.md)
* [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)