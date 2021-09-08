---
title: 在地图上显示交通信息 | Microsoft Azure Maps
description: 了解如何将交通数据添加到地图。 了解流量数据，并参阅如何使用 Azure Maps Web SDK 将事件数据和流量数据添加到地图。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9e7f2348c2cbf3ba0ad52f3ab5914235f0cb2e3a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425097"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 中提供了两种类型的交通数据：

- 事件数据 - 由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。
- 流量数据 - 提供有关道路交通流量的指标。 通常，交通流量流数据用于为道路着色。 这些颜色基于相对于速度限制或其他指标而言，多大的交通会减慢流量。 有四个值可传递到地图的交通 `flow` 选项。

    |交通流量值 | 说明|
    | :-- | :-- |
    | `none` | 不在地图上显示交通数据 |
    | `relative` | 显示相对于道路的自由流量速度的交通数据 |
    | `relative-delay` | 显示比平均预期延迟更慢的区域 |
    | `absolute` | 显示道路中所有车辆的绝对速度 |

下面的代码演示如何在地图上显示交通数据。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

## <a name="traffic-overlay-options"></a>交通状况叠加选项

以下工具可让你在不同的交通状况叠加设置之间进行切换，以查看呈现如何变化。 

<br/>

<iframe height="700" scrolling="no" title="交通状况叠加选项" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的触笔<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>交通状况叠加选项</a>。
</iframe>


## <a name="add-traffic-controls"></a>添加交通控件

有两种可添加到地图的不同交通控件。 第一个控件 `TrafficControl` 添加可用于打开和关闭流量的切换按钮。 此控件的选项允许你指定显示流量时要使用的流量设置。 默认情况下，此控件将显示相对的交通流量和事件数据，但是，你可以将其更改为显示绝对交通流量，而不显示事件（如果需要）。 第二个控件 `TrafficLegendControl` 将向地图添加一个交通流量图例，该图例可帮助用户了解突出显示的颜色代码道路的含义。 仅当地图上显示交通流量数据时，此控件才会出现在地图上，而在其他所有时间都将被隐藏。

下面的代码演示了如何将交通控件添加到地图。

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" scrolling="no" title="交通控件" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的触笔<a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>交通控件</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

增强用户体验：

> [!div class="nextstepaction"]
> [地图与鼠标事件交互](map-events.md)

> [!div class="nextstepaction"]
> [生成可访问的地图](map-accessibility.md)

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)