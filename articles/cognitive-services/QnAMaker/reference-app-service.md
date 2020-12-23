---
title: 服务配置-QnA Maker
description: 了解配置资源的方式和位置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: e7dfc6db50432b19d515671fabe26edf9cfb45ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345662"
---
# <a name="service-configuration"></a>服务配置

每个版本的 QnA Maker 都使用一组不同的 Azure 资源 (服务) 。 本文介绍这些服务支持的自定义项。 

## <a name="app-service"></a>应用服务

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 使用应用服务提供 [GENERATEANSWER API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)使用的查询运行时。

这些设置适用于应用服务的 Azure 门户。 可以通过依次选择 " **设置**"、" **配置**" 来使用这些设置。

可以通过 "应用程序设置" 列表设置单个设置，也可以通过选择 " **高级编辑**" 来修改多个设置。

|资源|设置|
|--|--|
|AzureSearchAdminKey|认知搜索-用于 QnA 对存储和 Ranker #1|
|AzureSearchName|认知搜索-用于 QnA 对存储和 Ranker #1|
|DefaultAnswer|找不到匹配项时的应答文本|
|UserAppInsightsAppId|聊天日志和遥测|
|UserAppInsightsKey|聊天日志和遥测|
|UserAppInsightsName|聊天日志和遥测|

完成更改后，需要从 Azure 门户的 "**概述**" 页中 **重新启动** 该服务。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

应用服务自定义不适用于 QnA Maker 托管 (预览版) 。

---

## <a name="qna-maker-service"></a>QnA Maker 服务

QnA Maker 服务为以下用户提供了配置，以便在单个 QnA Maker 服务及其所有知识库上进行协作。

了解 [如何向服务添加协作](./reference-role-based-access-control.md) 者。

## <a name="change-azure-cognitive-search"></a>更改 Azure 认知搜索

了解如何更改链接到 QnA Maker 服务 [的认知搜索服务](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 。

## <a name="change-default-answer"></a>更改默认答案

了解 [如何更改默认答案的文本](How-To/change-default-answer.md)。 

## <a name="telemetry"></a>遥测

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

Application Insights 用于监视 QnA Maker GA 的遥测数据。 没有特定于 QnA Maker 的配置设置。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

了解 [如何将遥测添加到 QnA Maker 管理的 (预览版) 服务](How-To/get-analytics-knowledge-base.md)。 

---

## <a name="app-service-plan"></a>应用服务计划

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (稳定版本) ](#tab/v1)

应用服务计划没有特定于 QnA Maker 的配置设置。

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker 托管 (预览版本) ](#tab/v2)

应用服务计划不与 QnA Maker 托管 (预览版) 一起使用。

---

## <a name="next-steps"></a>后续步骤

了解有关要导入到知识库中的文档和 Url [格式](reference-document-format-guidelines.md) 的详细信息。