---
title: 快速入门：使用 REST API 创建搜索索引
titleSuffix: Azure Cognitive Search
description: 在本 REST API 快速入门中，了解如何使用 Postman 或 Visual Studio Code 调用 Azure 认知搜索 REST API。
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711283"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>快速入门：使用 REST API 创建 Azure 认知搜索索引

本文介绍如何使用 [Azure 认知搜索 REST API](/rest/api/searchservice) 和用于发送和接收请求的 API 客户端以交互方式构建 REST API 请求。 在编写任何代码之前，可以使用 API 客户端按照这些说明发送请求和查看响应。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

现在你已了解如何执行核心任务，你可以继续使用其他 REST API 调用来获取更高级的功能，例如索引器或[设置将内容转换添加到索引的扩充管道](cognitive-search-tutorial-blob.md)。 在下一步中，我们建议你访问以下链接：

> [!div class="nextstepaction"]
> [教程：使用 REST 和 AI 从 Azure Blob 生成可搜索的内容](cognitive-search-tutorial-blob.md)