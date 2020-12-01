---
title: 数据源和内容类型-QnA Maker
description: 了解如何从数据源和支持的内容类型（包括许多标准结构化文档，如 PDF、.DOCX 和 TXT QnA Maker）导入问题与答案对。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a6488d2c7611513528ff02d4e620124763912730
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353249"
---
# <a name="importing-from-data-sources"></a>从数据源导入

知识库由公共 Url 和文件中引入的问题和答案对构成。

## <a name="data-source-locations"></a>数据源位置

内容将从数据源引入知识库。 数据源位置是不需要身份验证的 **公共 url 或文件**。

[SharePoint 文件](../how-to/add-sharepoint-datasources.md)是由身份验证保护的，这是个例外。 SharePoint 资源必须是文件，而不是网页。 如果 URL 以 web 扩展名（如 .ASPX）结尾，则不会从 SharePoint 导入 QnA Maker。

## <a name="chit-chat-content"></a>Chit-聊天内容

Chit 内容集以多种语言和会话样式提供的完整内容数据源。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。 了解 [如何将 chit 内容添加](../how-to/chit-chat-knowledge-base.md) 到知识库中。

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要 `.tsv` 包含问题和答案的结构化文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | 源| Metadata (1 密钥：1值)  |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>通过导入的结构化多转换格式

可以采用文件格式创建多轮会话 `.tsv` 。 格式使您能够通过分析 (与其他进程的先前聊天日志来创建多轮会话，而不是使用 QnA Maker) ，然后通过自动化创建该 `.tsv` 文件。 导入文件以替换现有知识库。

> [!div class="mx-imgBorder"]
> ![3级多轮问题的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

对于多轮 `.tsv` ，特定于多重转换的列是 **提示**。 例如 `.tsv` ，在 Excel 中显示的一个示例，显示了定义多轮子项所要包括的信息：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** 为数值，而 "文本" 是不应包含 " **markdown" 的** 文本。

> [!div class="mx-imgBorder"]
> ![Excel 中所示的多个问题示例](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>导出为示例

如果不确定如何在文件中表示 QnA 对 `.tsv` ：
* 使用 [GitHub 中的此可下载示例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或在 QnA Maker 门户中创建对，保存，然后导出知识库，以获取有关如何表示对的示例。

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知识库的文档内容类型
内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。

### <a name="file-and-url-data-types"></a>文件和 URL 数据类型

下表汇总了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|URL|常见问题解答<br> （平面，包含节或主题主页）<br>支持页面 <br> （单页操作指南文章、故障排除文章，等等）|[纯文本常见问题解答](../troubleshooting.md)、 <br>[包含链接的常见问题解答](https://www.microsoft.com/en-us/software-download/faq)、<br> [包含主题主页的常见问题解答](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支持文章](./best-practices.md)|
|PDF / DOC|常见问题解答、<br> 产品手册、<br> 小册子、<br> 论文、<br> 传单策略、<br> 支持指南、<br> 结构化 QnA，<br> 等等|**无多轮**<br>[结构化 QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)，<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [示例 semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)，<br> [示例白色 paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)，<br><br>**多项转换**：<br>[Surface Pro (.docx) ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso (.docx) 权益权益 ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso (pdf) 权益 ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|结构化 QnA 文件<br> （包括 RTF、HTML 支持）|**不启用多个**：<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**多项转换**：<br>[结构化简单 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface 便携机 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|结构化 QnA 文件|[示例 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果需要对数据源进行身份验证，请考虑以下方法，以便将该内容导入 QnA Maker：

* 手动下载文件并导入 QnA Maker
* 从经过身份验证的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)添加文件

### <a name="url-content"></a>URL 内容

可以通过 QnA Maker 中的 **URL** 导入两种类型的文档：

* 常见问题解答 URL
* 支持 URL

每种类型都指示一个预期格式。

### <a name="file-based-content"></a>基于文件的内容

您可以在 [QnA Maker 门户](https://www.qnamaker.ai)中从公共源或本地文件系统将文件添加到知识库中。

### <a name="content-format-guidelines"></a>内容格式指南

了解有关不同文件的 [格式准则](../reference-document-format-guidelines.md) 的详细信息。

## <a name="next-steps"></a>后续步骤

了解问题中存储的信息 [和答案 (QnA) 对](question-answer-set.md)。