---
title: Microsoft Azure Maps 中 Azure Maps 路线服务的最佳做法
description: 了解如何使用 Microsoft Azure Maps 中的路线服务来规划车辆的路线。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 31a5a7f37ef61103a0e70b3daca076e62a16eaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742633"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure Maps 路线服务的最佳做法

可以使用 Azure Maps [路线服务](/rest/api/maps/route)中的路线方向和路线矩阵 API 计算请求的每条路线的预计抵达时间 (ETA)。 路线 API 考虑多种因素，例如实时交通信息和历史交通数据，例如请求的星期几某一时间的典型通行速度。 这些 API 会根据时间或距离依次或按最佳顺序返回可用于多个目的地的最短或最快路线。 用户还可以请求行人、骑行人员和商用车辆（例如货车）的专门路线与详细信息。 本文将会分享有关调用 Azure Maps [路线服务](/rest/api/maps/route)的最佳做法，其中将会介绍以下操作：

 * 在路线方向 API 与矩阵路线 API 之间进行选择
 * 根据实时和历史交通数据请求历史和预测行程时间
 * 请求整条路线的路线详细信息（例如时间和距离）以及路线的每条支线
 * 请求商用车辆（例如货车）的路线
 * 请求路线上的交通信息，例如拥堵和收费信息
 * 请求包括一个或多个停靠站（中途点）的路线
 * 优化一个或多个停靠站的路线，以获取前往每个停靠站（中途点）的最佳顺序
 * 使用支持点优化备选路线。 例如，提供途经电动车充电站的备选路线。
 * 将[路线服务](/rest/api/maps/route)与 Azure Maps Web SDK 配合使用

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

有关路线服务覆盖范围的详细信息，请参阅[路线覆盖范围](routing-coverage.md)。

