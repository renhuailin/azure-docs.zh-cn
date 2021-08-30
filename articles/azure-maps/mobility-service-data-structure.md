---
title: Microsoft Azure Maps 中的出行服务（预览版）数据结构
description: 了解如何在 Azure Maps 出行服务（预览版）中按都会区组织数据。 了解哪些字段存储有关公交停靠站和线路的信息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a64049ab544b2ec790d3625dad9137ae539f3020
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733406"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Azure Maps 出行服务（预览版）中的数据结构 

> [!IMPORTANT]
> Azure Maps 出行服务（预览版）已停用，2021 年 10 月 5 日之后将不再可用，也不再获得支持。 所有其他 Azure Maps API 和服务不受此停用公告的影响。
> 有关详细信息，请参阅 [Azure Maps 出行服务（预览版）停用文档](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)。

本文介绍 [Azure Maps 出行服务](/rest/api/maps/mobility)中的都会区概念。 其中将会说明在此服务中查询公交停靠站和线路时返回的一些常见字段。 建议在使用出行服务 API 进行开发之前阅读本文。

## <a name="metro-area"></a>都会区

出行服务（预览版）数据是按支持的都会区分组的。 都会区不遵循城市边界。 一个都会区可以包含多个城市（人口密集城市和周边城市）。 事实上，一个国家/地区也可算作是一个都会区。 

`metroID` 是都会区的 ID，可用于调用[获取都会区信息 API](/rest/api/maps/mobility/getmetroareainfopreview)。 使用 Azure Maps“获取都会区”API 可以请求交通类型、交通营运公司、现行告示，以及所选都会区的其他详细信息。 还可以请求支持的都会区和 metroID。 都会区 ID 随时可能更改。

metroID：522，名称：Seattle-Tacoma-Bellevue 

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停靠站 ID

交通停靠站可按两种类型的 ID 引用：[常规交通源规范 (GFTS)](http://gtfs.org/) ID 和 Azure Maps 停靠站 ID。 GFTS ID 称为 stopKey，Azure Maps 停靠站 ID 称为 stopID。 如果经常需要引用交通停靠站，建议使用 Azure Maps 停靠站 ID。 stopID 更稳定，只要实际的停靠站存在，此 ID 就可能一直保持不变。 GTFS 停靠站 ID 更频繁地更新。 例如，GTFS 停靠站 ID 可应 GTFS 提供商的请求更新，或者在发布新的 GTFS 版本后更新。 尽管实际的停靠站未变化，但 GTFS 停靠站 ID 却可能会变化。

若要开始，可以使用[获取附近交通 API](/rest/api/maps/mobility/getnearbytransitpreview) 请求附近的交通停靠站。

## <a name="line-groups-and-lines"></a>线路组和线路

出行服务（预览版）对线路和线路组使用并行数据模型。 此模型用于更好地处理从 [GTFS](http://gtfs.org/) 路线和行程数据继承的更改。


### <a name="line-groups"></a>线路组

线路组是一个实体，它将逻辑上属于同一个组的所有线路分组到一起。 通常，一个线路组包含两条线路，其中一条线路从点 A 前往点 B，另一条线路从点 B 回到点 A。这两条线路属于同一家公交营运公司，具有相同的线路编号。 但在某些情况下，一个线路组包含两条以上的线路，或者只包含一条线路。


### <a name="lines"></a>线条

如前所述，每个线路组由一组线路构成。 每个线路组由两条线路构成，其中每条线路描述一个方向。  但在某些情况下，一个线条组由更多条线路构成。 例如，某条线路有时会绕道经过特定的邻近区域，而有时则不会。 在这两种情况下，这条线路都是以同一个线路编号运营的。 另外，一个线路组可能仅由一条线路构成。 单向环形线路就是包含一条线路的线路组。

若要开始，可以使用[获取交通线路 API](/rest/api/maps/mobility/gettransitlineinfopreview) 请求线路组。


## <a name="next-steps"></a>后续步骤

了解如何使用出行服务（预览版）来请求交通数据：

> [!div class="nextstepaction"]
> [如何请求交通数据](how-to-request-transit-data.md)

了解如何使用出行服务（预览版）请求实时数据：

> [!div class="nextstepaction"]
> [如何请求实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 出行服务（预览版）API 文档

> [!div class="nextstepaction"]
> [出行服务 API 文档](/rest/api/maps/mobility)