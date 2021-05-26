---
title: Azure 资源 - QnA Maker
description: QnA Maker 使用多个 Azure 源，其中每个资源具有不同的用途。 了解了如何单独使用每个资源后，便可以规划和选择正确的定价层，或者知道何时要更改定价层。 了解如何将资源组合起来使用可以发现问题并解决出现的问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e69b39415ea90deb6ce4477569d372f9bd8f2134
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110368632"
---
# <a name="azure-resources-for-qna-maker"></a>用于 QnA Maker 的 Azure 资源

QnA Maker 使用多个 Azure 源，其中每个资源具有不同的用途。 了解了如何单独使用每个资源后，便可以规划和选择正确的定价层，或者知道何时要更改定价层。 了解如何将资源组合起来使用可以发现问题并解决出现的问题。

## <a name="resource-planning"></a>资源规划

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

当你首次开发 QnA Maker 知识库时，在原型设计阶段，通常会将单个 QnA Maker 资源用于测试和生产。

在进入项目的开发阶段时，应该考虑到：

* 你的知识库系统将包含多少种语言？
* 需要在多少个区域中提供你的知识库？
* 系统将包含每个领域中的多少个文档？

规划在单个 QnA Maker 资源中包含具有相同语言、相同区域和相同主题领域组合的所有知识库。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

当你首次开发知识库时，在原型设计阶段，通常会将单个资源同时用于测试和生产。

在进入项目的开发阶段时，应该考虑到：

* 你的知识库系统将包含多少种语言？
* 需要在多少个区域中提供你的知识库？
* 系统将包含每个领域中的多少个文档？

---

## <a name="pricing-tier-considerations"></a>定价层注意事项

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

通常，你需要考虑三个参数：

