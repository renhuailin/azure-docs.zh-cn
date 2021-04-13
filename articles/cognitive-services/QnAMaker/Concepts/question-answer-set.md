---
title: 设计知识库 - QnA Maker 概念
description: 了解如何设计知识库 - QnA Maker。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c21c82b5fd024598da30f4ac7b1ed01e64561e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000107"
---
# <a name="question-and-answer-pair-concepts"></a>问答对概念

知识库包含问答 (QnA) 对。  每对包含一个答案，一对包含与该答案关联的所有信息。 答案与数据库行或数据结构实例有点类似。

## <a name="question-and-answer-pairs"></a>问答集

问答 (QnA) 对中的必需设置如下：

* 问题 - 用户查询的文本，用于 QnA Maker 机器学习，与用户措辞不同但答案相同的问题文本匹配
* 答案 - 答案是当用户查询与关联的问题匹配时返回的响应

每对用一个 ID 表示。

问答对的可选设置包括：

* 问题的替代形式 - 有助于 QnA Maker 针对各种问题措辞返回正确答案
* 元数据：元数据是与 QnA 对关联的标记，以键值对形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。
* 多回合提示，用于继续多回合对话

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>以编辑身份添加到知识库

如果没有预先存在的内容来填充知识库，可以在 QnA Maker 门户中以编辑方式添加 QnA 对。 在[此处](../How-To/edit-knowledge-base.md)了解如何更新知识库。

## <a name="editing-your-knowledge-base-locally"></a>在本地编辑知识库

创建知识库后，建议在 [QnA Maker 门户](https://qnamaker.ai)中对知识库文本进行编辑，而不是通过导出并重新导入本地文件来进行编辑。 但是，有时可能需要在本地编辑知识库。

从“设置”页面上导出知识库，然后使用 Microsoft Excel 编辑知识库。 如果选择使用其他应用程序来编辑导出的文件，则应用程序可能会引发语法错误，因为它不是完全符合 TSV。 Microsoft Excel 的 TSV 文件通常不会引发任何格式设置错误。

在完成编辑后，从“设置”页重新导入 TSV 文件。 这将完全将当前知识库替换为导入的知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker 中的知识库生命周期](./development-lifecycle-knowledge-base.md)