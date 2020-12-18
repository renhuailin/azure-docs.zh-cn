---
title: 显示 Android 地图上的流量数据 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure map Android SDK 来显示地图上的流量数据。
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680458"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Android SDK (显示地图上的流量数据) 

流数据和事件数据是可以在地图上显示的两种类型的流量数据。 本指南演示如何显示这两种类型的流量数据。 事件数据包含基于点的数据和基于行的数据，如构造、闭包和意外等。 流数据显示有关路上流量的指标。

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。 本文中的代码块可以插入到 maps `onReady` 事件处理程序中。

## <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 中提供了两种类型的交通数据：

- 事件数据 - 由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。
- 流量数据 - 提供有关道路交通流量的指标。 通常，交通流量流数据用于为道路着色。 这些颜色基于相对于速度限制或其他指标而言，多大的交通会减慢流量。 有四个可传递到映射的流量选项的值 `flow` 。

    |流值 | 说明|
    | :-- | :-- |
    | TrafficFlow | 不显示地图上的流量数据 |
    | TrafficFlow | 显示相对于旅途的自由流动速度的流量数据 |
    | TrafficFlow.RELATIVE_DELAY | 显示比平均预计延迟慢的区域 |
    | TrafficFlow | 显示路上的所有车辆的绝对速度 |

下面的代码演示如何在地图上显示交通数据。

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

以下屏幕截图显示了上面的代码呈现地图上的实时流量信息。

![显示实时流量信息的地图](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>获取流量事件详细信息

有关流量事件的详细信息可在用于在地图上显示事件的功能的属性中找到。 使用 Azure Maps 流量事件向量磁贴服务将流量事件添加到映射。 这些矢量磁贴中的数据格式（如 [此处所述](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)）。 以下代码将单击事件添加到地图中，并检索已单击的流量事件功能，并显示带有一些详细信息的 toast 消息。

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

以下屏幕截图显示了上面的代码呈现地图上的实时流量信息，以及显示事件详细信息的 toast 消息。

![显示实时流量信息以及显示事件详细信息的 toast 消息的地图](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>后续步骤

查看以下指南以了解如何将更多数据添加到地图：

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)
