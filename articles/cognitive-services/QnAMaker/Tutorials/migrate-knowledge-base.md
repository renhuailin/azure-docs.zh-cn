---
title: 迁移知识库 - QnA Maker
description: 迁移知识库需要从一个知识库中导出然后导入另一个知识库。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 8e1cff5a82870c5491b262f5ced95f688d5ee4ac
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369503"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

迁移是基于现有知识库创建新知识库的过程。 你这样做可能有以下几个原因：

* 备份和还原过程
* CI/CD 管道
* 移动区域

迁移知识库需要将数据从一个现有知识库导出，然后导入另一个知识库。

> [!NOTE]
> 按照以下说明将现有知识库迁移到新的 QnA Maker 托管知识库（预览版）。

## <a name="prerequisites"></a>先决条件

> * 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

> * 在 Azure 门户中创建的 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。
> * 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

> * 在 Azure 门户中启用了自定义问答功能的[文本分析资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅和文本分析资源名称。
> * 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

---

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>从 QnA Maker 迁移知识库
1. 登录 [QnA Maker 门户](https://qnamaker.ai)。
1. 选择要迁移的知识库。

1. 在“设置”页上，选择“导出知识库”，以下载包含原始知识库的内容（问题、答案、元数据、跟进提示和从中提取这些内容的数据源名称）的 .tsv 文件。 随问题和答案一起导出的 QnA ID 可用于通过[更新 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) 更新特定的 QnA 对。 在多个导出操作中，特定 QnA 对的 QnA ID 保持不变。

1. 从顶部菜单选择“创建知识库”，然后创建一个空知识库。 它是空的，因为在创建它时不会添加任何 URL 或文件。 创建后，将在导入步骤中添加这些 URL 或文件。

    配置知识库。 仅设置新知识库名称。 支持重复的名称，也可以使用特殊字符。

    不要在步骤 4 中选择任何内容，因为导入文件时这些值会被覆盖。

1. 在“步骤 5”中，选择“创建”。

1. 在此新的知识库中，打开“设置”选项卡，然后选择“导入知识库”。 此操作会导入问题、答案、元数据和跟进提示，并保留从中提取这些内容的数据源名称。 新知识库中创建的 QnA 对的 QnA ID 应该与已导出文件中存在的 QnA ID 相同。 这有助于创建该知识库的精确副本。

   > [!div class="mx-imgBorder"]
   > [![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用“测试”面板测试新知识库。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

1. 发布知识库并创建聊天机器人。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以编程方式从 QnA Maker 迁移知识库

可通过以下 REST API 以编程方式使用迁移过程：

**导出**

* [下载知识库 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**导入**

* [替换 API（使用同一知识库 ID 进行重载）](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [创建 API（使用新的知识库 ID 进行加载）](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天日志和变更内容
区分大小写的替换词（同义词）不会自动导入。 使用 [V4 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) 可将变更内容移至新知识库中。

无法迁移聊天日志，因为新知识库使用 Application Insights 存储聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)