---
title: 使用答案范围检测的精确应答 - QnA Maker
description: 了解 QnA Maker 托管中提供的精确应答功能。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232182"
---
# <a name="precise-answering"></a>精确应答

通过 QnA Maker 托管（预览版）中引入的精确应答功能，你可以从任何用户查询的知识库中存在的整段最佳候选答案中获得精确的简短答案。 此功能在运行时使用深度学习模型，该模型能够理解用户查询的意图并从整段答案中检测精确的简短答案（如果简短答案作为事实出现在整段答案中）。 

默认情况下，此功能在“测试”窗格中处于启用状态，因此可以测试特定于方案的功能。 对于内容开发人员和最终用户来说，此功能极其有用。 现在，内容开发人员无需针对知识库中的每个事实手动策展特定的 QnA 对，最终用户无需浏览从服务返回的整段答案，就能找到回答用户查询的实际事实。 你可以[通过生成应答 API 提取答案](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api)。

## <a name="precise-answering-on-qna-maker-portal"></a>针对 QnA Maker 门户的精确应答

在 QnA Maker 门户中，打开“测试”窗格时，你将会在顶部看到“显示简短答案”选项。 默认情况下将选择此选项。 当你在“测试”窗格中输入查询时，如果整段答案中出现了一个简短答案，则你将看到一个简短答案和该整段答案。
 
![托管的已启用的测试窗格](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

如果只想在测试窗格中查看整段答案，则可以取消选择“显示简短答案”选项。 

该服务还会将准确应答的置信度分数返回为“答案范围分数”，对此你可以通过选择窗格中查询正下方的“检查”选项进行检查。

![托管答案范围分数](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>发布 QnA Maker 机器人

当你发布机器人时，默认你会在应用程序中获得启用了精确应答的体验，您会在其中看到简短答案以及整段答案。 有关[生成答案](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan)，请参阅 API 引用，以查看如何在响应中使用精确应答（称为 AnswerSpan）。 用户通过机器人应用服务更新模板可以灵活地选择其他体验。 

## <a name="language-support"></a>语言支持

目前精确应答功能仅支持英语。
