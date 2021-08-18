---
title: 使用 Azure IoT Central 解决方案中的位置数据
description: 了解如何使用从连接到 IoT Central 应用程序的设备发出的位置数据。 在地图上绘制位置数据或创建地理围栏规则。
author: dominicbetts
ms.author: dobett
ms.date: 06/25/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 57f1bb47ceaa0552053cb61321dc54a3d2b19293
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112964505"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>使用 Azure IoT Central 解决方案中的位置数据

本文介绍如何在 IoT Central 应用程序中使用位置数据。 连接到 IoT Central 的设备可将位置数据作为遥测流发送，也可使用设备属性来报告位置数据。

位置数据可用于：

* 在地图上绘制报告的位置。
* 在地图上绘制遥测位置历史记录。
* 创建地理围栏规则，以便在设备移入/移出特定区域时通知操作员。

## <a name="add-location-capabilities-to-a-device-template"></a>向设备模板添加位置功能

以下屏幕截图显示了一个设备模板，其中包含使用位置数据的设备属性和遥测类型的示例。 定义使用 location 语义类型和 geolocation 架构类型 ：

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="屏幕截图显示了设备模板中的位置属性定义":::

这些功能的[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 定义如下所示，可供参考：

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
> geopoint 架构类型不属于 [DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 目前支持 geopoint 架构类型和 location 语义类型，以实现后向兼容性 。

## <a name="send-location-data-from-a-device"></a>从设备发送位置数据

当设备发送上一部分中所示的 DeviceLocation 属性数据时，有效负载将与以下 JSON 代码片段类似：

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

当设备发送上一部分中所示的跟踪遥测数据时，有效负载将与以下 JSON 代码片段类似：

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

可在 IoT Central 应用程序中的多个位置显示位置数据。 例如，在与单个设备或仪表板相关联的视图上。

为设备创建视图时，可选择在地图上绘制位置，或显示各个值：

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="屏幕截图显示了包含位置数据的示例视图":::

你可向仪表板添加地图图块，绘制一个或多个设备的位置。 添加地图图块来显示位置遥测时，可绘制在某个时间段的位置。 以下屏幕截图显示了模拟设备在最近 30 分钟内报告的位置：

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="屏幕截图显示了包含位置数据的示例仪表板":::

## <a name="create-a-geofencing-rule"></a>创建地理围栏规则

你可使用位置遥测创建一个在设备移入/移出矩形区域时生成警报的地理围栏规则。 以下屏幕截图显示了一个使用四个条件来定义使用经纬度值的矩形区域的规则。 此规则将在设备移入矩形区域时生成一封电子邮件：

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="屏幕截图显示了地理围栏规则定义":::

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的属性，请参阅：

* [Payloads](concepts-telemetry-properties-commands.md)
* [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)