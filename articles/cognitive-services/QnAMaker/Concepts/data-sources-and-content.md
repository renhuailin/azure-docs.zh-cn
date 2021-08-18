---
title: 数据源和内容类型 - QnA Maker
description: 了解如何从数据源和受支持的内容类型（包括许多标准的结构化文档，如 PDF、DOCX 和 TXT）导入问题与答案对 - QnA Maker。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: e205d403a8d482d5af3278ed42dde60e5808a00a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280794"
---
# <a name="importing-from-data-sources"></a>从数据源导入

知识库包括通过公共 URL 和文件引入的问题和答案对。

## <a name="data-source-locations"></a>数据源位置

内容会从数据源引入到知识库中。 数据源位置是公共 URL 或文件，不需要进行身份验证。

[SharePoint 文件](../how-to/add-sharepoint-datasources.md)是个例外，它受身份验证保护。 SharePoint 资源必须是文件，不能是网页。 

QnA Maker 支持以 .ASPX Web 扩展名结尾的公共 URL，这些 URL 不通过身份验证进行保护。

## <a name="chit-chat-content"></a>聊天内容

聊天内容集作为完整的内容数据源，以多种语言和聊天样式提供。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。 了解[如何将聊天内容添加到知识库](../how-to/chit-chat-knowledge-base.md)。

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要一个包含问题和答案的结构化 `.tsv` 文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | 源| 元数据（1 个键：1 个值） |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>通过导入提供的结构化多回合格式

可以创建采用 `.tsv` 文件格式的多轮聊天。 利用该格式，可以通过分析以前的聊天日志来创建多回合聊天（使用其他过程，不使用 QnA Maker），然后通过自动化创建 `.tsv` 文件。 导入该文件以替换现有知识库。

> [!div class="mx-imgBorder"]
> ![3 级多回合问题的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

对于多回合 `.tsv`，特定于多回合的列是“Prompts”。 在 Excel 中显示的示例 `.tsv` 显示了为定义多回合子项而需要包括的信息：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

DisplayOrder 为数值，displayText 是不应该包括 Markdown 的文本 。

> [!div class="mx-imgBorder"]
> ![Excel 中显示的多回合问题示例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>导出为示例

如果不确定如何在 `.tsv` 文件中表示 QnA 对，请执行以下操作：
* 使用这个[可从 GitHub 下载的示例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或在 QnA Maker 门户中创建该对，保存，然后导出知识库，以获取有关如何表示该对的示例。

## <a name="unstructured-data-format"></a>非结构化数据格式 

还可以基于通过文件导入的非结构化内容来创建知识库。 目前，只有进行文档上传之后，此功能才适用于那些采用受支持文件格式的文档。

> [!IMPORTANT]
> 只有进行文件上传之后，才在自定义问答（预览版）中提供对非结构化内容的支持

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知识库的文档的内容类型
内容类型包括许多标准的结构化文档，如 PDF、DOC 和 TXT。

### <a name="file-and-url-data-types"></a>文件和 URL 数据类型

下表汇总了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|URL|常见问题<br> （平面，包含节或主题主页）<br>支持页面 <br> （单页操作指南文章、故障排除文章，等等）|[纯文本常见问题解答](../troubleshooting.md)、 <br>[包含链接的常见问题解答](https://www.microsoft.com/en-us/software-download/faq)、<br> [包含主题主页的常见问题解答](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支持文章](./best-practices.md)|
|PDF / DOC|常见问题解答、<br> 产品手册、<br> 小册子、<br> 论文、<br> 传单策略、<br> 支持指南、<br> 结构化 QnA，<br> 等等|没有多回合<br>[Structured QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)、<br> [Unstructured blog.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Introducing-surface-laptop-4-and-new-access.pdf)、<br> [Unstructured white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/sample-unstructured-paper.pdf)<br><br>多回合:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|结构化 QnA 文件<br> （包括 RTF、HTML 支持）|没有多回合：<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>多回合:<br>[Structured simple FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface laptop FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|结构化 QnA 文件|[示例 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果需要针对数据源进行身份验证，请考虑使用以下方法将该内容导入到 QnA Maker 中：

* 手动下载该文件并将其导入 QnA Maker
* 从经过身份验证的 [SharePoint 位置](../how-to/add-sharepoint-datasources.md)添加该文件

### <a name="url-content"></a>URL 内容

可以在 QnA Maker 中通过 URL 导入两种类型的文档：

* 常见问题解答 URL
* 支持 URL

每种类型都指示一个应有的格式。

### <a name="file-based-content"></a>基于文件的内容

可以在 [QnA Maker 门户](https://www.qnamaker.ai)中将文件从公共源或本地文件系统添加到知识库。

### <a name="content-format-guidelines"></a>内容格式指南

详细了解不同文件的[格式准则](../reference-document-format-guidelines.md)。

## <a name="next-steps"></a>后续步骤

了解如何[编辑 QnA](../how-to/edit-knowledge-base.md)。
