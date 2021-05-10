---
title: 限制和边界 - QnA Maker
description: QnA Maker 对部分知识库和服务具有元限制。 为了测试和发布，请务必将知识库保留在这些限制内。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816361"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知识库限制和边界

以下提供的 QnA Maker 限制是 [Azure 认知搜索定价层限制](../../search/search-limits-quotas-capacity.md)和 [QnA Maker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的组合。 你需要了解这两组限制，才能了解每个资源可以创建多少个知识库以及每个知识库可以增长多少。

## <a name="knowledge-bases"></a>知识库

知识库最大数量基于 [Azure 认知搜索层限制](../../search/search-limits-quotas-capacity.md)。

|**Azure 认知搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|已发布知识库的最大允许数量|2|14|49|199|199|2,999|

 例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引 `testkb` 用于所有知识库以进行创作和测试。

## <a name="extraction-limits"></a>提取限制

### <a name="file-naming-constraints"></a>文件命名约束

文件名中不得包含下列字符：

|请勿使用字符|
|--|
|单引号 `'`|
|双引号 `"`|

### <a name="maximum-file-size"></a>文件大小上限

|格式|最大文件大小 (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>最大文件数

可提取文件的最大数量和最大文件大小取决于 [QnAMaker 定价层限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)。

> [!NOTE]
> QnA Maker 托管（预览版）是一项免费服务，对可以添加的源数量没有限制。 对于管理 API 和预测 API，目前的吞吐量上限为每秒 10 个事务。

### <a name="maximum-number-of-deep-links-from-url"></a>URL 中的最大深层链接数

从 URL 页面提取 QnA 时可抓取的深层链接的最大数量为 20。

## <a name="metadata-limits"></a>元数据限制

元数据表示为基于文本的键值对，例如 `product:windows 10`。 它以小写形式存储和比较。

### <a name="by-azure-cognitive-search-pricing-tier"></a>按 Azure 认知搜索定价层

每个知识库的元数据字段的最大数量基于 [Azure 认知搜索层限制](../../search/search-limits-quotas-capacity.md)。

|**Azure 认知搜索层** | **免费** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每个 QnA Maker 服务的元数据字段的最大数量（包括所有知识库）|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>按名称和值

下表列出了元数据名称和值的长度和可接受的字符。

|项|允许的字符|Regex 模式匹配|最大字符|
|--|--|--|--|
|名称（键）|允许<br>字母数字（字母和数字）<br>`_`（下划线）<br> 不能含有空格。|`^[a-zA-Z0-9_]+$`|100|
|值|允许除以下字符外的所有字符<br>`:`（冒号）<br>`|`（竖线）<br>只允许一个值。|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知识库内容限制
知识库中内容的总体限制：
* 答案文本的长度：25,000 个字符
* 问题文本的长度：1,000 个字符
* 元数据键文本的长度：100 个字符
* 元数据值文本的长度：500 个字符
* 支持的元数据名称字符：字母、数字和短划线 (`_`)
* 支持的元数据值字符：除 `:` 和 `|` 以外的所有字符
* 文件名长度：200
* 支持的文件格式:：“.tsv”、“.pdf”、“.txt”、“.docx”、“.xlsx”。
* 备用问题的最大数量：300
* 问-答对的最大数量：取决于所选的 [Azure 认知搜索层](../../search/search-limits-quotas-capacity.md#document-limits)。 问答对映射到 Azure 认知搜索索引上的文档。
* URL/HTML 页面：100 万个字符

## <a name="create-knowledge-base-call-limits"></a>创建知识库调用限制：
表示每个创建知识库操作的限制；即，单击“创建知识库”或调用 CreateKnowledgeBase API。
* 每个答案的备用问题的推荐最大数量：300
* 最大 URL 数：10
* 最大文件数：10
* 每个调用允许的 QnA 最大数量：1000

## <a name="update-knowledge-base-call-limits"></a>更新知识库调用限制
表示每个更新操作的限制；即，单击“保存并培训”或调用 UpdateKnowledgeBase API。
* 每个源名称的长度：300
* 添加或删除的备用问题的推荐最大数量：300
* 添加或删除的元数据字段的最大数量：10
* 可以刷新的 URL 的最大数量：5
* 每个调用允许的 QnA 最大数量：1000

## <a name="add-unstructured-file-limits"></a>添加非结构化文件限制

> [!NOTE]
> * 如果需要使用的文档的大小超出允许的限制，可将文件拆分成多个较小的文件，然后再将其发送到 API。 

下面是使用非结构化文件创建 KB 或调用 CreateKnowledgeBase API 时的限制：
* 文件的长度：我们将提取前 32000 个字符
* 每个文件最多 3 个响应。

## <a name="prebuilt-question-answering-limits"></a>预生成的问答限制

> [!NOTE]
> * 如果需要使用的文档的大小超出允许的限制，可将文本拆分成较小的文本块，然后再将其发送到 API。 
> * 文档是由文本字符构成的单个字符串。  

下面是将预生成的 API 用于生成响应或调用 GenerateAnswer API 时的限制：
* 文档数：5
* 单个文档的最大大小：5,120 个字符
* 每个文档最多 3 个响应。

## <a name="next-steps"></a>后续步骤

了解何时以及如何更改[服务定价层](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)。
