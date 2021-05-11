---
title: .NET 教程：搜索集成亮点
titleSuffix: Azure Cognitive Search
description: 使用此速查表，了解在已启用搜索的网站中使用的 .NET SDK 搜索集成查询。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 0f4f2b2c87f295aa98257521c420902a4e7302f7
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163582"
---
# <a name="4---net-search-integration-cheat-sheet"></a>4 - .NET 搜索集成速查表

在前面的课程中，您已将搜索添加到静态 Web 应用。 本课重点介绍了建立集成的必要步骤。 如果你正在寻找有关如何将搜索集成到 Web 应用的速查表，本文便介绍了你需要了解的内容。

此应用程序可用： 
* [示例](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [演示网站 - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearchdocuments"></a>Azure SDK Azure.Search.Documents

Function 应用使用 Azure SDK 进行认知搜索：

* NuGet：[Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
* 参考文档：[客户端库](/dotnet/api/overview/azure/search)

Function 应用使用资源名称、资源键和索引名称通过 SDK 向基于云的认知搜索 API 进行身份验证。 密码存储在静态 Web 应用设置中，并作为环境变量拉取到 Function 中。 

## <a name="configure-secrets-in-a-localsettingsjson-file"></a>在 local.settings.json 文件中配置机密

1. 在 `./api/` 创建一个名为 `local.settings.json` 的新文件，并将以下 JSON 对象复制到该文件中。

    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "",
        "FUNCTIONS_WORKER_RUNTIME": "dotnet",
        "SearchApiKey": "YOUR_SEARCH_QUERY_KEY",
        "SearchServiceName": "YOUR_SEARCH_RESOURCE_NAME",
        "SearchIndexName": "good-books"
      }
    }
    ```

1. 为下方内容更改为你自己的搜索资源值： 
    * YOUR_SEARCH_RESOURCE_NAME
    * YOUR_SEARCH_QUERY_KEY

## <a name="azure-function-search-the-catalog"></a>Azure Function：搜索目录

`Search` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Search.cs) 采用搜索词并在搜索索引中的文档之间搜索，并返回匹配项的列表。 

Azure Function 拉取搜索配置信息并完成查询。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Search.cs" highlight="22-24, 55" :::

## <a name="client-search-from-the-catalog"></a>客户端：从目录中搜索

通过以下代码在 React 客户端中调用 Azure Function。 

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Function：来自目录的建议

在用户键入内容时，`Suggest` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Suggest.cs) 将使用搜索词，并为搜索索引中的文档建议搜索词（如书籍标题和作者），并返回一个较小的匹配列表。 

搜索建议器 `sg` 在大容量上传期间使用的[架构文件](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/bulk-insert/BookSearchIndex.cs)中定义。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Suggest.cs" highlight="21-23, 50-52" :::

## <a name="client-suggestions-from-the-catalog"></a>客户端：来自目录的建议

建议函数 API 在 `\src\components\SearchBar\SearchBar.js` 作为组件初始化的一部分在 React 应用程序中调用：

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Function：获取特定文档 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Lookup.cs) 使用 ID 并从搜索索引中返回文档对象。 

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Lookup.cs" highlight="19-21, 42" :::

## <a name="client-get-specific-document"></a>客户端：获取特定文档 

此函数 API 在 `\src\pages\Details\Detail.js` 作为组件初始化的一部分在 React 应用程序中调用：

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="c-models-to-support-function-app"></a>支持函数应用的 C# 模型

以下模型用于支持此应用中的函数。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Models.cs" :::

## <a name="next-steps"></a>后续步骤

* [编制 Azure SQL 数据索引 ](search-indexer-tutorial.md)
