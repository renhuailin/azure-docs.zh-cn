---
title: 使用 Azure Maps 服务模块 | Microsoft Azure Maps
description: 了解 Azure Maps 服务模块。 请参阅如何加载和使用此帮助程序库，以访问 Web 或 Node.js 应用程序中 Azure Maps REST 服务。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 8cd1cd820583a177ec65c9b63066119399e4bba0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435143"
---
# <a name="use-the-azure-maps-services-module"></a>使用 Azure Maps 服务模块

Azure Maps Web SDK 提供了一个服务模块。 此模块是一个帮助程序库，可以让用户使用 JavaScript 或 TypeScript 轻松地在 Web 或 Node.js 应用程序中使用 Azure Maps REST 服务。

## <a name="use-the-services-module-in-a-webpage"></a>在网页中使用服务模块

1. 创建新的 HTML 文件。
1. 加载 Azure Maps 服务模块。 可以通过两种方法加载这个帮助程序库：
    - 使用 Azure Maps 服务模块的 Azure 内容分发网络版本（全局托管）。 将脚本引用添加到文件的 `<head>` 元素中：

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - 或者，通过使用 [azure-maps-rest npm](https://www.npmjs.com/package/azure-maps-rest) 包在本地加载 Azure Maps Web SDK 源代码的服务模块，然后将其托管在你的应用中。 此程序包还包括了 TypeScript 定义。 使用此命令：
    
        > npm install azure-maps-rest
    
        然后将脚本引用添加到文件的 `<head>` 元素中：

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 创建身份验证管道。 必须先创建管道，然后才能初始化服务 URL 客户端终结点。 使用你自己的 Azure Maps 帐户密钥或 Azure Active Directory (Azure AD) 的凭据来对 Azure Maps 搜索服务客户端进行身份验证。 在此示例中，将创建搜索服务 URL 客户端。 

    如果使用订阅密钥进行身份验证：

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    如果使用 Azure AD 进行身份验证：

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    有关详细信息，请参阅[向 Azure Maps 进行身份验证](azure-maps-authentication.md)。

1. 以下代码使用新创建的 Azure Maps 搜索服务 URL 客户端对地址“1 Microsoft Way, Redmond, WA”进行地理编码。 该代码使用 `searchAddress` 函数，并将结果以表的形式显示在页面的正文中。

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    下面是完整的运行代码示例：

<br/>

<iframe height="500" scrolling="no" title="使用服务模块" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 中的 Pen <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>使用服务模块</a>，由 Azure Maps 发布 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure 政府版云支持

Azure Maps Web SDK 支持 Azure 政府版云。 用于访问 Azure Maps Web SDK 的所有 JavaScript 和 CSS URL 保持不变，但需要执行以下任务来连接到 Azure Maps 平台的 Azure 政府版云版本。

使用交互式地图控件时，请在创建 `Map` 类的实例之前添加以下代码行。 

```javascript
atlas.setDomain('atlas.azure.us');
```

对地图和服务进行身份验证时，请确保使用 Azure 政府版云平台中的 Azure Maps 身份验证详细信息。

使用服务模块时，需要在创建 API URL 终结点的实例时设置服务的域。 例如，下面的代码创建 `SearchURL` 类的实例，并将该域指向 Azure 政府云。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接访问 Azure Maps REST 服务，请将 URL 域更改为 `atlas.azure.us`。 例如，如果使用搜索 API 服务，请将 URL 域从更改 `https://atlas.microsoft.com/search/` 为 `https://atlas.azure.us/search/` 。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

有关使用服务模块的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示搜索结果](./map-search-location.md)

> [!div class="nextstepaction"]
> [从坐标获取信息](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](./map-route.md)