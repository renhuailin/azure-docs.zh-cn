---
title: 技能组中的自定义 AML 技能
titleSuffix: Azure Cognitive Search
description: 使用 Azure 机器学习模型扩展 Azure 认知搜索技能组的功能。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 529be70de61fe9adb025ee801bd417bc860ef5e6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736327"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure 认知搜索扩充管道中的 AML 技能

> [!IMPORTANT] 
> 此技能根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)处于公开预览状态。 [预览版 REST API](/rest/api/searchservice/index-preview) 支持此技能。

通过 AML 技能，可以使用自定义 [Azure 机器学习](../machine-learning/overview-what-is-azure-machine-learning.md) (AML) 模型来扩展 AI 扩充。 在[训练和部署](../machine-learning/concept-azure-machine-learning-architecture.md#workspace) AML 模型后，AML 技能即可将其集成到 AI 扩充中。

与内置技能一样，AML 技能具有输入和输出。 输入将以 JSON 对象形式发送到已部署的 AML 服务，进而以响应形式输出 JSON 有效负载以及成功状态代码。 响应需包含 AML 技能指定的输出。 其他任何响应都被视为错误，并且不会执行任何扩充。

> [!NOTE]
> 索引器对于 AML 服务返回的特定标准 HTTP 状态代码会重试两次。 这些 HTTP 状态代码为：
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>先决条件

* [AML 工作区](../machine-learning/concept-workspace.md)
* 此工作区中[已部署模型](../machine-learning/how-to-deploy-azure-kubernetes-service.md)的 [Azure Kubernetes 服务 AML 计算目标](../machine-learning/concept-compute-target.md)
  * [计算目标应已启用 SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service)。 Azure 认知搜索仅允许访问 https 终结点
  * 不能使用自签名证书。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.AmlSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。 选择使用哪些参数取决于 [AML 服务所需的身份验证（如果有）](#WhatSkillParametersToUse)

| 参数名称 | 说明 |
|--------------------|-------------|
| `uri` | （[不进行身份验证或密钥身份验证](#WhatSkillParametersToUse)需要）JSON 有效负载将发送到的 [AML 服务的评分 URI](../machine-learning/how-to-consume-web-service.md)。 仅支持 https URI 方案。 |
| `key` | （[密钥身份验证](#WhatSkillParametersToUse)需要）[AML 服务的密钥](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)。 |
| `resourceId` | （[令牌身份验证](#WhatSkillParametersToUse)需要）。 AML 服务的 Azure 资源管理器资源 ID。 它的格式应如下所示：subscriptions/{guid}/resourceGroups/{resource-group-name}/Microsoft.MachineLearningServices/workspaces/{workspace-name}/services/{service_name}。 |
| `region` | （对于[令牌身份验证](#WhatSkillParametersToUse)可选）。 AML 服务部署到的[区域](https://azure.microsoft.com/global-infrastructure/regions/)。 |
| `timeout` | （可选）如果指定，表明执行 API 调用的 http 客户端的超时值。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 例如，`PT60S` 表示 60 秒。 如果未设置，选择的是默认值 30 秒。 超时可以设置为最大 230 秒和最小 1 秒。 |
| `degreeOfParallelism` | （可选）如果指定了此值，则指示索引器将对你提供的终结点进行的并行调用数。 如果终结点在请求负载过高的情况下失败，则可以减小此值；如果终结点能够接受更多请求并且希望提高索引器的性能，则可以增大此值。  如果未设置，则将使用默认值 5。 可以为 degreeOfParallelism 设置的最大值为 10，最小值为 1。

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>要使用的技能参数

需要哪些 AML 技能参数取决于 AML 服务使用的身份验证（如果有）。 AML 服务提供三个身份验证选项：

* [基于密钥的身份验证](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication)。 提供静态密钥，对来自 AML 技能的评分请求进行身份验证
  * 使用 uri 和 key 参数
* [基于令牌的身份验证](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)。 [使用基于令牌的身份验证部署](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication) AML 服务。 在 AML 服务工作区中，对 Azure 认知搜索服务的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)授予[读者角色](../machine-learning/how-to-assign-roles.md)。 然后，AML 技能将使用 Azure 认知搜索服务的托管标识针对 AML 服务进行身份验证，无需任何静态密钥。
  * 使用 resourceId 参数。
  * 如果 Azure 认知搜索服务与 AML 工作区位于不同的区域，请使用 region 参数设置 AML 服务已部署到的区域
* 不进行身份验证。 使用 AML 服务不需要任何身份验证
  * 使用 uri 参数

## <a name="skill-inputs"></a>技能输入

此技能没有“预定义”输入。 可以选择在执行此技能时已提供的一个或多个字段作为输入，发送到 AML 服务的 JSON 有效负载将会有不同的字段。

## <a name="skill-outputs"></a>技能输出

此技能没有“预定义”输出。 根据 AML 服务返回的响应，添加输出字段，以便能够从 JSON 响应中选择这些字段。

## <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>示例输入 JSON 结构

此 JSON 结构表示将发送到 AML 服务的有效负载。 该结构的顶层字段对应技能定义的 `inputs` 部分中指定的“名称”。 这些字段的值将来自这些字段的 `source`（可能来自文档中的字段，也可能来自另一个技能）

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>示例输出 JSON 结构

输出对应 AML 服务返回的响应。 AML 服务只应返回 JSON 有效负载（通过查看 `Content-Type` 响应头验证），对象的字段应为与 `output` 中的“名称”对应的扩充，且其值应被视为扩充。

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>内联成形示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>内联成形 JSON 结构

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>内联成形示例输出 JSON 结构

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>错误案例
除 AML 不可用或发出未成功状态代码之外，还会将以下情况视为出错：

* 如果 AML 服务返回成功状态代码，但响应指明它不是 `application/json`，则响应会被视为无效，并且不会执行任何扩充。
* 如果 AML 服务返回无效 json

在 AML 服务不可用或返回 HTTP 错误的情况下，索引器执行历史记录中会以友好方式添加错误，其中包含有关 HTTP 错误的任何可用详细信息。

## <a name="see-also"></a>另请参阅

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [AML 服务故障排除](../machine-learning/how-to-troubleshoot-deployment.md)