---
title: 缩放和管理自定义技能
titleSuffix: Azure Cognitive Search
description: 了解有效地缩放自定义技能以实现最大吞吐量的工具和技术。 自定义技能调用自定义 AI 模型或逻辑，可将这些模型或逻辑添加到 Azure 认知搜索中的 AI 扩充索引管道。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 6b44cc3bde1a2e36cdd902a7366df8841795891a
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728426"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>有效地横向扩展自定义技能

自定义技能是实现特定接口的 Web API。 可以在任何可公开寻址的资源上实现自定义技能。 最常见的自定义技能实现包括：
* 用于自定义逻辑技能的 Azure Functions
* 用于简单容器化 AI 技能的 Azure Webapps
* 用于更复杂或更大技能的 Azure Kubernetes 服务。

## <a name="prerequisites"></a>先决条件

+ 查看[自定义技能接口](cognitive-search-custom-skill-interface.md)，其中介绍了自定义技能应该实现的输入/输出接口。

+ 设置环境 你可以从[此端到端教程](../azure-functions/create-first-function-vs-code-python.md)着手，以使用 Visual Studio Code 和 Python 扩展设置无服务器 Azure 函数。

## <a name="skillset-configuration"></a>技能集配置

若要配置自定义技能以最大程度地提高索引过程的吞吐量，需要了解技能、索引器配置以及技能与每个文档的关系。 例如，每个文档调用技能的次数和每次调用的预期持续时间。

### <a name="skill-settings"></a>技能设置

在[自定义技能](cognitive-search-custom-skill-web-api.md)上，设置以下参数。

1. 设置自定义技能的 `batchSize`，以配置在一次调用技能时发送到该技能的记录数。

2. 设置 `degreeOfParallelism` 以校准索引器将对你的技能发出的并发请求数。

3. 将 `timeout` 设置为技能足以做出有效响应的值。

4. 在 `indexer` 定义中，将 [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) 设置为应从数据源读取并同时扩充的文档数。

### <a name="considerations"></a>注意事项

若要设置这些变量以优化索引器性能，需要确定技能在处理多个并发小请求还是较少的大请求时表现更佳。 需要考虑以下几个问题：

* 什么是技能调用基数？ 该技能是针对每个文档执行一次（例如文档分类技能），还是针对每个文档执行多次（例如段落分类技能）？

* 根据技能批大小，平均要从数据源读取多少文档来填充技能请求？ 理想情况下，此值应小于索引器批大小。 如果批大小大于 1，你的技能可以从多个源文档接收记录。 例如，如果索引器批计数为 5，而技能批计数为 50，并且每个文档只生成 5 条记录，索引器需要跨多个索引器批填充自定义技能请求。

* 索引器批可生成的平均请求数应为你提供并行度的最佳设置。 如果托管该技能的基础结构无法支持这种并发级别，请考虑降低并行度。 作为最佳做法，请使用几个文档测试你的配置，以验证你对参数的选择。

* 使用较小的文档示例进行测试，评估技能的执行时间与处理文档子集所花费的总时间。 索引器是否花费更多时间来生成批处理或等待技能的响应？ 

* 请考虑并行度的上游含义。 如果自定义技能的输入是先前技能的输出，则是否有效横向扩展了技能组中的所有技能以最大程度地减少延迟？

## <a name="error-handling-in-the-custom-skill"></a>自定义技能中的错误处理

技能成功完成后，自定义技能应返回成功状态代码 HTTP 200。 如果批中的一个或多个记录导致错误，请考虑返回多状态代码 207。 记录的错误或警告列表应包含相应的消息。

批中的任何项目出错都将导致相应文档失败。 如果需要文档成功，则返回警告。

超过 299 的任何状态代码都将视为错误，并且所有扩充操作均失败，从而导致文档失败。 

### <a name="common-error-messages"></a>常见错误消息

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` 在技能上设置超时参数，以允许更长的执行时间。

* `Could not execute skill because Web Api skill response is invalid.` 指示技能未以自定义技能响应格式返回消息。 这可能是因为技能中出现未捕获的异常。

* `Could not execute skill because the Web Api request failed.` 很可能是由授权错误或异常引起的。

* `Could not execute skill.` 通常是因为技能响应映射到文档层次结构中的现有属性。

## <a name="testing-custom-skills"></a>测试自定义技能

首先用 REST API 客户端测试自定义技能，以验证：

* 技能为请求和响应实现自定义技能接口

* 技能返回 `application/JSON` MIME 类型的有效 JSON

* 返回有效的 HTTP 状态代码

创建一个[调试会话](cognitive-search-debug-session.md)，将你的技能添加到技能集中，并确保它生成有效的扩充。 虽然调试会话不允许你调整技能的性能，但通过该会话可确保使用有效值配置技能并返回预期的扩充对象。

## <a name="best-practices"></a>最佳做法

* 尽管技能可以接受和返回更大的有效负载，但在返回 JSON 时，请考虑将响应限制为 150 MB 或更低。

* 请考虑在索引器和技能上设置批大小，以确保每个数据源批为你的技能生成一个完整的有效负载。

* 对于长期任务，请将超时设置为足够大的值，以确保索引器在并发处理文档时不会出错。

* 优化索引器批大小、技能批大小和技能并行度，以生成技能期望的负载模式（较少的大型请求或多个小型请求）。

* 使用详细的故障日志监视自定义技能，因为你可能会遇到由于数据可变性导致特定请求不断失败的情况。


## <a name="next-steps"></a>后续步骤
祝贺你！ 你的自定义技能现已缩放到索引器上的最大吞吐量。 

+ [强大技能：自定义技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [将自定义技能添加到 AI 扩充管道](cognitive-search-custom-skill-interface.md)
+ [添加 Azure 机器学习技能](./cognitive-search-aml-skill.md)
+ [使用调试会话测试更改](./cognitive-search-debug-session.md)