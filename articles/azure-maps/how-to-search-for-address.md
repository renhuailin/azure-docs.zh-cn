---
title: 使用 Azure Maps 搜索服务来搜索位置
description: 了解 Azure Maps 搜索服务。 了解如何使用此组 API 执行地理编码、反向地理编码、模糊搜索和反向十字街搜索。
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: de8acfbf4d4930f5c029aaa71300af770a274194
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751456"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>使用 Azure Maps 搜索服务来搜索位置

[Azure Maps 搜索服务](/rest/api/maps/search)是一组 REST API，旨在帮助开发人员按名称、类别和其他地理信息搜索地址、位置和业务列表。 除了支持传统的地理编码以外，该服务还可以根据纬度和经度对地址和十字街执行反向地理编码。 在诸如[路线](/rest/api/maps/route)和[天气](/rest/api/maps/weather)服务之类的其他 Azure Maps 服务中，可将搜索返回的纬度和经度值用作参数。


本文将指导如何进行以下操作：

* 使用[搜索地址 API](/rest/api/maps/search/getsearchaddress) 请求地址（地理编码地址位置）的纬度和经度坐标。
* 使用[模糊搜索 API](/rest/api/maps/search/getsearchfuzzy) 搜索地址或兴趣点 (POI)。
* 执行[反向地址搜索](/rest/api/maps/search/getsearchaddressreverse)，将坐标位置转换为街道地址。
* 使用[搜索地址反向十字街 API](/rest/api/maps/search/getsearchaddressreversecrossstreet) 将坐标位置转换为人类可理解的十字街。  大多数情况下，从设备或资产接收 GPS 源并且要了解坐标所在位置的跟踪应用程序需要此 API。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>请求地址的纬度和经度（地理编码）

在此示例中，我们将使用 Azure Maps [获取搜索地址 API](/rest/api/maps/search/getsearchaddress)，将地址转换为纬度和经度坐标。 此过程也称为“地理编码”。 除了返回坐标外，响应还会返回详细的地址属性，例如街道、邮政编码、市政当局和国家/地区信息。

>[!TIP]
>如果你有一组需执行地理编码的地址，则可以使用[发布搜索地址批处理 API](/rest/api/maps/search/postsearchaddressbatch)，以通过单个 API 调用发送一批查询。

1. 在 Postman 应用中，选择“新建”以创建请求。 在“新建”窗口中，选择“HTTP 请求” 。 在“请求名称”中，输入请求名称。

2. 在生成器选项卡中选择“GET”HTTP 方法，然后输入以下 URL。 在此请求中，我们要搜索特定地址：`400 Braod St, Seattle, WA 98109`。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

3. 单击蓝色“发送”按钮。 响应正文将包含单个位置的数据。

4. 现在，我们将搜索具有多个可能位置的地址。 在“参数”部分中，将 `query` 键更改为 `400 Broad, Seattle`。 单击蓝色“发送”按钮。

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="搜索地址":::

5. 接下来，尝试将 `query` 键设置为 `400 Broa`。

6. 单击“发送”按钮  。 现在可以看到响应包含来自多个国家/地区的响应。 若要让结果在地理上偏近用户的相关区域，始终要在请求中添加尽可能多的位置详细信息。

## <a name="using-fuzzy-search-api"></a>使用模糊搜索 API

Azure Maps [模糊搜索 API](/rest/api/maps/search/getsearchfuzzy) 支持标准单行搜索和自由格式搜索。 如果不知道搜索请求的用户输入类型，我们建议使用 Azure Maps 模糊搜索 API。  此查询输入可以是完整或部分地址。 它还可以是兴趣点 (POI) 标记，如 POI 名称、POI 类别或品牌名称。 此外，若要改善搜索结果的相关性，可以通过坐标位置和半径或通过定义边界框来限制查询结果。

