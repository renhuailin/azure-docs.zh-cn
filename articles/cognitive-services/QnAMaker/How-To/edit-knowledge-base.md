---
title: 编辑知识库 - QnA Maker
description: QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 8d9f54c1fcd142c77fb73c294b81bf0f11ee083f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99557699"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>编辑知识库中的 QnA 对

QnA Maker 允许你通过提供易于使用的编辑体验来管理知识库的内容。

QnA 对是从数据源（如文件或 URL）进行添加，或是作为编辑源进行添加。 编辑源指示 QnA 对是在 QnA 门户中手动添加。 所有 QnA 对都可进行编辑。

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>问答集

知识库包含问答 (QnA) 对。  每对包含一个答案，一对包含与该答案关联的所有信息。 答案与数据库行或数据结构实例有点类似。 问答 (QnA) 对中的必需设置如下：

* 问题 - 用户查询的文本，用于 QnA Maker 机器学习，与用户措辞不同但答案相同的问题文本匹配
* 答案 - 答案是当用户查询与关联的问题匹配时返回的响应

每对用一个 ID 表示。

问答对的可选设置包括：

* 问题的替代形式 - 有助于 QnA Maker 针对各种问题措辞返回正确答案
* 元数据：元数据是与 QnA 对关联的标记，以键值对形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。
* 多回合提示，用于继续多回合对话

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>添加编辑 QnA 对

如果没有预先存在的内容来填充知识库，可以在 QnA Maker 门户中以编辑方式添加 QnA 对。

1. 登录 [QnA 门户](https://www.qnamaker.ai/)，然后选择要将 QnA 对添加到其中的知识库。
1. 在知识库的“编辑”页面上，选择“添加 QnA 对”以添加新的 QnA 对 。

    > [!div class="mx-imgBorder"]
    > ![添加 QnA 对](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 在新 QnA 对行中，添加所需问题和答案字段。 其他字段是可选字段。 可随时更改所有字段。

1. （可选）添加[替代短语](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)。 替代短语是任何形式的问题，与原始问题明显不同，但应提供相同的答案。

    当发布知识库并打开了[主动学习](use-active-learning.md)时，QnA Maker 会收集替代短语选项以供你接受。 选择这些选项是为了增加预测准确度。

1. （可选）添加[元数据](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)。 若要查看元数据，请在上下文菜单中选择“查看选项”。 元数据针对客户端应用程序（如聊天机器人）提供的答案提供筛选器。

1. （可选）添加[跟进提示](multiturn-conversation.md)。 跟进提示向客户端应用程序提供其他对话路径，以展示给用户。

1. 选择“保存并训练”以查看包含新 QnA 对的预测。

## <a name="rich-text-editing-for-answer"></a>用于答案的富文本编辑

答案的富文本编辑可从简单工具栏提供 markdown 样式。

1. 选择答案的文本区域，富文本编辑器工具栏会显示在 QnA 对行中。

    > [!div class="mx-imgBorder"]
    > ![富文本编辑器的屏幕截图，其中包含一个 QnA 对行的问题和答案。](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    答案中已有的任何文本在用户从机器人看到时都会正确显示。

1. 编辑文本。 从富文本编辑工具栏选择格式设置功能，或使用切换功能切换到 markdown 语法。

    > [!div class="mx-imgBorder"]
    > ![使用富文本编辑器编写文本并设置格式，然后保存为 markdown。](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |富文本编辑器功能|键盘快捷键|
    |--|--|
    |在富文本编辑器与 markdown 之间切换。 `</>`|Ctrl+M|
    |粗体。 **B**|CTR+LB|
    |斜体，使用斜体 I 来指示|CTRL+I|
    |无序列表||
    |有序列表||
    |段落样式||
    |图像 - 添加从公共 URL 提供的图像。|Ctrl+G|
    |添加指向公开提供的 URL 的链接。|Ctrl+K|
    |表情符 - 从选择的表情符进行添加。|Ctrl+E|
    |高级菜单 - 撤消|CTRL+Z|
    |高级菜单 - 恢复|Ctrl+Y|

1. 使用富文本工具栏中的“图像”图标向答案添加图像。 就地编辑器需要可公开访问的图像 URL 和图像的替代文本。


    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示就地编辑器，其中包含可公开访问的图像 URL 和输入的图像替代文本。](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 可通过选择答案中的文本，然后选择工具栏中的“链接”图标，或通过选择工具栏中的“链接”图标，然后输入新文本和 URL，来添加 URL 链接。

    > [!div class="mx-imgBorder"]
    > ![使用富文本编辑器添加一个可公开访问的图像及其替代文本。](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>编辑 QnA 对

无论原始数据源如何，都可以编辑任何 QnA 对中的任何字段。 由于当前“视图选项”设置（位于上下文工具栏中），某些字段可能不可见。

## <a name="delete-a-qna-pair"></a>删除 QnA 对

要删除 QnA，请单击 QnA 行最右侧的删除图标。 这是一项永久性操作。 无法撤消。 请考虑在删除对之前从“发布”页面导出知识库。

![删除 QnA 对](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>查找 QnA 对 ID

如果需要查找 QnA 对 ID，可以在两个位置找到它：

* 将鼠标悬停在感兴趣的 QnA 对行上的删除图标上方。 悬停文本包含 QnA 对 ID。
* 导出知识库。 知识库中的每个 QnA 对都包含 QnA 对 ID。

## <a name="add-alternate-questions"></a>添加替代问题

向现有 QnA 对添加替代问题，以提高与用户查询匹配的可能性。

![添加替代问题](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>链接 QnA 对

链接 QnA 对功能随[跟进提示](multiturn-conversation.md)提供。 这是 QnA 对之间的逻辑连接，在知识库级别上进行管理。 可以在 QnA Maker 门户中编辑跟进提示。

无法在答案的元数据中链接 QnA 对。

## <a name="add-metadata"></a>添加元数据

首先选择“视图选项”，然后选择“显示元数据”，从而添加元数据对 。 这会显示元数据列。 接下来，选择 **+** 签名以添加元数据对。 此对包含一个键和一个值。

有关元数据的详细信息，请参阅 QnA Maker 门户中的元数据快速入门：
* [创作 - 将元数据添加到 QnA 对](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查询预测 - 按元数据筛选应答](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>保存对 QnA 对进行的更改

在编辑后定期选择“保存并训练”，以避免丢失更改。

![添加元数据](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>何时使用富文本编辑和 markdown

借助答案[富文本编辑](#add-an-editorial-qna-set)，你（作者）可以使用格式设置工具栏快速选择并设置文本格式。

当需要自动生成内容以创建要作为 CI/CD 管道的一部分导入或用于[批量测试](../index.yml)的知识库时，[markdown](../reference-markdown-format.md) 是一种更好的工具。

## <a name="editing-your-knowledge-base-locally"></a>在本地编辑知识库

创建知识库后，建议在 [QnA Maker 门户](https://qnamaker.ai)中对知识库文本进行编辑，而不是通过导出并重新导入本地文件来进行编辑。 但是，有时可能需要在本地编辑知识库。

从“设置”页面上导出知识库，然后使用 Microsoft Excel 编辑知识库。 如果选择使用其他应用程序来编辑导出的文件，则应用程序可能会引发语法错误，因为它不是完全符合 TSV。 Microsoft Excel 的 TSV 文件通常不会引发任何格式设置错误。

在完成编辑后，从“设置”页重新导入 TSV 文件。 这将完全将当前知识库替换为导入的知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对知识库进行协作](../index.yml)

* [管理 QnA Maker 使用的 Azure 资源](set-up-qnamaker-service-azure.md)
