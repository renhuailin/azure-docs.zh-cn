---
title: 迁移知识库 - QnA Maker
description: 迁移知识库需要从一个知识库中导出然后导入另一个知识库。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ea0a02366f2c2d2c3fd656d9a6dbce111d632422
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121213"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

出于以下几个原因，建议创建知识库的副本：

* 将知识库从 QnA Maker GA 复制到自定义问答 
* 实现备份和还原过程 
* 与 CI/CD 管道集成 
* 当希望将数据移动到不同区域时

## <a name="prerequisites"></a>先决条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

> * 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中创建的 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。
> * 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

> * 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中启用了自定义问答功能的[文本分析资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅和文本分析资源名称。
> * 设置[自定义问答](../How-To/set-up-qnamaker-service-azure.md)

---

## <a name="export-a-knowledge-base"></a>导入知识库
1. 登录 [QnA Maker 门户](https://qnamaker.ai)。
1. 选择要迁移的知识库。

1. 在“设置”页面上，可以选择导出“QnA”、“同义词”或“知识库副本”选项   。 可以选择下载 .tsv/.xlsx 中的数据。

   1. **QnA**：导出 QnA 时，将下载所有 QnA 对（包括问题、答案、元数据、跟进提示和数据源名称）。 随问题和答案一起导出的 QnA ID 可用于通过[更新 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) 更新特定的 QnA 对。 在多个导出操作中，特定 QnA 对的 QnA ID 保持不变。
   2. **同义词**：可以导出已添加到知识库中的同义词。
   4. **知识库副本**：如果要下载包含同义词和其他设置的整个知识库，可以选择此选项。

## <a name="import-a-knowledge-base"></a>导入知识库
1. 单击 qnamaker.ai 门户顶部菜单中的“创建知识库”，然后通过不添加任何 URL 或文件来创建一个空知识库。 为新知识库设置所选名称，然后单击“创建知识库” **** 。 

1. 在此新知识库中，打开“设置”，然后在“导入知识库”下选择以下选项之一：“QnA”、“同义词”或“知识库副本”  。 

   1. **QnA**：此选项会导入所有 QnA 对。 新知识库中创建的 QnA 对的 QnA ID 应该与已导出文件中存在的 QnA ID 相同。 可以参考 [SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas)、[SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv) 以导入 QnA。
   2. **同义词**：此选项可用于将同义词导入知识库。 可以参考 [SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms)、[SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv) 以导入同义词。
   3. **知识库副本**：此选项可用于导入具有“QnA”、“同义词”和“设置”的知识库副本。 可以参考 [KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica)、[KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv) 以了解更多详细信息。 如果还要将非结构化内容添加到副本中，请参阅 [CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica)。

      导入副本时需要 QnA 或非结构化内容。 非结构化文档仅对自定义问答有效。
      导入副本时，同义词文件不是必需的。
      导入副本时，设置文件是必需的。

         |设置|导入到 QnA Maker 知识库时是否允许更新？|导入到自定义问答知识库时是否允许更新？|
         |:--|--|--|
         |DefaultAnswerForKB|否|是|
         |EnableActiveLearning (True/False)|是|否|
         |EnableMultiTurnExtraction (True/False)|是|是|
         |DefaultAnswerforMultiturn|是|是|
         |语言|否|否|

1. 使用“测试”面板测试新知识库。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

1. 发布知识库并创建聊天机器人。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

   > [!div class="mx-imgBorder"]
   > ![迁移知识库](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以编程方式从 QnA Maker 迁移知识库

可通过以下 REST API 以编程方式使用迁移过程：

**导出**

* [下载知识库 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**导入**

* [替换 API（使用同一知识库 ID 进行重载）](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [创建 API（使用新的知识库 ID 进行加载）](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>聊天日志
无法迁移聊天日志，因为新知识库使用 Application Insights 存储聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