>[!TIP]
>大多数搜索查询默认指定 maxFuzzyLevel=1，以提高性能并减少不正常的结果。 可以使用 `maxFuzzyLevel` 或 `minFuzzyLevel` 参数调整模糊度。 有关 `maxFuzzyLevel` 的详细信息以及所有可选参数的完整列表，请参阅[模糊搜索 URI 参数](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>使用模糊搜索搜索地址

在此示例中，我们将使用模糊搜索来搜索全世界的 `pizza`。  然后将介绍如何在特定国家/地区范围内进行搜索。 最后，我们将说明如何使用坐标位置和半径来将搜索限定在特定区域内，并限制返回的结果数。

>[!IMPORTANT]
>若要让结果在地理上偏近用户的相关区域，始终要添加尽可能多的位置详细信息。 若要了解详细信息，请参阅[搜索最佳实践](how-to-use-best-practices-for-search.md#geobiased-search-results)。

1. 在 Postman 应用中，选择“新建”以创建请求。 在“新建”窗口中，选择“HTTP 请求” 。 在“请求名称”中，输入请求名称。

2. 在生成器选项卡中选择“GET”HTTP 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL 路径中的 _json_ 属性确定响应格式。 为便于使用和阅读，本文使用 json。 若要查找其他受支持的响应格式，请参阅 [URI 参数参考文档](/rest/api/maps/search/getsearchfuzzy#uri-parameters)中的 `format` 参数定义。

3. 单击“发送”并查看响应正文。

    “pizza”的模糊查询字符串返回了“pizza”和“restaurant”类别中的 10 个[兴趣点结果](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI)。 每个结果都包含相应位置的街道地址、纬度和经度值、视区和入口点。 现在此查询的结果各不相同，且与任何参照位置都不相关。
  
    在下一步中，我们将使用 `countrySet` 参数，以便仅指定应用程序需要覆盖的国家/地区。 有关支持的国家/地区的完整列表，请参阅[搜索范围](./geocoding-coverage.md)。

4. 默认行为是在全球搜索，这样可能会返回不需要的结果。 接下来，我们将仅在美国范围内搜索 pizza。 将 `countrySet` 键添加到“参数”部分，并将其值设置为 `US`。 将 `countrySet` 键设置为 `US` 会将结果限定在美国范围内。

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="在美国范围内搜索 pizza":::

    结果现在受限为国家/地区代码，查询返回了美国境内的比萨餐馆。

5. 若要执行更具针对性的搜索，可以在纬度/经度坐标对的范围内 进行搜索。 在此示例中，我们将使用 西雅图太空针塔的纬度/经度。 由于我们只想返回 400 米半径范围内的结果，因此我们将添加 `radius` 参数。 此外，我们将添加 `limit` 参数，将结果限制为最近的五个比萨店位置。

    在“参数”部分中，添加以下键/值对：

     | 密钥 | 值 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. 单击“发送”。 响应包含西雅图太空针塔附近的比萨店。

## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜索搜索街道地址

Azure Maps [获取搜索地址反向 API](/rest/api/maps/search/getsearchaddressreverse) 将坐标转换为人类可理解的街道地址。 此 API 通常用于使用 GPS 源并且要发现位于特定坐标点的地址的应用程序。

>[!IMPORTANT]
>若要让结果在地理上偏近用户的相关区域，始终要添加尽可能多的位置详细信息。 若要了解详细信息，请参阅[搜索最佳实践](how-to-use-best-practices-for-search.md#geobiased-search-results)。

>[!TIP]
>如果你有一组需要执行反向地理编码的坐标位置，则可以使用 [发布搜索地址反向批处理 API](/rest/api/maps/search/postsearchaddressreversebatch)，以通过单个 API 调用发送一批查询。

在此示例中，我们将使用几个可用的可选参数执行反向搜索。 有关可选参数的完整列表，请参阅[反向搜索参数](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。

1. 在 Postman 应用中，选择“新建”以创建请求。 在“新建”窗口中，选择“HTTP 请求” 。 在“请求名称”中，输入请求名称。

2. 在生成器选项卡中选择“GET”HTTP 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. 单击“发送”并查看响应正文。 应该会看到一个查询结果。 该响应包括有关 Safeco Field 的关键地址信息。
  
4. 现在，在“参数”部分中，添加以下键/值对：

    | 密钥 | 值 | 返回
    |-----|------------|------|
    | 数字 | 1 |响应可能包含街道的边侧（左/右）以及该数字的偏移位置。|
    | returnSpeedLimit | 是 | 返回该地址的速度限制。|
    | returnRoadUse | 是 | 返回该地址的道路使用类型。 有关所有可能的道路使用类型，请参阅[道路使用类型](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。|
    | returnMatchType | 是| 返回匹配类型。 若要查看所有可能的值，请参阅[反向地址搜索结果](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="执行反向搜索。":::

5. 单击“发送”并查看响应正文。

6. 接下来，我们将添加 `entityType` 键，并将其值设置为 `Municipality`。 `entityType` 键将覆盖上一步中的 `returnMatchType` 键。 我们还需要删除 `returnSpeedLimit` 和 `returnRoadUse`，因为我们要请求有关市政当局的信息。  有关所有可能的实体类型，请参阅[实体类型](/rest/api/maps/search/getsearchaddressreverse#entitytype)。

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="搜索反向 entityType。":::

7. 单击“发送”。 将结果与步骤 5 中返回的结果进行比较。  由于请求的实体类型现在为 `municipality`，因此响应不包含街道地址信息。 此外，返回的 `geometryId` 可用于通过 Azure Maps [获取搜索多边形 API](/rest/api/maps/search/getsearchpolygon) 请求边界多边形。

>[!TIP]
>若要获取有关这些参数以及其他参数的详细信息，请参阅[“反向搜索参数”部分](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)。

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向地址十字街搜索来搜索十字街

在此示例中，我们将根据地址的坐标搜索十字街。

1. 在 Postman 应用中，选择“新建”以创建请求。 在“新建”窗口中，选择“HTTP 请求” 。 在“请求名称”中，输入请求名称。

2. 在生成器选项卡中选择“GET”HTTP 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。 请求应如下面的 URL 所示：
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="搜索十字街。":::
  
3. 单击“发送”并查看响应正文。 你会注意到，响应包含 `South Atlantic Street` 的 `crossStreet` 值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Maps 搜索服务 REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps 搜索服务最佳实践](how-to-use-best-practices-for-search.md)