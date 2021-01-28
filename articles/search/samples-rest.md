---
title: REST 示例
titleSuffix: Azure Cognitive Search
description: 查找使用搜索或管理 REST Api 的 Azure 认知搜索演示 REST 代码示例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956470"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>适用于 Azure 认知搜索的 REST 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 REST API 示例。 这些示例使用 [**搜索 REST api**](/rest/api/searchservice)。

REST 是适用于 Azure 认知搜索的权威编程接口，可通过编程方式调用的所有操作首先在 REST 中提供，然后在 Sdk 中提供。 出于此原因，文档中的大多数示例都利用 REST Api 来演示或解释重要的概念。

REST 示例通常在 Postman 上进行开发和测试，但你可以使用支持 HTTP 调用的任何客户端：

+ 开始使用 [快速入门：使用 REST Api 创建 Azure 认知搜索索引](search-get-started-rest.md) ，以帮助你构建 HTTP 调用。
+ 如果你使用的是 Visual Studio Code，请尝试在 [Azure 认知搜索 Visual Studio Code 扩展](search-get-started-vs-code.md)（当前为预览版）。

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示了功能和工作流。 其中的许多示例在教程、快速入门和操作指南文章中进行了引用。 可在 GitHub 上的 [**Azure 示例/postman-示例**](https://github.com/Azure-Samples/azure-search-postman-samples) 中找到这些示例。

| 示例 | 文章 |
|---------|---------|
| [快速入门](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | [快速入门：使用 REST Api 创建搜索索引](search-get-started-rest.md)。 本文介绍了使用示例数据创建、加载和查询搜索索引的基本工作流。 |
| [教程](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | 教程的源代码 [：使用 REST 和 AI 从 Azure blob 生成可搜索的内容](cognitive-search-tutorial-blob.md)。 本文介绍如何创建技能组合来循环访问 Azure blob，以提取信息并推断结构。|
| [调试-会话](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | 教程的源代码 [：诊断、修复和提交对技能组合所做的更改](cognitive-search-tutorial-debug-sessions.md)。 本文介绍如何在 Azure 门户中使用技能组合调试会话。 REST 用于创建在调试过程中使用的对象。|
| [自定义分析器](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | 教程的源代码 [：创建电话号码的自定义分析器](tutorial-create-custom-analyzer.md)。 本文介绍如何使用分析器保留可搜索内容中的模式和特殊字符。|
| [知识-存储](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | [使用 REST 和 Postman 创建知识存储](knowledge-store-create-rest.md)的源代码。 本文介绍了填充用于知识挖掘工作流的知识存储的必要步骤。 |
| [预算](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | [如何对根据进行形状和导出](knowledge-store-projections-examples.md)的源代码。 本文介绍如何在知识库中指定物理数据结构。|
| [索引加密-blob](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | 有关 [如何使用 blob 索引器和技能集为加密的 blob 编制索引](search-howto-index-encrypted-blobs.md)的源代码。 本文介绍如何在 Azure Blob 存储中为以前使用 Azure Key Vault 加密的文档编制索引。 |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但在文档中未被引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [查询-示例](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | 演示各种查询技术（包括模糊搜索、正则表达式和通配符搜索、自动完成等等）的 Postman 集合。 |