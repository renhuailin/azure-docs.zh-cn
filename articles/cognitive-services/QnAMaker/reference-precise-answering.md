---
title: 使用应答范围检测精确应答-QnA Maker
description: 了解 QnA Maker 托管中提供的精确应答功能。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 6c597e8fd48eb157b69c918ae5fd5f699611ad75
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703399"
---
# <a name="precise-answering"></a>精确应答

QnA Maker 托管 (预览版) 中引入的精确应答功能，您可以从任何用户查询的知识库中获得的最佳候选答案中获得精确的简短答案。 此功能使用深度学习模型，此模型在运行时了解用户查询的意图，并检测答案段的准确简短答案（如果答案在答案中存在）。 

此功能在 "测试" 窗格中默认启用，因此可以测试特定于方案的功能。 对于内容开发人员和最终用户来说，此功能非常有用。 现在，内容开发人员无需针对知识库中的每个事实手动组织特定的 QnA 对，最终用户无需浏览从服务返回的整个答案，就能找到回答用户查询的实际事实。 

## <a name="precise-answering-on-qna-maker-portal"></a>QnA Maker 门户上精确回答

在 QnA Maker 门户中，打开 "测试" 窗格时，会显示一个用于在顶部 **显示简短答案** 的选项。 默认情况下将选择此选项。 当你在 "测试" 窗格中输入查询时，如果答案产生了一个简短答案，你会看到一个简短答案以及答案段。 
 
![托管的已启用的测试窗格](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

如果只想在测试窗格中查看答案段，则可以取消选中 " **显示简短答案** " 选项。 

该服务还会将确切答案的置信度分数返回为 **应答范围分数** ，你可以通过选择 "测试" 窗格中查询下方的 " **检查** " 选项来进行检查。

![托管应答范围分数](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>发布 QnA Maker 机器人

发布机器人时，默认情况下，你可以在应用程序中获得精确的应答启用体验，你会在其中看到简短答案以及答案段。 请参阅 API 参考，获取有关如何在响应中使用称为 AnswerSpan) 的确切答案 (获取 [答案](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) 。 用户通过机器人应用服务更新模板可以灵活选择其他体验。 

## <a name="language-support"></a>语言支持

目前仅支持英语的确切答案功能。