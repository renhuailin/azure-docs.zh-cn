---
title: Markdown 格式 - QnA Maker
description: 下面是可以在 QnA Maker 的答案文本中使用的 Markdown 格式的列表。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352263"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 答案文本中支持的 Markdown 格式

QnA Maker 以 Markdown 形式存储答案文本。 有很多不同形式的 Markdown。 为了确保返回并正确显示答案文本，请使用此参考。

使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** 教程验证 Markdown。 此教程具有用于快速复制/粘贴验证的 **试一试** 功能。

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何时使用富文本编辑而不是 Markdown

借助答案的[富文本编辑](How-To/edit-knowledge-base.md#add-an-editorial-qna-set)，你（作者）可以使用格式设置工具栏快速选择文本并设置其格式。

当需要自动生成内容以创建要作为 CI/CD 管道的一部分导入或用于[批量测试](./index.yml)的知识库时，Markdown 是一种更好的工具。

## <a name="supported-markdown-format"></a>支持的 Markdown 格式

下面是可以在 QnA Maker 的答案文本中使用的 Markdown 格式的列表。

|用途|格式|示例 Markdown|渲染<br>在聊天机器人中显示|
|--|--|--|--|
2 个句子之间的新行。|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![2 个句子之间的换行格式](./media/qnamaker-concepts-datasources/format-newline.png)|
|从 h1 到 h6 的标头，`#` 数指示是哪个标头。 1 `#` 为 h1。|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Markdown 标头格式](./media/qnamaker-concepts-datasources/format-headers.png)<br>![Markdown 标头 H1 至 H5 的格式](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|斜体 |`*text*`|`How do I create a bot with *QnA Maker*?`|![斜体格式](./media/qnamaker-concepts-datasources/format-italics.png)|
|强调（粗体）|`**text**`|`How do I create a bot with **QnA Maker**?`|![强调标记（表示粗体）的格式](./media/qnamaker-concepts-datasources/format-strong.png)|
|链接的 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 格式（超链接）](./media/qnamaker-concepts-datasources/format-url.png)|
|*公共图像的 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![公共图像 URL 格式 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|删除线|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![删除线格式](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|粗体和斜体|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![粗体和斜体格式](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|链接的粗体 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![粗体 URL 格式](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|链接的斜体 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![斜体 URL 格式](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|转义 Markdown 符号|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![转义 Markdown 符号格式。](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|有序列表|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>前面的示例使用了 Markdown 中内置的自动编号。<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>前面的示例使用了显式编号。|![已排序列表格式](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|无序列表|`\n * item1 \n * item2`<br>或<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![无序列表格式](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|嵌套列表|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>可以将已排序列表和无序列表嵌套在一起。 制表符 `\t` 指示子元素的缩进级别。|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![嵌套无序列表格式](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![嵌套已排序列表格式](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker 不会以任何方式处理图像。 由客户端应用程序负责呈现图像。

如果要使用更新/替换知识库 API 添加内容，并且内容/文件包含 html 标记，则可以通过确保以编码格式转换标记的左侧和右侧部分来保留文件中的 HTML。

| 保留 HTML  | 在 API 请求中的表示形式  | 在 KB 中的表示形式 |
|-----------|---------|-------------------------|
| 是 | \&lt;br\&gt; | &lt;br&gt; |
| 是 | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;header&lt;/h3&gt; |

此外，CR LF(\r\n) 在 KB 中转换为 \n。 LF(\n) 保持原样。 如果要转义任何转义序列（如 \t 或 \n），可以使用反斜杠，例如：“\\\\r\\\\n”和“\\\\t”

## <a name="next-steps"></a>后续步骤

查看批量测试[文件格式](reference-tsv-format-batch-testing.md)。