本文使用 [Postman 应用](https://www.postman.com/downloads/)来生成 REST 调用，但你可以选择任何 API 开发环境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>在路线方向与矩阵路线之间进行选择

路线方向 API 返回指示，其中包括路线路径的行程时间和坐标。 路线矩阵 API 可用于计算按出发地位置和目的地位置定义的一组路线的行程时间与距离。 对于每个给定的出发地，矩阵 API 将计算从该出发地到每个给定目的地的路线成本（行程时间和距离）。 所有这些 API 都允许指定所需出发时间、抵达时间和车辆类型（例如乘用车或货车）等参数。 这些 API 都相应地使用实时或预测交通数据来返回最佳路线。

如果你的方案要实现以下目的，请考虑调用路线方向 API：

* 请求两个或更多个已知位置之间的最短或最快驾驶路线，以获取载运车辆的精确抵达时间。
* 请求详细的路线指导（包括路线几何图形）以在地图上可视化路线
* 根据客户位置的列表，计算前往每个客户位置并返回出发地位置的最短路线。 这种方案通常称为“旅行商问题”方案。 在一个请求中最多可以传递 150 个中途点（停靠站）。
* 只需使用一个 API 调用将查询批发送到路线方向 Batch API。

如果你的方案要实现以下目的，请考虑调用矩阵路线 API：

* 计算一组出发地与目的地之间的行程时间或距离。 例如，你有 12 名驾驶员，需要查找离饭店最近的可运送食物的有空驾驶员。
* 按照驾驶员的实际行程距离或时间将潜在路线排序。 矩阵 API 只返回每个出发地与目的地组合的行程时间和距离。
* 基于行程时间或距离聚类数据。 例如，你的公司拥有 50 名员工，你需要查找其居住地距离办公室的驾车时间不超过 20 分钟的所有员工。

下面是路线方向和矩阵 API 的部分功能的比较：

| Azure Maps API | 请求中的最大查询数 | 规避区域 | 货车和电动车路线 | 中途点和旅行商优化 | 支持点 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 获取路线方向 | 1 | | ✔ | ✔ | |
| 发布路线方向 | 1 | ✔ | ✔ | ✔ | ✔ |
| 发布路线目录批 | 700 | | ✔ | ✔ | |
| 发布路线矩阵 | 700 | | ✔ | | |

若要详细了解电动车路线功能，请参阅有关如何[在 Python 中使用 Azure Notebooks 规划电动车路线](tutorial-ev-routing.md)的教程。

## <a name="request-historic-and-real-time-data"></a>请求历史数据和实时数据

默认情况下，路线服务假设旅行模式为乘用车，出发时间为现在。 除非路线计算请求指定了其他设置，否则它会根据实时交通状况返回路线。 路线引擎将捕获并考虑固定的时间相关交通限制，例如“在下午 4:00 至 6:00 不允许左转”。 除非你专门请求了一条忽略当前实时交通的路线，否则会考虑封路的情况（例如道路施工）。 若要忽略当前交通，请在 API 请求中将 `traffic` 设置为 `false`。

路线计算 travelTimeInSeconds 值包括交通状况导致的延迟。 当出发时间设置为现在时，将利用当前和历史行程时间数据生成此值。 如果出发时间设置为将来的时间，则 API 将根据历史数据返回预测的行程时间。

如果在请求中包含 computeTravelTimeFor=all 参数，则响应中的 summary 元素将包含以下附加字段，其中包括历史交通状况：

| 元素 | 说明|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 估计行程时间，此值是假设交通状况（例如拥塞）不会导致行程出现任何延迟的条件下计算得出的 |
| historicTrafficTravelTimeInSeconds | 使用时间相关的历史交通数据计算得出的估计行程时间 |
| liveTrafficIncidentsTravelTimeInSeconds | 使用实时速度数据计算得出的估计行程时间 |

下一部分将演示如何使用所述的参数对路线 API 发出调用。

### <a name="sample-query"></a>示例查询

在下面的第一个示例中，出发时间设置为编写本文时的将来时间。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

响应包含如下所示的 summary 元素。 由于出发时间设置为将来时间，因此 trafficDelayInSeconds 值为零。 travelTimeInSeconds 值是使用时间相关的历史交通数据计算得出的。 因此，在本例中，travelTimeInSeconds 值等于 historicTrafficTravelTimeInSeconds 值 。

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>示例查询

在下面的第二个示例中使用了实时路线请求，其出发时间设置为现在。 由于此出发时间采用默认值，因此未在 URL 中显式指定。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

响应包含如下所示的摘要。 由于拥塞，trafficDelaysInSeconds 值大于零。 它也大于 historicTrafficTravelTimeInSeconds。

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>请求路线和支线详细信息

路线服务默认返回坐标数组。 响应将在名为 `points` 的列表中包含构成路径的坐标。 路线响应还包含从路线起点开始的距离，以及估计的已用时间。 这些值可用于计算整个路线的平均速度。

下图显示了 `points` 元素。

![Points 元素](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

展开 `point` 元素可以查看路径的坐标列表：

![已展开 points 元素](media/how-to-use-best-practices-for-routing/points-list-img.png)

路线方向 API 支持不同格式的指示，可以通过指定 instructionsType 参数来使用这些指示。 若要设置指示的格式以方便进行计算机处理，请使用 instructionsType=coded。 使用 instructionsType=tagged 将以文本格式向用户显示指示。 此外，还可以将指示格式设置为文本，在其中将会标记指示的某些元素，并以特殊格式显示指示。 有关详细信息，请参阅[支持的指示类型列表](/rest/api/maps/route/postroutedirections#routeinstructionstype)。

请求指示后，响应将返回名为 `guidance` 的新元素。 `guidance` 元素包含两个信息片段：转弯方向和汇总指示。

![指示类型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions` 元素包含行程的转弯方向，`instructionGroups` 包含汇总指示。 每段指示摘要包括可能涵盖多条道路的行程段。 API 可以返回各个路段的详细信息。 例如，交通拥堵路段的坐标范围，或当前交通速度。

![转弯指示](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![汇总指示](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>请求商用车辆的路线

Azure Maps 路线 API 支持规划商用车辆的路线，包括商用货车的路线。 API 将考虑指定的限制。 例如，车辆的高度和重量，以及车辆是否运送危险性货物。 例如，如果车辆运送易燃货车，则路线引擎将会规避居民区附近的某些隧道。

### <a name="sample-query"></a>示例查询

以下示例请求将查询商用货车的路线。 该货车运送第 1 类危险废料。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路线 API 将返回与货车尺寸和危险废物相适应的路线。 可以展开 `guidance` 元素阅读路线指示。

![运送第 1 类危险废料的货车](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>示例查询

更改上述查询中的美国危险废料分类将生成与此项更改相适应的另一条路线。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

以下响应适用于运送第 9 类危险物料（危险程度低于第 1 类危险物料）的货车。 展开 `guidance` 元素阅读路线时，即会发现路线与前面不同。 针对运送第 1 类危险物料的货车显示的路线指示更多。



![运送第 9 类危险废料的货车](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>请求路线上的交通信息

借助 Azure Maps 路线方向 API，开发人员可以通过在请求中包含 `sectionType` 参数来请求每个路段类型的详细信息。 例如，可以请求每个交通拥堵路段的车速信息。 请参阅 [sectionType 键值列表](/rest/api/maps/route/getroutedirections#sectiontype)来了解可以请求的各种详细信息。

### <a name="sample-query"></a>示例查询

以下查询将 `sectionType` 设置为 `traffic`。 此查询将请求从西雅图到圣地亚哥的路段及其交通信息。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

响应将在给定的坐标中标出适合行车的路段。

![交通路段](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

呈现地图时，此选项可用于为路段标上颜色，如下图所示： 

![在地图上以不同的颜色呈现路段](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>计算和优化包含多个停靠站的路线

Azure Maps 目前提供两种形式的路线优化：

* 在不更改中途点顺序的情况下，基于请求的路线类型进行优化。 可以[在此处找到支持的路线类型](/rest/api/maps/route/postroutedirections#routetype)

* 旅行商优化。此优化方式会更改中途点的顺序以获取前往每个停靠站的最佳顺序

对于多停靠站路线，在单个路线请求中最多可以指定 150 个中途点。 与在往返行程中一样，起始和结束坐标位置可以相同。 但是，需要至少提供一个额外的中途点才能计算路线。 中途点可以添加到查询中的出发地与目的地坐标之间。

如果你要优化前往给定中途点的最佳顺序，则需要指定 computeBestOrder=true。 此方案也称为旅行商问题优化。

### <a name="sample-query"></a>示例查询

以下查询在将 `computeBestOrder` 参数设置为 `false` 的情况下请求六个中途点的路径。 这也是 `computeBestOrder` 参数的默认值。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应将路径长度描述为 140,851 米，通过该路径需要 9,991 秒。

![未优化的路线响应](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

下图演示了此查询生成的路径。 此路径是一条可能的路线。 它不是基于时间或距离的最佳路径。

![未优化的路线插图](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



此路线的中途点顺序为：0、1、2、3、4、5 和 6。

### <a name="sample-query"></a>示例查询

以下查询请求前面示例中相同的六个中途点的路径。 这一次，`computeBestOrder` 参数设置为 `true`（旅行商优化）。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应将路径长度描述为 91,814 米，通过该路径需要 7,797 秒。 此处的行程距离和行程时间之所以都已减少，是因为 API 返回了优化的路线。

![优化的路线响应](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

下图演示了此查询生成的路径。

![优化的路线插图](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

最佳路线采用以下中途点顺序：0、5、1、2、4、3 和 6。

>[!TIP]
> 路线服务的优化中途点顺序信息提供一组索引。 这些索引不包括出发地与目的地索引。 需要将这些值加 1 以考虑出发地。 然后，在末尾添加目的地以获取完整的中途点排序列表。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支持点计算和偏离备选路线

在某些情况下，你可能想要重建路线，以计算参考路线的零个或多个备选路线。 例如，你可能想要向客户显示途经你的零售店的备选路线。 在这种情况下，需要使用支持点来偏离某个位置。 下面是偏离位置的步骤：

1. 按原有方式计算路线并获取路线响应中的路径
2. 使用路线路径找出该路线路径上或者其附近的所需位置。 例如，可以使用 Azure Maps [兴趣点 API](/rest/api/maps/search/getsearchpoi)，或者在数据库中查询你自己的数据。  
3. 根据与路线起点之间的距离将位置排序
4. 将这些位置作为支持点添加到对[发布路线方向 API](/rest/api/maps/route/postroutedirections) 发出的新路线请求中。 若要详细了解支持点，请参阅[发布路线方向 API 文档](/rest/api/maps/route/postroutedirections#supportingpoints)。 

调用[发布路线方向 API](/rest/api/maps/route/postroutedirections) 时，可以连同支持点一起设置最小偏差时间或距离约束。 如果你想要提供备选路线，同时又想要限制行程时间，请使用这些参数。 使用这些约束时，备选路线的给定行程时间或距离将遵循从出发点开始的参考路线。 换言之，其他路线将根据给定的约束偏离参考路线。

下图是一个示例，其中使用指定的时间和距离偏差限制呈现了备选路线。

![备选路线](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>在 Web 应用中使用路线服务

Azure Maps Web SDK 提供一个[服务模块](/javascript/api/azure-maps-rest/)。 此模块是一个帮助程序库，可让用户通过 JavaScript 或 TypeScript 轻松地在 Web 或 Node.js 应用程序中使用 Azure Maps REST API。 服务模块可用于在地图上呈现返程路线。 该模块会自动确定要使用哪个 API 来处理 GET 和 POST 请求。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

> [!div class="nextstepaction"]
> [Azure Maps 路线服务](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服务模块](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [在地图上显示路线](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM 包](https://www.npmjs.com/package/azure-maps-rest  )
