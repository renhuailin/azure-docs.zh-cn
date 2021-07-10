---
title: 教程：使用 Azure 机器人服务为多个域创建 FAQ 机器人
description: 在本教程中，你将使用 QnA Maker 和 Azure 机器人服务为生产用例创建一个无代码 FAQ 机器人。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.author: diagarw
ms.date: 03/31/2021
ms.openlocfilehash: d79eed22d441949810cfc1738f3af2c0f8703adc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116417"
---
# <a name="add-multiple-domains-to-your-faq-bot"></a>向 FAQ 机器人添加多个域

构建 FAQ 机器人时，可能会遇到需要跨多个域处理查询的用例。 假设 Microsoft 的营销团队想要构建一个客户支持机器人，该机器人可以回答用户针对多个 Surface 产品的常见查询。 为了简单起见，我们将使用 [Surface 触笔](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)和 [Surface Earbuds](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9) 上的 FAQ URL 创建知识库。

可以按照以下方式，设计一个使用 QnA Maker 处理跨多个域的查询的机器人：

* 创建单个知识库并使用元数据将 QnA 对标记到不同的域中。
* 为每个域创建单独的知识库。
* 为每个域创建单独的 QnA Maker 资源。

## <a name="create-a-single-knowledge-base-and-tag-qna-pairs-into-distinct-domains-with-metadata"></a>创建单个知识库并使用元数据将 QnA 对标记到不同的域中。

内容作者可以使用文档提取 QnA 或将自定义 QnA 添加到知识库。 为了将这些 QnA 分组到特定的域或类别中，可以将[元数据](../How-To/query-knowledge-base-with-metadata.md)添加到 QnA 对。

对于 Surface 产品上的机器人，可以按照以下步骤创建一个机器人来回答关于这两种产品类型的查询：

1. 在“创建 KB”页的步骤 3 中添加有关 Surface 产品的以下 FAQ URL，并单击“创建 KB”。 从这些源提取 QnA 对后，系统会创建一个新的知识库。 
   
   [Surface 触笔 FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)<br>[Surface Earbuds FAQ](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9)
 
2. 创建 KB 后，可以转到“视图选项”，然后单击“显示元数据” 。 这会打开 QnA 的元数据列。

   >[!div class="mx-imgBorder"]
   >[![显示元数据]( ../media/qnamaker-tutorial-updates/show-metadata.png)]( ../media/qnamaker-tutorial-updates/expand/show-metadata.png#lightbox)


3. 在此知识库中，我们拥有关于这两个产品的 QnA，并且希望将它们区分开来，以便在给定产品的 QnA 中搜索答复。 为此，我们应该相应地更新 QnA 对的元数据字段。 

   如以下示例所示，我们添加了一个元数据，其中 product 作为键，surface_pen 或 surface_earbuds 作为值（如果适用）  。 可以扩展此示例，以提取多个产品的数据，并为每个产品添加不同的值。

   >[!div class="mx-imgBorder"]
   >[![元数据]( ../media/qnamaker-tutorial-updates/metadata-example-2.png)]( ../media/qnamaker-tutorial-updates/expand/metadata-example-2.png#lightbox)

4. 现在，为了限制系统搜索特定产品的响应，需要在生成 Answer API 中将该产品作为严格的筛选器传递。

    了解[如何使用 GenerateAnswer API](../How-To/metadata-generateanswer-usage.md)。 GenerateAnswer URL 采用以下格式：
    ```
    https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
    ```

    在 API 调用的 JSON 正文中，我们已将 surface_pen 作为元数据“product”的值传递 。 因此，系统将只查找具有相同元数据的 QnA 对中的响应。 

    ```json
    {
        "question": "What is the price?",
        "top": 6,
        "isTest": true,
        "scoreThreshold": 30,
        "rankerType": ""  // values: QuestionOnly
        "strictFilters": [
        {
            "name": "product",
            "value": "surface_pen"
        }],
        "userId": "sd53lsY="
    }
    ```

    可按照以下方式根据用户输入获取元数据值： 

    * 通过机器人客户端将域明确用作用户的输入。 如以下实例所示，发起对话时，可以将产品类别作为用户输入。

      ![获取元数据输入](../media/qnamaker-tutorial-updates/expand/explicit-metadata-input.png)

    * 基于机器人上下文隐式识别域。 例如，如果上一个问题与特定的 Surface 产品有关，则客户端可以将其保存为上下文。 如果用户未在下一个查询中指定产品，则可以将机器人上下文作为元数据传递给生成答案 API。

      ![传递上下文]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-context.png)

    * 从用户查询中提取实体，以识别用于元数据筛选的域。 可以将文本分析和 LUIS 等其他认知服务用于实体提取。

      ![从查询中提取元数据]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-query.png)

### <a name="how-large-can-our-knowledge-bases-be"></a>我们的知识库能有多大？ 

最多可向单个知识库添加 50000 个 QnA 对。 如果数据超过 50,000 个 QnA 对，则应该考虑拆分知识库。

## <a name="create-a-separate-knowledge-base-for-each-domain"></a>为每个域创建单独的知识库

你还可以为每个域创建一个单独的知识库，并分别维护这些知识库。 所有 API 都要求用户传递知识库 ID，以便对知识库进行任何更新或提取用户问题的答案。  

服务接收到用户问题时，你需要传递上面所示的“生成答案”终结点中的 KB ID，以便从相关知识库提取响应。 你可以在“发布”页部分中找到知识库 ID，如下所示。

>[!div class="mx-imgBorder"]
>![提取 KB ID](../media/qnamaker-tutorial-updates/fetch-kb-id.png)

## <a name="create-a-separate-qna-maker-resource-for-each-domain"></a>为每个域创建单独的 QnA Maker 资源。

假设 Microsoft 的营销团队想要构建一个客户支持机器人，该机器人可以回答用户针对 Surface 和 Xbox 产品的常见查询。 他们计划安排不同的团队访问 Surface 和 Xbox 上的知识库。 在这种情况下，建议创建两个 QnA Maker 资源：一个用于 Surface，另一个用于 Xbox。 但可以为访问相同资源的用户定义不同的角色。 阅读有关[基于角色的访问控制](../How-To/manage-qna-maker-app.md)的更多内容。 
