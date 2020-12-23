---
title: 在文本分析 v3 中指定模型版本
titleSuffix: Azure Cognitive Services
description: 了解如何指定用于数据的文本分析 API 模型。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 7ab2e037cc7593431a668a216fcc152c0511c410
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965079"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>文本分析 API 中的模型版本控制

通过版本 3 的文本分析 API，你可以选择用于你的数据的模型版本。 在 API 请求中使用可选的 `model-version` 参数选择一个模型版本。 例如：`<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`。 如果未指定此参数，API 会默认为最新的稳定版。 

## <a name="available-versions"></a>可用版本

使用下表查找每个托管终结点支持的模型版本。


| 终结点                        | 支持的版本                                     | 最新版本 |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`               | `2020-09-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/entities/health`              | `2020-09-03`                           | `2020-09-03`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


可以在[新增功能](../whats-new.md)中找到有关这些模型的更新的详细信息。

## <a name="text-analytics-for-health"></a>运行状况文本分析

[运行状况文本分析](../how-tos/text-analytics-for-health.md)容器使用与上述 API 终结点不同的模型版本控制。  请注意，每个容器映像仅有一个可用的模型版本。

| 终结点                        | 容器映像标记                     | 模型版本 |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `1.1.013530001-amd64-preview` 或更高版本          | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