* **需要的服务吞吐量**：
    * 根据需要为应用服务选择合适的[应用计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。 可以[纵向扩展](../../../app-service/manage-scale-up.md)或收缩应用。
    * 这应当也会影响你的 Azure **认知搜索** SKU 选择，请参阅 [此处](../../../search/search-sku-tier.md)的更多详细信息。 此外，你可能需要用副本来调整认知搜索的[容量](../../../search/search-capacity-planning.md)。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，你会根据不同主题领域的数量来确定所需的知识库数量。 一个主题域（适用于单一语言）应位于一个知识库中。

你的 Azure 搜索服务资源必须创建于 2019 年 1 月之后，且不能位于免费（共享）层。 当前不支持在 Azure 门户中配置客户管理的密钥。

> [!IMPORTANT]
> 可以在特定的层中发布 N-1 个知识库，其中，N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。

* **源文档的数量**：QnA Maker 管理服务的免费 SKU 将可以通过门户和 API 管理的文档数限制为 3（每个文档的大小限制为 1 MB）。 标准 SKU 对于可以管理的文档数没有限制。 有关更多详细信息，请参阅[此处](https://aka.ms/qnamaker-pricing)。

下表提供了一些概要准则。

|                            | QnA Maker 管理 | 应用服务 | Azure 认知搜索 | 限制                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **试验**        | 免费 SKU             | 免费层   | 免费层    | 分别最多发布 2 KB、50 MB 大小  |
| **开发/测试环境**   | 标准 SKU         | 共享      | 基本        | 分别最多发布 14 KB、2 GB 大小    |
| **生产环境** | 标准 SKU         | 基本       | Standard     | 分别最多发布 49 KB、25 GB 大小 |

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

通常，你需要考虑三个参数：

* 你需要的吞吐量：
    * 自定义问答（预览版）是一项免费功能，对于管理 API 和预测 API，目前的吞吐量上限为 10 TPS。
    * 这应当也会影响你的 Azure **认知搜索** SKU 选择，请参阅 [此处](../../../search/search-sku-tier.md)的更多详细信息。 此外，你可能需要用副本来调整认知搜索的[容量](../../../search/search-capacity-planning.md)。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，你会根据不同主题领域的数量来确定所需的知识库数量。 一个主题域（适用于单一语言）应位于一个知识库中。

    使用自定义问答（预览版）时，你可以选择以一种或多种语言为知识库设置文本分析服务。 在自定义问答（预览版）功能中创建第一个知识库时可以做出此选择。

> [!div class="mx-imgBorder"]
> ![多语言知识库选择](../media/qnamaker-create-publish-knowledge-base/select-language-custom-qna.png)

> [!IMPORTANT]
> 在特定的层中，可以发布单种语言的 N-1 个知识库或者不同语言的 N/2 个知识库，其中 N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

例如，如果你的层允许 15 个索引，则你可以发布同一语言的 14 个知识库（发布的每个知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。 如果你选择拥有不同语言的知识库，则只能发布 7 个知识库。

* 用作源的文档数：自定义问答（预览版）是一项免费功能，对于可添加为源的文档数没有限制。 有关更多详细信息，请参阅[此处](https://aka.ms/qnamaker-pricing)。

下表提供了一些概要准则。

|                            |Azure 认知搜索 | 限制                      |
| -------------------------- |------------ | -------------------------------- |
| **试验**        |免费层    | 分别最多发布 2 KB、50 MB 大小  |
| **开发/测试环境**   |基本        | 分别最多发布 14 KB、2 GB 大小    |
| **生产环境** |Standard     | 分别最多发布 49 KB、25 GB 大小 |

---

## <a name="recommended-settings"></a>建议的设置

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

|目标 QPS | 应用服务 | Azure 认知搜索 |
| -------------------- | ----------- | ------------ |
| 3             | S1，1 个副本   | S1，1 个副本    |
| 50         | S3，10 个副本       | S1，12 个副本         |
| 80         | S3，10 个副本      |  S3，12 个副本  |
| 100         | P3V2，10 个副本  | S3，12 个副本，3 个分区   |
| 200 到 250         | P3V2，20 个副本 | S3，12 个副本，3 个分区    |

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

自定义问答（预览版）是一项免费功能。对于管理 API 和预测 API，目前的吞吐量上限都是每秒 10 个事务。 若要以每秒 10 个事务作为服务的目标，我们建议使用 Azure 认知搜索的 S1（1 个实例）SKU。

---

## <a name="when-to-change-a-pricing-tier"></a>何时更改定价层

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

|升级|原因|
|--|--|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker 管理 SKU|你想要在知识库中包含更多的 QnA 对或文档源。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)应用服务 SKU，检查认知搜索层并[创建认知搜索副本](../../../search/search-capacity-planning.md)|你的知识库需要为来自客户端应用（例如聊天机器人）的更多请求提供服务。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure 认知搜索服务|你打算拥有许多知识库。|

通过[在 Azure 门户中更新应用服务](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)来获取最新的运行时更新。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

当你打算使用许多知识库时[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure 认知搜索服务。

---

## <a name="keys-in-qna-maker"></a>QnA Maker 中的密钥

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 服务处理两种类型的密钥：与应用服务中承载的运行时配合使用的 **创作密钥** 和 **查询终结点密钥**。

通过 API 向服务发出请求时使用这些密钥。

![密钥管理](../media/authoring-key.png)

|名称|位置|用途|
|--|--|--|
|创作/订阅密钥|[Azure 门户](https://azure.microsoft.com/free/cognitive-services/)|这些密钥用来访问 [QnA Maker 管理服务 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)。 这些 API 可让你编辑知识库中的问题和答案，以及发布知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>可以在“密钥和终结点”页上的“认知服务”资源中找到这些密钥。 |
|查询终结点密钥|[QnA Maker 门户](https://www.qnamaker.ai)|这些密钥用于查询已发布的知识库终结点，以获取对用户问题的响应。 通常，你会在连接到 QnA Maker 服务的聊天机器人或客户端应用程序代码中使用此查询终结点。 这些密钥是你发布 QnA Maker 知识库时创建的。<br><br>可以在“服务设置”页中找到这些密钥。 在页面右上方的下拉菜单上的用户菜单中找到该页。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 门户中查找创作密钥

可以从你在其中创建了 QnA Maker 资源的 Azure 门户中查看和重置创作密钥。

1. 转到 Azure 门户中的 QnA Maker 资源，选择具有“认知服务”类型的资源：

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 转到“密钥和终结点”：

    ![QnA Maker 托管（预览版）订阅密钥](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 门户中查找查询终结点密钥

终结点与资源位于同一区域，因为终结点密钥用于调用知识库。

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到 [QnA Maker 门户](https://qnamaker.ai)，转到你的个人资料，然后选择“服务设置”。

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置你的密钥：

    > [!div class="mx-imgBorder"]
    > ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为你的密钥已泄露，请刷新密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

自定义问答（预览版）功能处理两种类型的密钥：创作密钥和 Azure 认知搜索密钥，用于访问客户订阅中的服务。

通过 API 向服务发出请求时使用这些密钥。

> [!div class="mx-imgBorder"]
> ![密钥管理托管预览版](../media/qnamaker-how-to-key-management/custom-question-answering-key-management.png)

|名称|位置|用途|
|--|--|--|
|创作/订阅密钥|[Azure 门户](https://azure.microsoft.com/free/cognitive-services/)|这些密钥用来访问 [QnA Maker 管理服务 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)。 这些 API 可让你编辑知识库中的问题和答案，以及发布知识库。 这些密钥是在创建新服务时创建的。<br><br>可以在“密钥和终结点”页上的“认知服务”资源中找到这些密钥。 |
|Azure 认知搜索管理密钥|[Azure 门户](../../../search/search-security-api-keys.md)|这些密钥用来与用户的 Azure 订阅中部署的 Azure 认知搜索服务通信。 将 Azure 认知搜索与自定义问答（预览版）功能关联时，会自动将管理密钥传递到 QnA Maker 服务。 <br><br>可以在“密钥”页上的“Azure 认知搜索”资源中找到这些密钥。 |

### <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 门户中查找创作密钥

你可以从 Azure 门户查看和重置创作密钥。在该门户的文本分析资源中，你已添加了自定义问答（预览版）功能。

1. 转到 Azure 门户中的文本分析资源，选择具有“认知服务”类型的资源：

> [!div class="mx-imgBorder"]
> ![自定义问答（预览版）资源列表](../media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. 转到“密钥和终结点”：

> [!div class="mx-imgBorder"]
> ![自定义问答（预览版）订阅密钥](../media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="update-the-resources"></a>更新资源

了解如何升级知识库使用的资源。 自定义问答（预览版）功能在预览期间免费。 

---

## <a name="management-service-region"></a>管理服务区域

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 的管理服务仅用于 QnA Maker 门户和初始数据处理。 此服务仅在“美国西部”区域中可用。 不会将任何客户数据存储在此美国西部服务中。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

在自定义问答（预览版）功能中，管理服务和预测服务共置于同一区域。 目前，可在“美国中南部”、“北欧”和“澳大利亚东部”区域使用自定义问答（预览版）。

---

## <a name="resource-naming-considerations"></a>资源命名注意事项

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 资源的名称（如 `qna-westus-f0-b` ）也用于命名其他资源。

在 Azure 门户的创建窗口中，可以创建 QnA Maker 资源并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> ![用于创建 QnA Maker 资源的 Azure 门户的屏幕截图](../media/concept-azure-resource/create-blade.png)

创建资源后，它们的名称是相同的，但可选的 Application Insights 资源除外，其名称的后面附加了其他字符。

> [!div class="mx-imgBorder"]
> ![Azure 门户资源列表的屏幕截图](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 创建 QnA Maker 资源时请创建一个新资源组。 这样，在按资源组进行搜索时，便可以查看与 QnA Maker 资源关联的所有资源。

> [!TIP]
> 使用命名约定在资源或资源组的名称中指示定价层。 如果在创建新知识库或添加新文档时收到错误，常见的原因是认知搜索定价层存在限制。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

文本分析资源的资源名称（例如 `qna-westus-f0-b`）还用于命名其他资源。

在 Azure 门户的创建窗口中，可以创建文本分析资源并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> ![用于创建 QnA Maker 托管（预览版）资源的 Azure 门户的屏幕截图](../media/qnamaker-how-to-setup-service/custom-qna-create-button.png) 创建资源后，它们的名称是相同的。

> [!div class="mx-imgBorder"]
> ![Azure 门户中 QnA Maker 托管（预览版）资源列表的屏幕截图](../media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

> [!TIP]
> 创建文本分析资源时请创建一个新资源组。 这样，在按资源组进行搜索时，便可以查看与文本分析资源关联的所有资源。

> [!TIP]
> 使用命名约定在资源或资源组的名称中指示定价层。 如果在创建新知识库或添加新文档时收到错误，常见的原因是认知搜索定价层存在限制。

---

## <a name="resource-purposes"></a>资源用途

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

使用 QnA Maker 创建的每个 Azure 资源都有特定的用途：

* QnA Maker 资源
* 认知搜索资源
* 应用服务
* 应用计划服务
* Application Insights 服务

### <a name="qna-maker-resource"></a>QnA Maker 资源

QnA Maker 资源提供对创作和发布 API 的访问，以及在运行时提供对 QnA 对的基于自然语言处理 (NLP) 的第二排名层 (ranker #2) 的访问。

第二排名应用智能筛选器，这些筛选器可以包含元数据和跟进提示。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 资源配置设置

在 [QnA Maker 门户](https://qnamaker.ai)中创建新知识库时，在资源级别应用的唯一设置是“语言”设置。 你将在为资源创建第一个知识库时选择语言。

### <a name="cognitive-search-resource"></a>认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 对
* 在运行时提供 QnA 对的初始排名 (ranker #1)

#### <a name="index-usage"></a>索引使用情况

资源保留一个索引充当测试索引，剩余的每个索引关联到一个已发布的知识库。

按照包含 15 个索引这一条件定价的资源将包含 14 个已发布的知识库，还有一个索引用于测试所有知识库。 此测试索引按知识库分区，这样，使用交互式测试窗格的查询将使用测试索引，但只会从与特定知识库关联的特定分区返回结果。

#### <a name="language-usage"></a>语言的使用

在 QnA Maker 资源中创建的第一个知识库用于确定认知搜索资源及其所有索引的单个语言集。 对于一个 QnA Maker 服务，只能使用一个语言集。

#### <a name="using-a-single-cognitive-search-service"></a>使用单个认知搜索服务

如果你通过门户创建 QnA 服务及其依赖项（如搜索），则系统会为你创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用先前存在的搜索服务，并删除刚刚创建的搜索服务。

了解[如何配置](../How-To/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker，以使用其他认知服务资源，而不是在创建 QnA Maker 资源过程中创建的认知服务资源。

### <a name="app-service-and-app-service-plan"></a>应用服务和应用服务计划

客户端应用程序使用[应用服务](../../../app-service/index.yml)通过运行时终结点访问已发布的知识库。

若要查询已发布的知识库，所有已发布的知识库需使用相同的 URL 终结点，但要在路由中指定 **知识库 ID**。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) 用于收集聊天日志和遥测数据。 有关服务的信息，请查看常见的 [Kusto 查询](../how-to/get-analytics-knowledge-base.md)。

### <a name="share-services-with-qna-maker"></a>与 QnA Maker 共享服务

QnA Maker 创建多个 Azure 资源。 若要减少管理工作量并从成本分担中获益，请参考下表来了解可以和不可以共享的服务：

|服务|共享|原因|
|--|--|--|
|认知服务|X|在设计上不可行|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果共享同一应用服务计划的其他应用使用了大量磁盘空间，QnAMaker 应用服务实例将遇到问题。|
|应用服务|X|在设计上不可行|
|Application Insights|✔|可以共享|
|搜索服务|✔|1. `testkb` 是 QnAMaker 服务的保留名称；不能由其他服务使用。<br>2. 名称的同义词映射 `synonym-map` 为 QnAMaker 服务保留。<br>3. 已发布的知识库数受搜索服务层级限制。 如果有可用的索引，其他服务可以使用这些索引。|

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

使用自定义问答（预览版）功能创建的每个 Azure 资源都有特定用途：

* 文本分析资源
* 认知搜索资源

### <a name="text-analytics-resource"></a>文本分析资源

使用自定义问答（预览版）功能的文本分析资源提供了对创作和发布 API 的访问，托管着排名运行时并提供遥测。

### <a name="azure-cognitive-search-resource"></a>Azure 认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 对
* 在运行时提供 QnA 对的初始排名 (ranker #1)

#### <a name="index-usage"></a>索引使用情况

在特定的层中，可以发布单种语言的 N-1 个知识库或者不同语言的 N/2 个知识库，其中 N 是 Azure 认知搜索层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

例如，如果你的层允许 15 个索引，则你可以发布同一语言的 14 个知识库（发布的每个知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。 如果你选择拥有不同语言的知识库，则只能发布 7 个知识库。

#### <a name="language-usage"></a>语言的使用

使用自定义问答（预览版）时，你可以选择以一种或多种语言为知识库设置你的服务。 在文本分析服务中，你可以在创建第一个知识库期间做出此选择。 请参阅[此文](#pricing-tier-considerations)了解如何启用每个知识库的语言设置。

---

## <a name="next-steps"></a>后续步骤

* 了解 QnA Maker [知识库](../How-To/manage-knowledge-bases.md)
* 了解[知识库生命周期](development-lifecycle-knowledge-base.md)
* 查看服务和知识库的[限制](../limits.md)
