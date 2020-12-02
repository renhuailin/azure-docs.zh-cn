---
title: 快速入门：使用 Python 调用必应自定义搜索终结点 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 按照此快速入门开始使用 Python 从必应自定义搜索实例请求搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 9c0a5b849ff3984c7fccc83a149100b462295da5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352025"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>快速入门：使用 Python 调用必应自定义搜索终结点

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用此快速入门了解如何从必应自定义搜索实例请求搜索结果。 虽然此应用程序是使用 Python 编写的，但必应自定义搜索 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) 上获得。

## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 有关详细信息，请参阅[快速入门：创建首个必应自定义搜索实例](quick-start.md)。
- [Python](https://www.python.org/) 2.x 或 3.x。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

- 在你喜欢使用的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句。 为订阅密钥、自定义配置 ID 和搜索词创建变量。

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>发送和接收搜索请求 

1. 构造请求 URL，方法是：将搜索词追加​​到 `q=` 查询参数后面，并将搜索实例的自定义配置 ID 追加​​到 `customconfig=` 参数后面。 使用与号 (`&`) 分隔参数。 可以为你的资源使用以下代码中的全局终结点，或者使用 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. 将请求发送到必应自定义搜索实例，并输出返回的搜索结果。  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)