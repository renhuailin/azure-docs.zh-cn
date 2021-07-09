---
title: JavaScript 教程：搜索集成亮点
titleSuffix: Azure Cognitive Search
description: 使用此速查表，了解在已启用搜索的网站中使用的 JavaScript SDK 搜索集成查询。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 3353ff11a93fa4a79c266b7bd323071b55cd76c6
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109848098"
---
# <a name="4---javascript-search-integration-cheat-sheet"></a>4 - JavaScript 搜索集成速查表

在前面的课程中，您已将搜索添加到静态 Web 应用。 本课重点介绍了建立集成的必要步骤。 如果您正在寻找有关如何将搜索集成到 JavaScript 应用的备忘单，本文介绍您需要了解的内容。

此应用程序可用： 
* [示例](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [演示网站 - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

Function 应用使用 Azure SDK 进行认知搜索：

* NPM：[@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* 参考文档：[客户端库](/javascript/api/overview/azure/search-documents-readme)

Function 应用使用资源名称、资源键和索引名称通过 SDK 向基于云的认知搜索 API 进行身份验证。 密码存储在静态 Web 应用设置中，并作为环境变量拉取到 Function 中。 

## <a name="configure-secrets-in-a-configuration-file"></a>在配置文件中配置密码

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure Function：搜索目录

`Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) 采用搜索词并在搜索索引中的文档之间搜索，并返回匹配项的列表。 

搜索 API 的路由包含在 [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) 绑定中。

Azure Function 拉取搜索配置信息并完成查询。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>客户端：从目录中搜索

通过以下代码在 React 客户端中调用 Azure Function。 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure Function：来自目录的建议

在用户键入内容时，`Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) 将使用搜索词，并为搜索索引中的文档建议搜索词（如书籍标题和作者），并返回一个较小的匹配列表。 

搜索建议器 `sg` 在大容量上传期间使用的[架构文件](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json)中定义。

建议 API 的路由包含在 [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) 绑定中。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>客户端：来自目录的建议

建议函数 API 在 `\src\components\SearchBar\SearchBar.js` 作为组件初始化的一部分在 React 应用中调用：

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure Function：获取特定文档 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) 使用 ID 并从搜索索引中返回文档对象。 

查找 API 的路由包含在 [function.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) 绑定中。

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>客户端：获取特定文档 

此函数 API 在 `\src\pages\Details\Detail.js` 作为组件初始化的一部分在 React 应用程序中调用：

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>后续步骤

* [编制 Azure SQL 数据索引 ](search-indexer-tutorial.md)
