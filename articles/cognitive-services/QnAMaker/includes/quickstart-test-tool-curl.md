---
title: include 文件
description: include 文件
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: 2219eca3be83aa955d761b333c66c4ade3bd4999
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300283"
---
此基于 cURL 的快速入门详细介绍如何从知识库获取答案。

## <a name="prerequisites"></a>先决条件

* 必须具有：
    * 最新的 [cURL](https://curl.haxx.se/)。
    * 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

> * 在 Azure 门户中创建的 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

> * 在 Azure 门户中启用了自定义问答功能的[文本分析资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅和文本分析资源名称。

---

   * 在前一篇[快速入门](../Quickstarts/add-question-metadata-portal.md)中生成的已训练且已发布的知识库，包含问题和答案，并且已配置元数据和闲聊内容。


> [!NOTE]
> 准备好从知识库生成问题的答案后，必须[训练](../Quickstarts/create-publish-knowledge-base.md#save-and-train)并[发布](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)该知识库。 发布知识库后，“发布”页将显示 HTTP 请求设置以生成答案。 “cURL”选项卡将显示从命令行工具生成答案所需的设置。

## <a name="use-metadata-to-filter-answer"></a>使用元数据筛选答案

使用上一篇快速入门中的知识库基于元数据查询答案。

1. 在知识库的“设置”页中，选择“CURL”选项卡查看用于从知识库生成答案的示例 cURL 命令。 
1. 将该命令复制到可编辑环境（例如文本文件），以便可以编辑该命令。 按如下所示编辑问题，以便将 `service:qna_maker` 的元数据用作 QnA 对的筛选器。

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
   # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)
    
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H   "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    ---

    该问题只是一个单词 `size`，可以返回两个 QnA 对中的一个。 `strictFilters` 数组告知响应将缩减为仅包含 `qna_maker` 回答。

1. 该响应仅包含符合筛选条件的回答。 为便于阅读，以下 cURL 响应已设置格式：

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    如果某对问答不符合搜索词但符合筛选器，将不会返回该对问答。 而是返回常规回答 `No good match found in KB.`。

## <a name="use-debug-query-property"></a>使用调试查询属性
> [!NOTE]
>建议不要将 Debug 属性用于任何依赖项。 添加此属性是为了帮助产品团队进行故障排除。

调试信息可帮助你了解返回的答案是如何确定的。 尽管此信息非常有用，但不是必需的。 若要生成附带调试信息的答案，请添加 `debug` 属性：

```json
Debug: {Enable:true}
```

1. 编辑 cURL 命令，以包含调试属性来查看更多信息。

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
   # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    ---

1. 响应包括有关答案的相关信息。 在以下 JSON 输出中，为简洁起见，某些调试详细信息已替换为省略号。

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>使用测试知识库

若要从测试知识库获取答案，请使用 `isTest` 正文属性。

此属性是一个布尔值。

```json
isTest:true
```

cURL 命令如下所示：
# <a name="qna-maker-ga"></a>[QnA Maker GA](#tab/v1)
```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```
# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)
```bash
curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

---
JSON 响应使用与已发布的知识库查询相同的架构。

> [!NOTE]
> 即使测试知识库和已发布的知识库看上去完全相同，也仍可能存在一些细微的差异，因为测试索引在资源中的所有知识库之间共享。

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>使用 cURL 查询聊天式解答

1. 在支持 cURL 的终端中，使用用户提供的机器人聊天结束语句，例如 `Thank you`。 无需设置任何其他属性。
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
   # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    ---

1. 运行 cURL 命令，然后接收包含评分和解答的 JSON 响应。

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    由于问题 `Thank you` 与聊天问题完全匹配，因此分数为 100，表明 QnA Maker 完全确信其答案。 QnA Maker 还返回了包含聊天元数据标记信息的所有相关问题和元数据属性。

## <a name="use-threshold-and-default-answer"></a>使用阈值和默认答案

可以请求答案的最小阈值。 如果不满足阈值，将返回默认答案。

1. 添加 `threshold` 属性，以请求提供阈值为 80% 或更佳的 `size` 答案。 知识库应该不会查找该答案，因为问题的评分为 71%。 结果将返回在创建知识库时提供的默认答案。

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
   # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    ---

1. 运行 cURL 命令并接收 JSON 响应。

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker 返回的评分为 `0`，表示没有置信度。 它同时返回了默认解答。

1. 将阈值更改为 60%，并再次请求查询：
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
   # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    ---

    返回的 JSON 找到了答案。

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../Concepts/azure-resources.md) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
## <a name="use-unstructured-data-sources"></a>使用非结构化数据源
    
我们现在支持添加无法用于提取 QnA 的非结构化文档。用户可以在提取查询响应时，选择在 GenerateAnswer API 中包括或排除非结构化数据集。
     
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)
我们不支持在 GA 服务中使用非结构化数据集。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

1. 如果要在评估对“生成答案 API”的响应时包含非结构化数据源，请将参数 includeUnstructuredResources 设置为 true，反之亦然。

    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question': 'what is Surface Headphones 2+ priced at?', 'includeUnstructuredSources':true,'top': 2}"
    ```

2. 响应包括解答源。 
    
    ```json
    {
       "answers": [{
               "questions": [],
               "answer": "Surface Headphones 2+ is priced at $299.99 USD. Business and education customers in select markets can place orders today through microsoft.com\n\nor their local authorized reseller.\n\nMicrosoft Modern USB and Wireless Headsets:\n\nCertified for Microsoft Teams, these Microsoft Modern headsets enable greater focus and call privacy, especially in shared workspaces.",
               "score": 82.11,
               "id": 0,
               "source": "blogs-introducing-surface-laptop-4-and-new-access.pdf",
               "isDocumentText": false,
               "metadata": [],
               "answerSpan": {
                   "text": "$299.99 USD",
                   "score": 0.0,
                   "startIndex": 34,
                   "endIndex": 45
               }
           },
           {
               "questions": [],
               "answer": "Now certified for Microsoft Teams with the included dongle, Surface Headphones 2+ provides an even more robust meeting experience with on‐ear Teams controls and improved remote calling. Surface Headphones 2+ is priced at $299.99 USD. Business and education customers in select markets can place orders today through microsoft.com\n\nor their local authorized reseller.",
               "score": 81.95,
               "id": 0,
               "source": "blogs-introducing-surface-laptop-4-and-new-access.pdf",
               "isDocumentText": false,
               "metadata": []
           }
       ],
       "activeLearningEnabled": true
   }
    ```
---
