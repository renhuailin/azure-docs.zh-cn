---
title: 快速入门：使用 Node.js 向 REST API 发送搜索请求 - 必应实体搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Node.js 将请求发送到必应实体搜索 REST API，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9311329ea4115d49f36dd7d39782bbd748a356b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106098"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>快速入门：使用 Node.js 向必应实体搜索 REST API 发送搜索请求

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

参考本快速入门对必应实体搜索 API 进行第一次调用并查看 JSON 响应。 这个简单的 JavaScript 应用程序会向该 API 发送一个新闻搜索查询并显示响应。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js) 上获得。

虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。

* [JavaScript 请求库](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在最喜爱的 IDE 或编辑器中创建新的 JavaScript 文件，并设置严格性和 HTTPS 要求。

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. 为 API 终结点、订阅密钥和搜索查询创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. 将市场和查询参数追加到名为 `query` 的字符串。 确保使用 `encodeURI()` 对查询进行 URL 编码。
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>处理和分析响应

1. 定义一个名为 `response_handler()` 的函数，该函数使用 HTTP 调用 `response` 作为参数。 

2. 在此函数中，定义一个包含 JSON 响应正文的变量。  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. 调用 `data` 标志时，存储响应的正文。
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. 指示了 `end` 标志后，请分析并输出 JSON。

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
    ```

## <a name="send-a-request"></a>发送请求

1. 创建名为 `Search()` 的函数，以便发送搜索请求。 在其中执行以下步骤：

2. 在此函数中，创建一个包含请求参数的 JSON 对象。 对方法使用 `Get`，并添加主机和路径信息。 将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。 

3. 使用 `https.request()` 来发送请求，请求中包含搜索参数和之前创建的响应处理程序。
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. 调用 `Search()` 函数。

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../overview.md )
* [必应实体搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。
