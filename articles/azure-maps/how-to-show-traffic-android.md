---
title: 在 Android 地图上显示交通数据 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure Maps Android SDK 在地图上显示交通数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 34d5db3b9c7a1f5829850d999cf94fc1a13c167a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437735"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>在地图上显示交通数据 (Android SDK)

可以在地图上显示两种类型的交通数据，分别是：交通流量数据和交通事件数据。 本指南演示如何显示这两种类型的交通数据。 交通事件数据由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。 交通流量数据显示有关道路上交通流量的指标。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 中提供了两种类型的交通数据：

- 事件数据 - 由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。
- 流量数据 - 提供有关道路交通流量的指标。 通常，交通流量流数据用于为道路着色。 这些颜色基于相对于速度限制或其他指标而言，多大的交通会减慢流量。 有四个值可传递到地图的交通 `flow` 选项。

    |流量枚举 | 说明|
    | :-- | :-- |
    | `TrafficFlow.NONE` | 不在地图上显示交通数据 |
    | `TrafficFlow.RELATIVE` | 显示相对于道路的自由流量速度的交通数据 |
    | `TrafficFlow.RELATIVE_DELAY` | 显示比平均预期延迟更慢的区域 |
    | `TrafficFlow.ABSOLUTE` | 显示道路中所有车辆的绝对速度 |

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

## <a name="filter-traffic-incidents"></a>筛选交通事件

在大多数主要城市，通常每天会有非常多的交通事件，但根据你的场景，可能需要进行筛选，以显示一部分这些事件。 在设置流量选项时，`incidentCategoryFilter` 和 `incidentMagnitudeFilter` 选项会接受一批事件类别、量级枚举器或字符串值。

下表显示了可在 `incidentCategoryFilter` 选项中使用的所有交通事件类别。

| 类别枚举 | 字符串值 | 说明 |
|--------------------|--------------|-------------|
| `IncidentCategory.UNKNOWN` | `"unknown"` | 不符合任何已定义类别或尚未分类的事件。 |
| `IncidentCategory.ACCIDENT` | `"accident"` | 交通事故。 |
| `IncidentCategory.FOG` | `"fog"` | 雾天，会降低能见度，很可能会减少交通流量，并增加事故风险。 |
| `IncidentCategory.DANGEROUS_CONDITIONS` | `"dangerousConditions"` | 道路中的危险情况，例如道路上有物体。 |
| `IncidentCategory.RAIN` | `"rain"` | 大雨，可能会降低能见度，使驾驶条件变得恶劣，并可能增加事故风险。 |
| `IncidentCategory.ICE` | `"ice"` | 结冰路况，可能导致驾驶困难或危险。 |
| `IncidentCategory.JAM` | `"jam"` | 交通阻塞，会导致车辆行驶速度减慢。 |
| `IncidentCategory.LANE_CLOSED` | `"laneClosed"` | 行车道关闭。 |
| `IncidentCategory.ROAD_CLOSED` | `"roadClosed"` | 道路关闭。 |
| `IncidentCategory.ROAD_WORKS` | `"roadWorks"` | 此区域中有道路工程/施工。 |
| `IncidentCategory.WIND` | `"wind"` | 大风，可能会使侧面轮廓较大或重心较高的车辆难以行驶。 |
| `IncidentCategory.FLOODING` | `"flooding"` | 道路淹没。 |
| `IncidentCategory.DETOUR` | `"detour"` | 正在指挥交通绕行。 |
| `IncidentCategory.CLUSTER` | `"cluster"` | 一组不同类别的交通事故。 放大地图会使集中的事件分解为各个事件。 |
| `IncidentCategory.BROKEN_DOWN_VEHICLE` | `"brokenDownVehicle"` | 道路上或路边发生车辆故障。 |

下表显示了可在 `incidentMagnitudeFilter` 选项中使用的所有交通事件量级。

| 量级枚举 | 字符串值 | 说明 |
|--------------------|--------------|-------------|
| `IncidentMagnitude.UNKNOWN` | `"unknown"` | 尚未对其量级进行分类的事件。 |
| `IncidentMagnitude.MINOR` | `"minor"` | 轻度交通问题，通常只用于提供信息，对交通流量的影响很小。 |
| `IncidentMagnitude.MODERATE` | `"moderate"` | 中度交通问题，对交通流量有一定影响。 |
| `IncidentMagnitude.MAJOR` | `"major"` |  重大交通问题，对交通流量有重大影响。 |

以下代码筛选了交通事件，使地图上只显示中等程度的交通堵塞和具有危险性的事件。

::: zone pivot="programming-language-java-android"

``` java
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(new String[] { IncidentMagnitude.MODERATE }),
    incidentCategoryFilter(new String[] { IncidentCategory.DANGEROUS_CONDITIONS, IncidentCategory.JAM })              
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(*arrayOf(IncidentMagnitude.MODERATE)),
    incidentCategoryFilter(
        *arrayOf(
            IncidentCategory.DANGEROUS_CONDITIONS,
            IncidentCategory.JAM
        )
    )
)
```

::: zone-end

下面的屏幕截图显示了一个地图，其中显示了中等程度的交通堵塞和具有危险性的事件。

![显示中等程度的交通堵塞和具有危险性的事件的地图。](media/how-to-show-traffic-android/android-traffic-incident-filters.jpg)

> [!NOTE]
> 一些交通事件可能分配有多个类别。 如果一个事件的任何类别与传入 `incidentCategoryFilter` 的任何选项相匹配，则会显示该事件。 主要事件类别可能不同于筛选器中指定的类别，因此会显示不同的图标。

## <a name="next-steps"></a>后续步骤

查看以下指南以了解如何将更多数据添加到地图：

> [!div class="nextstepaction"]
> [添加图块层](how-to-add-tile-layer-android-map.md)
