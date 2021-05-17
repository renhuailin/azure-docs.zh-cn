---
title: 在 Android 地图上显示交通数据 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure Maps Android SDK 在地图上显示交通数据。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 36b3666f12b48468467e76f4c281d58d8018478c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098530"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>在地图上显示交通数据 (Android SDK)

可以在地图上显示两种类型的交通数据，分别是：交通流量数据和交通事件数据。 本指南演示如何显示这两种类型的交通数据。 交通事件数据由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。 交通流量数据显示有关道路上交通流量的指标。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 中提供了两种类型的交通数据：

- 事件数据 - 由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。
- 流量数据 - 提供有关道路交通流量的指标。 通常，交通流量流数据用于为道路着色。 这些颜色基于相对于速度限制或其他指标而言，多大的交通会减慢流量。 有四个值可传递到地图的交通 `flow` 选项。

    |交通流量值 | 说明|
    | :-- | :-- |
    | TrafficFlow.NONE | 不在地图上显示交通数据 |
    | TrafficFlow.RELATIVE | 显示相对于道路的自由流量速度的交通数据 |
    | TrafficFlow.RELATIVE_DELAY | 显示比平均预期延迟更慢的区域 |
    | TrafficFlow.ABSOLUTE | 显示道路中所有车辆的绝对速度 |

下面的代码演示如何在地图上显示交通数据。

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

以下屏幕截图显示了上面的代码在地图上进行的实时交通信息渲染。

![显示实时交通信息的地图](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>获取交通事件详细信息

用于在地图上显示交通事件的功能的属性中提供了有关交通流量事件的详细信息。 可使用 Azure Maps 交通事件矢量地图图块服务将交通流量事件添加到地图。 这些矢量磁贴中的数据格式如[此处所述](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)。 以下代码可向地图中添加单击事件，并检索已单击的交通事件功能，并显示带有一些详细信息的 toast 消息。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

以下屏幕截图显示了上面的代码在地图上进行的实时交通信息渲染，以及显示事件详细信息的 toast 消息。

![地图上显示实时交通信息，并且包含一个显示交通事件详细信息的 toast 消息](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>后续步骤

查看以下指南以了解如何将更多数据添加到地图：

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)
