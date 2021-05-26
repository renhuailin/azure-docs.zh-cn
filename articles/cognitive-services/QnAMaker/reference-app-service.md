---
title: 服务配置 - QnA Maker
description: 了解配置资源的方式和位置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: 7a24045d38b594e09b931a885c60851109a108e3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372681"
---
# <a name="service-configuration"></a>服务配置

每个版本的 QnA Maker 使用一组不同的 Azure 资源（服务）。 本文介绍了这些服务支持的自定义项。 

## <a name="app-service"></a>应用服务

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 使用应用服务提供 [generateAnswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer) 使用的查询运行时。

这些设置在 Azure 门户的应用服务中可用。 可以通过依次选择“设置”、“配置”来使用这些设置 。

可以通过“应用程序设置”列表来设置单个设置，也可以通过选择“高级编辑”来修改多个设置。

|资源|设置|
|--|--|
|AzureSearchAdminKey|认知搜索 - 用于 QnA 对存储和 Ranker #1|
|AzureSearchName|认知搜索 - 用于 QnA 对存储和 Ranker #1|
|DefaultAnswer|找不到匹配项时的应答文本|
|UserAppInsightsAppId|聊天日志和遥测|
|UserAppInsightsKey|聊天日志和遥测|
|UserAppInsightsName|聊天日志和遥测|
|QNAMAKER_EXTENSION_VERSION|始终设置为“最新”。 此设置将初始化应用服务中的 QnAMaker 站点扩展。|

完成更改后，需要从 Azure 门户的“概述”页重启服务 。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务自定义不适用于自定义问答（预览版）。

---

## <a name="qna-maker-service"></a>QnA Maker 服务

QnA Maker 服务为以下用户提供配置，以使其可以在单个 QnA Maker 服务及其所有知识库上进行协作。

了解如何向服务[添加协作者](./reference-role-based-access-control.md)。

## <a name="change-azure-cognitive-search"></a>更改 Azure 认知搜索

了解如何更改链接到 QnA Maker 服务的[认知搜索服务](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)。

## <a name="change-default-answer"></a>更改默认答案

了解[如何更改默认答案的文本](How-To/change-default-answer.md)。 

## <a name="telemetry"></a>遥测

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

Application Insights 用于通过 QnA Maker GA 监控遥测。 没有特定于 QnA Maker 的配置设置。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

了解[如何将遥测添加到自定义问答（预览版）服务](How-To/get-analytics-knowledge-base.md)。 

---

## <a name="app-service-plan"></a>应用服务计划

# <a name="qnamaker-ga-stable-release"></a>[QnA Maker GA（稳定版）](#tab/v1)

应用服务计划没有特定于 QnA Maker 的配置设置。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务计划不与自定义问答（预览版）一起使用。

---

## <a name="next-steps"></a>后续步骤

了解有关要导入知识库中的文档和 URL 的[格式](reference-document-format-guidelines.md)的详细信息。
