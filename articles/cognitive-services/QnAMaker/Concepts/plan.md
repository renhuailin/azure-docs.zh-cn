---
title: 规划应用 - QnA Maker
description: 了解如何规划 QnA Maker 应用。 了解 QnA Maker 如何工作并与其他 Azure 服务交互，以及一些知识库概念。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 1f3db34f477e228157cfa8378f171adf7a239811
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828838"
---
# <a name="plan-your-qna-maker-app"></a>规划 QnA Maker 应用

若要规划 QnA Maker 应用，需要了解 QnA Maker 如何工作并与其他 Azure 服务交互。 还应扎实地掌握知识库概念。

## <a name="azure-resources"></a>Azure 资源

使用 QnA Maker 创建的每个 [Azure 资源](azure-resources.md#resource-purposes)都具有特定用途。 每个资源都有其自己的用途、限制和[定价层](azure-resources.md#pricing-tier-considerations)。 了解这些资源的功能非常重要，这样便可以在规划过程中使用该知识。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

| 资源 | 目的 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) 资源 | 创作和查询预测 |
| [认知搜索](azure-resources.md#cognitive-search-resource)资源 | 数据存储和搜索 |
| [应用服务资源和应用计划服务](azure-resources.md#app-service-and-app-service-plan)资源 | 查询预测终结点 |
| [Application Insights](azure-resources.md#application-insights) 资源 | 查询预测遥测 |


# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

| 资源 | 目的 |
|--|--|
| [文本分析](azure-resources.md#qna-maker-resource)资源 | 创作、查询预测终结点和遥测|
| [认知搜索](azure-resources.md#cognitive-search-resource)资源 | 数据存储和搜索 |

---
### <a name="resource-planning"></a>资源规划

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

每个资源的免费层 `F0` 都有效，可以提供创作和查询预测体验。 可以使用此层了解创作和查询预测。 转到生产或实时方案时，请重新评估资源选择。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

自定义问答（预览版）是一项免费功能。对于管理 API 和预测 API，目前的吞吐量上限都是每秒 10 个事务。 若要以每秒 10 个事务作为服务的目标，建议使用 Azure 认知搜索的 S1（1 个实例）SKU。

### <a name="text-analytics-resource"></a>文本分析资源

启用了自定义问答功能的单个文本分析资源可以托管多个知识库。 知识库的数量由认知搜索定价层支持的索引数量确定。 详细了解[索引与知识库的关系](azure-resources.md#index-usage)。

---

### <a name="knowledge-base-size-and-throughput"></a>知识库大小和吞吐量

生成实际应用时，需针对知识库大小和预期查询预测请求规划足够的资源。

知识库大小由以下各项控制：
* [认知搜索资源](../../../search/search-limits-quotas-capacity.md)定价层限制
* [QnA Maker 限制](../limits.md)

知识库查询预测请求由 Web 应用计划和 Web 应用控制。 若要规划定价层，请参阅[建议的设置](azure-resources.md#recommended-settings)。

### <a name="resource-sharing"></a>资源共享

如果已使用其中一些资源，则可以考虑共享资源。 查看哪些资源[可以共享](azure-resources.md#share-services-with-qna-maker)，了解资源共享是一种高级方案。

在相同 QnA Maker 资源中创建的所有知识库会共享相同测试查询预测终结点。

### <a name="understand-the-impact-of-resource-selection"></a>了解资源选择的影响

适当的资源选择意味着知识库可成功回答查询预测。

如果知识库不能正常工作，那么通常是资源管理不当问题。

资源选择不当需要进行调查来确定[需要更改的资源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知识库

知识库直接与其 QnA Maker 资源关联。 它包含用于回答查询预测请求的问题和答案 (QnA) 对。

### <a name="language-considerations"></a>语言注意事项

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

在 QnA Maker 资源上创建的第一个知识库会为资源设置语言。 对于一个 QnA Maker 资源，只能使用一种语言。

可以按语言构建 QnA Maker 资源，也可以在将查询发送到查询预测终结点之前，使用[翻译器](../../translator/translator-overview.md)将查询从另一种语言更改为知识库语言。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

现在你可以在启用了自定义问答功能的同一文本分析资源中拥有不同语言的知识库。 创建第一个知识库时，可以选择是要使用一种语言还是多种语言的知识库资源。

![QnA Maker 托管（预览版）多语言知识库选择](../media/qnamaker-create-publish-knowledge-base/connect-knowledgebase-custom-qna.png)

> [!NOTE]
> 如果为每个知识库启用多种语言，则不能在文本分析资源中创建尽可能多的知识库。 了解[有关语言设置限制的更多详细信息](./azure-resources.md)。

---

### <a name="ingest-data-sources"></a>引入数据源

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

可以使用以下引入的[数据源](../Concepts/data-sources-and-content.md)之一来创建知识库：

* 公共 URL
* 专用 SharePoint URL
* 文件

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

自定义问答还支持非结构化内容。 你可以上传包含非结构化内容的文件。

目前我们不支持非结构化内容的 URL。

---

引入过程会将[支持的内容类型](../reference-document-format-guidelines.md)转换为 markdown。 对答案的所有进一步编辑都是通过 markdown 来完成。 创建知识库后，可以使用[格式文本创作](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)在 QnA Maker 门户中编辑 [QnA 对](question-answer-set.md)。

### <a name="data-format-considerations"></a>数据格式注意事项

由于 QnA 对的最终格式是 markdown，因此必须了解 [markdown 支持](../reference-markdown-format.md)。

必须从公共 URL 提供链接图像，才能在 QnA Maker 门户的测试窗格或客户端应用程序中显示这些图像。 QnA Maker 不提供内容身份验证（包括图像）。

### <a name="bot-personality"></a>机器人个性化

使用[chit-chat](../how-to/chit-chat-knowledge-base.md) 将机器人个性化添加到知识库。 这种个性化是通过采用特定对话音调（如专业和友好 ）提供的答案来实现的。 此 chit-chat 作为对话集提供，你可以完全控制其添加、编辑和删除。

如果机器人连接到知识库，则建议使用机器人个性化。 即使还连接到其他服务，你也可以选择在知识库中使用 chit-chat，但应查看机器人服务如何进行交互，了解这是否是适合你使用的正确体系结构设计。

### <a name="conversation-flow-with-a-knowledge-base"></a>包含知识库的对话流

对话流通常以用户的问候语开头，例如 `Hi` 或 `Hello`。 知识库可以使用通用答案（如 `Hi, how can I help you`）进行回答，也可以提供一系列可供选择的跟进提示以继续进行对话。

你应在设计对话流时考虑循环，使用户知道如何使用你的机器人，而不会在对话中被机器人放弃。 [跟进提示](../how-to/multiturn-conversation.md)提供 QnA 对之间的链接，这样可以实现对话流。

### <a name="authoring-with-collaborators"></a>与协作者一起创作

协作者可以是共享知识库应用程序完整开发堆栈的其他开发者，也可以仅限于创作知识库。

知识库创作支持在 Azure 门户中应用的多个基于角色的访问权限，以限制协作者的能力范围。

## <a name="integration-with-client-applications"></a>与客户端应用程序集成

与客户端应用程序集成是通过向预测运行时终结点发送查询来完成。 查询通过 SDK 或基于 REST 的请求发送到特定知识库，以发送给 QnA Maker 的 Web 应用终结点。

若要正确地对客户端请求验证身份，客户端应用程序必须发送正确的凭据和知识库 ID。 如果使用 Azure 机器人服务，请在 Azure 门户中将这些设置配置为机器人配置的一部分。

### <a name="conversation-flow-in-a-client-application"></a>客户端应用程序中的对话流

客户端应用程序（如 Azure 机器人）中的对话流可能在与知识库交互前后需要相应功能。

客户端应用程序是否通过提供处理跟进提示的替代方法或包括 chit-chit 来支持对话流？ 如果是这样，请及早设计这些内容，并确保客户端应用程序查询可以由其他服务或是在发送到知识库时进行正确处理。

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>在 QnA Maker 与语言理解 (LUIS) 之间调度

客户端应用程序可以提供多种功能，其中只有一个功能由知识库回答。 其他功能仍然需要了解对话文本，并从中提取含义。

常见客户端应用程序体系结构是结合使用 QnA Maker 和[语言理解 (LUIS)](../../LUIS/what-is-luis.md)。 LUIS 可为任何查询（包括对其他服务的查询）提供文本分类和提取。 QnA Maker 通过知识库提供答案。

在这类[共享体系结构](../choose-natural-language-processing-service.md)方案中，两个服务之间的调度由 Bot Framework 中的[调度](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)工具来完成。

### <a name="active-learning-from-a-client-application"></a>来自客户端应用程序的主动学习

QnA Maker 使用主动学习，通过向答案提供替代问题建议来改进知识库。 客户端应用程序负责此[主动学习](../How-To/use-active-learning.md)的一部分。 通过对话提示，客户端应用程序可以确定知识库返回了对用户无用的答案，并且可以确定更好的答案。 客户端应用程序需要将该信息发送回知识库以提高预测质量。

### <a name="providing-a-default-answer"></a>提供默认答案

如果知识库找不到答案，则会返回默认答案。 此答案可在 QnA Maker 门户的“设置”页面上或 [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body) 中进行配置。

此默认答案与 Azure 机器人默认答案不同。 在 Azure 门户中，可作为配置设置的一部分来配置 Azure 机器人的默认答案。 在未满足分数阈值时，会返回该答案。

## <a name="prediction"></a>预测

预测是来自知识库的响应，它包含的信息不仅仅是答案。 若要获取查询预测响应，请使用 [GenerateAnswer API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>预测分数波动

分数可以基于多个因素而变化：

* 响应 [GenerateAnswer](query-knowledge-base.md) 时请求的答案数（使用 `top` 属性）
* 各种可用的替代问题
* 元数据筛选
* 发送到 `test` 或 `production` 知识库的查询

有一个[两阶段答案排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
- 认知搜索 - 第一次排名。 将允许的答案数设置得足够高，以便认知搜索可返回最佳答案，然后将其传入 QnA Maker 排名程序。
- QnA Maker - 第二次排名。 应用特征化和机器学习来确定最佳答案。

### <a name="service-updates"></a>服务更新

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

应用[最新运行时更新](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)以自动管理服务更新。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

在自定义问答（预览版）中，运行时由 QnA Maker 服务本身进行管理。 因此服务更新不适用。

---

### <a name="scaling-throughput-and-resiliency"></a>缩放、吞吐量和复原能力

缩放、吞吐量和复原能力取决于 [Azure 资源](../how-to/set-up-qnamaker-service-azure.md)、其定价层和任何周围的体系结构（如[流量管理器](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager)。

### <a name="analytics-with-application-insights"></a>使用 Application Insights 进行的分析

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

对知识库的所有查询都存储在 Application Insights 中。 使用我们[最常用的查询](../how-to/get-analytics-knowledge-base.md)来了解指标。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

对于自定义问答，遥测通过 [Azure Monitor 服务](../../../azure-monitor/index.yml)来提供。 使用我们[最常用的查询](../how-to/get-analytics-knowledge-base.md)来了解指标。


---

## <a name="development-lifecycle"></a>开发生命周期

知识库的[开发生命周期](development-lifecycle-knowledge-base.md)是持续的：编辑、测试和发布知识库。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 对的知识库开发

应基于客户端应用程序使用情况设计和开发 QnA 对。

每个对可以包含：
* 元数据 - 可在查询时进行筛选，以便可以通过有关数据的源、内容、格式和用途的附加信息来标记 QnA 对。
* 跟进提示 - 通过知识库帮助确定路径，使用户可到达正确的答案。
* 替代问题 - 重要的是允许进行搜索，以便从不同形式的问题与答案匹配。 [主动学习建议](../How-To/use-active-learning.md)转变为替代问题。

### <a name="devops-development"></a>DevOps 开发

开发要插入到 DevOps 管道中的知识库需要在批量测试过程中隔离知识库。

一个知识库与 QnA Maker 资源上的所有其他知识库共享认知搜索索引。 虽然知识库按分区进行隔离，但与已发布的知识库相比，共享索引可能会导致分数不同。

若要在 `test` 和 `production` 知识库上具有相同的分数，请将 QnA Maker 资源隔离到单个知识库中。 在此体系结构中，资源只需要生存时间与隔离的批量测试一样长即可。

## <a name="next-steps"></a>后续步骤

* [Azure 资源](../how-to/set-up-qnamaker-service-azure.md)
* [问答集](question-answer-set.md)