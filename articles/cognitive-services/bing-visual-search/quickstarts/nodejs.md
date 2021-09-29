---
title: 快速入门：使用 REST API 和 Node.js 获取图像见解 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视觉搜索 API 和 Node.js 上传图像，并获取有关该图像的见解。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: eeef03820a0a96376f9e7cb45bc22cc3e2331b22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663244"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>快速入门：使用必应视觉搜索 REST API 和 Node.js 获取图像见解

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应视觉搜索 API 进行第一次调用。 此简单 JavaScript 应用程序会将一个图像上传到该 API，并显示返回的相关信息。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/download/)
* JavaScript 请求模块。 可以使用 `npm install request` 命令安装该模块。
* 表单数据模块。 可以使用 `npm install form-data` 命令安装该模块。 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建 JavaScript 文件，并设置以下要求：

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. 为 API 终结点、订阅密钥和图像路径创建变量。 对于 `baseUri` 值，可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. 创建一个名为 `requestCallback()` 的函数来输出 API 的响应。

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>构造并发送搜索请求

1. 上传本地图像时，表单数据必须包含 `Content-Disposition` 标头。 将其 `name` 参数设置为“image”，并且将 `filename` 参数设置为映像的文件名。 表单内容包括图像的二进制数据。 可以上传的最大图像大小为 1 MB。

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. 使用 `FormData()` 创建新的 `FormData` 对象，并使用 `fs.createReadStream()` 将图像路径附加至对象。
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. 使用请求库上传图像，并调用 `requestCallback()` 输出响应。 将订阅密钥添加到请求标头。

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建视觉搜索单页 Web 应用](../tutorial-bing-visual-search-single-page-app.md)