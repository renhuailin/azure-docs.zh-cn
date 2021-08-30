---
title: 使用 Microsoft Azure Maps 出行服务（预览版）请求实时公共交通数据
description: 了解如何请求实时公共交通数据，例如某个公交车站的车辆到站情况。 了解如何使用 Azure Maps 出行服务（预览版）来执行此操作。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6e7358e76cdcf07f39a9b5a5fcab7ab0151ebc32
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742621"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>使用 Azure Maps 出行服务（预览版）请求实时公共交通数据 

> [!IMPORTANT]
> Azure Maps 出行服务（预览版）已停用，在 2021 年 10 月 5 日之后将不再可用，也不再受支持。 所有其他 Azure Maps API 和服务不受此停用公告的影响。
> 有关详细信息，请参阅 [Azure Maps 出行服务（预览版）停用](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)。


本文介绍如何使用 Azure Maps [出行服务](/rest/api/maps/mobility)来请求实时公共交通数据。

在本文中，你将学习如何请求到达某个指定车站的所有车次的下一批实时到站情况

## <a name="prerequisites"></a>先决条件

首先需要有 Azure Maps 帐户和订阅密钥才能对 Azure Maps 公共交通 API 进行调用。 如需相关信息，请按照[创建帐户](quick-demo-map-app.md#create-an-azure-maps-account)中的说明操作，以创建 Azure Maps 帐户。 请按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤操作，以获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

本文使用 [Postman 应用](https://www.getpostman.com/apps)来构建 REST 调用。 可以使用你喜欢的任何 API 开发环境。

## <a name="request-real-time-arrivals-for-a-stop"></a>请求某个车站的实时到站情况

为了请求某个特定公交车站的实时到站数据，需要向 Azure Maps [出行服务（预览版）](/rest/api/maps/mobility)的[实时到站 API](/rest/api/maps/mobility/getrealtimearrivalspreview) 发出请求。 你需要 metroID 和 stopID 来完成该请求 。 若要详细了解如何请求这些参数，请参阅有关如何[请求公交线路](./how-to-request-transit-data.md)的指南。

我们使用“522”作为 metroID，这是“华盛顿州西雅图–塔科马–贝尔维尤”地区的 metroID。 使用“522---2060603”作为 stopID，此车站位于“Ne 24th St & 162nd Ave Ne, Bellevue WA”。 若要针对这一站所有接下来的实时到站来请求接下来的五批实时到站数据，请完成以下步骤：

1. 打开 Postman 应用。 选择“新建”以创建请求。 在“新建”窗口中，选择“HTTP 请求” 。 在“请求名称”中，输入请求名称。

2. 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 来创建 GET 请求。 将 `{subscription-key}` 替换为 Azure Maps 主密钥。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

3. 在请求成功之后，会收到以下响应。  请注意，参数“scheduleType”定义预计的到站时间是基于实时数据还是静态数据。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>后续步骤

了解如何使用出行服务（预览版）来请求交通数据：

> [!div class="nextstepaction"]
> [如何请求交通数据](how-to-request-transit-data.md)

浏览 Azure Maps 出行服务（预览版）API 文档：

> [!div class="nextstepaction"]
> [出行服务 API 文档](/rest/api/maps/mobility)