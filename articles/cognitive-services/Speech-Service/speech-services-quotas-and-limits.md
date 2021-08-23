---
title: 语音服务配额和限制
titleSuffix: Azure Cognitive Services
description: 有关 Azure 认知语音服务配额和限制的快速参考、详细说明及最佳做法
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alexeyo
ms.openlocfilehash: a71a2736533cd2c707d4224db3790e93fe5e2782
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747553"
---
# <a name="speech-service-quotas-and-limits"></a>语音服务配额和限制

本文包含有关所有[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)的 Azure 认知语音服务配额和限制的详细说明。 它也包含避免请求限制的一些最佳做法。

## <a name="quotas-and-limits-quick-reference"></a>配额和限制快速参考
跳转到[文本转语音配额和限制](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>每个语音资源的语音转文本配额和限制
在下表中，没有“可调”行的参数对于所有价格层都不能进行调整。

#### <a name="online-transcription"></a>联机听录
用于 [语音 SDK](speech-sdk.md) 和/或[适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)。

| Quota | 免费 (F0)<sup>1</sup> | 标准 (S0) |
|--|--|--|
| 并发请求限制 - 基础模型终结点 | 1 | 100（默认值） |
| 可调 | 否<sup>2</sup> | Yes<sup>2</sup> |
| 并发请求限制 - 自定义终结点 | 1 | 100（默认值） |
| 可调 | 否<sup>2</sup> | Yes<sup>2</sup> |

#### <a name="batch-transcription"></a>批量听录
| Quota | 免费 (F0)<sup>1</sup> | 标准 (S0) |
|--|--|--|
| [语音转文本 REST API V2.0 和 v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 限制 | 批量听录不适用于 F0 | 每分钟 300 个请求 |
| 最大音频输入文件大小 | 空值 | 1 GB |
| 最大输入 Blob 大小（可能包含多个文件，例如在 zip 存档中；请确保注意上面的文件大小限制） | 空值 | 2.5 GB |
| 最大 Blob 容器大小 | 空值 | 5 GB |
| 每个容器的最大 Blob 数 | 空值 | 10000 |
| 每个听录请求的最大文件数（当使用多个内容 URL 作为输入时） | 空值 | 1000  |
| 同时运行作业的最大数目 | 空值 | 2000  |

#### <a name="model-customization"></a>模型自定义
| Quota | 免费 (F0)<sup>1</sup> | 标准 (S0) |
|--|--|--|
| REST API 限制 | 每分钟 300 个请求 | 每分钟 300 个请求 |
| 语音数据集的最大数目 | 2 | 500 |
| 数据导入的最大声学数据集文件大小 | 2 GB | 2 GB |
| 数据导入的最大语言数据集文件大小 | 200 MB | 1.5 GB |
| 数据导入的最大发音数据集文件大小 | 1 KB | 1 MB |
| 在[创建模型](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API 请求中使用 `text` 参数时的最大文本大小 | 200 KB | 500 KB |

<sup>1</sup> 有关免费 (F0) 定价层，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月限额。<br/>
<sup>2</sup> 请参阅[其他说明](#detailed-description-quota-adjustment-and-best-practices)、[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和[调整说明](#speech-to-text-increasing-online-transcription-concurrent-request-limit)。<br/>

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>每个语音资源的文本转语音配额和限制
在下表中，没有“可调”行的参数对于所有价格层都不能进行调整。

#### <a name="general"></a>常规

| Quota | 免费 (F0)<sup>3</sup> | 标准 (S0) |
|--|--|--|
| 每个语音资源的每秒事务数 (TPS) 的最大值 |  |  |
| 实时 API。 标准语音、神经语音、自定义语音和自定义神经语音 | 200<sup>4</sup> | 200<sup>4</sup> |
| 可调 | 否<sup>4</sup> | 否<sup>4</sup> |
| **HTTP 特定配额** |  |  |
| 每个请求已生成的最大音频长度 | 10 分钟 | 10 分钟 |
| SSML 中非重复 `<voice>` 和 `<audio>` 标记的最大总数 | 50 | 50 |
| **Websocket 特定配额** |  |  |
| 每轮已生成的最大音频长度 | 10 分钟 | 10 分钟 |
| SSML 中非重复 `<voice>` 和 `<audio>` 标记的最大总数 | 50 | 50 |
| 每轮的最大 SSML 消息大小 | 64 KB | 64 KB |

#### <a name="long-audio-api"></a>长音频 API

| Quota | 免费 (F0)<sup>3</sup> | 标准 (S0) |
|--|--|--|
| 最小文本长度 | 不适用 | 纯文本为 400 个字符；SSML 为 400 个[可计费字符](text-to-speech.md#pricing-note) |
| 最大文本长度 | 空值 | 10000 个段落 |
| 开始时间 | 不适用 | 累计 10 个任务或 10000 个字符 |

#### <a name="custom-neural-voice-and-custom-voicesup6sup"></a>自定义神经语音和自定义语音<sup>6</sup>

| Quota | 免费 (F0)<sup>3</sup> | 标准 (S0) |
|--|--|--|
| 每个语音资源的每秒事务数 (TPS) 的最大值 | [请参阅上文](#general) | [请参阅上文](#general) |
| 每个语音资源的最大数据集数 | 10 | 500 |
| 每个语音资源同时上传数据集的最大数目 | 2 | 5 |
| 每个数据集进行数据导入的最大数据文件大小 | 2 GB | 2 GB |
| 不使用脚本上传长音频或音频 | 否 | 是 |
| 每个语音资源同时进行的模型训练的最大数目 | 1（仅限自定义语音<sup>6</sup>） | 3 |
| 每个语音资源的最大自定义终结点数 | 1（仅限自定义语音<sup>6</sup>） | 50 |
| 自定义神经语音的并发请求限制 |  |  |
| 默认值 | 空值 | 10 |
| 可调 | 空值 | 是<sup>5</sup> |
| 自定义语音的并发请求限制<sup>6</sup> |  |  |
| 默认值 | 10 | 10 |
| 可调 | 否<sup>5</sup> | 是<sup>5</sup> |

<sup>3</sup> 有关免费 (F0) 定价层，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月限额。<br/>
<sup>4</sup> 请参阅[其他说明](#detailed-description-quota-adjustment-and-best-practices)和[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。<br/>
<sup>5</sup> 请参阅[其他说明](#detailed-description-quota-adjustment-and-best-practices)、[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和[调整说明](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices)。<br/>
<sup>6</sup> 自定义语音将被弃用，不适用于新创建的语音资源。 请参阅[其他信息](how-to-custom-voice.md#migrate-to-custom-neural-voice)。<br/>

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>详细说明、配额调整和最佳做法
请求增加配额（如果适用）之前，请确保其必要性。 语音服务正在使用自动缩放技术将所需的计算资源带入“按需”模式，同时通过不保留过多的硬件容量来降低客户成本。 每当应用程序收到响应代码 429（“请求过多”）时，但工作负载又在定义的限制内（请参阅[配额和限制快速参考](#quotas-and-limits-quick-reference)），最可能的解释是，该服务正在按需进行扩展，并且尚未达到所需的扩展，因此不会立即有足够的资源来提供请求服务。 此状态通常是暂时的，不应持续太久。

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>在自动缩放期间缓解限制的常规最佳做法
为了最大程度地减少与限制（响应代码 429）相关的问题，我们建议使用以下方法：
- 在应用程序中实现重试逻辑
- 避免工作负载的急剧变化。 逐步增加工作负载 <br/>
*示例。* 应用程序正在使用文本转语音，当前工作负载是 5 TPS（每秒事务数）。 下一秒，负载增加到 20 TPS（即四倍以上）。 服务会立即开始扩展以实现新的负载，但可能无法在一秒钟内完成此操作，因此某些请求将获得响应代码 429。
- 测试不同负载增加模式
  - 请参阅[工作负载模式示例](#example-of-a-workload-pattern-best-practice)
- 在相同或不同的区域中创建其他语音资源，并使用“轮循机制”方法在区域间分配工作负载。 这对于“文本转语音 TPS (每秒事务数)”参数尤其重要，该参数设置为每个语音资源 200 且无法调整

下一部分介绍调整配额的特定案例。<br/>
跳转到[文本转语音：提高自定义神经语音和自定义语音的并发请求限制](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>语音转文本：增加联机听录并发请求限制
默认情况下，每个语音资源（基础模型）和每个自定义终结点（自定义模型）的并发请求数都限制为 100。 此数量在标准定价层中可能有所增加。 提交请求之前，请确保熟悉[此部分](#detailed-description-quota-adjustment-and-best-practices)中的材料，并了解这些[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

>[!NOTE]
> 如果使用自定义模型，请注意，一个语音资源可能与托管许多自定义模型部署的多个自定义终结点相关联。 每个自定义终结点在创建时都设置了默认的并发请求数限制 (100)。 如需调整该限制，需要分别对每个自定义终结点进行调整。 另请注意，语音资源基础模型的并发请求限制数的值对与此资源关联的自定义终结点没有影响。


增加并发请求限制不会直接影响成本。 语音服务使用“只需为使用的资源付费”模型。 此限制定义了服务在开始限制请求之前可缩放的程度。

基础模型和自定义模型的并发请求限制需要单独调整  。

并发请求限制参数的现有值不通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md)不会需要增加并发请求限制，因为容器只受其托管到的硬件的 CPU 约束。 但是，语音容器有其自己的容量限制，这一点应予以考虑。 请参阅问题“是否可以帮助为本地语音转文本容器进行容量规划和成本估算？” （来自[语音容器常见问题解答](./speech-container-howto.md)）。

#### <a name="have-the-required-information-ready"></a>准备好必需的信息：
- 对于基础模型：
  - 语音资源 ID
  - 区域
- 对于自定义模型：
  - 区域
  - 自定义终结点 ID

- **如何获取信息（基础模型）** ：
  - 转到 [Azure 门户](https://portal.azure.com/)
  - 选择要增加并发请求限制的语音资源
  - 选择“属性”（资源管理组） 
  - 复制并保存以下字段的值：
    - 资源 ID
    - 位置（终结点区域）

- **如何获取信息（自定义模型）** ：
  - 转到 [Speech Studio](https://speech.microsoft.com/) 门户
  - 视需要登录
  - 转到自定义语音识别
  - 选择项目
  - 转到“部署”
  - 选择所需终结点
  - 复制并保存以下字段的值：
    - 服务区域（终结点区域）
    - **终结点 ID**

#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
开始增加资源的并发请求限制，或在必要时通过提交支持请求来检查今天的限制：

- 确保具有[所需的信息](#have-the-required-information-ready)
- 转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加（或要检查）并发请求限制的语音资源
- 选择“新建支持请求”（支持 + 疑难解答组） 
- 将出现一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 输入“摘要”（如“增加 STT 并发请求限制”）
- 在“问题类型”中选择“配额或订阅问题”
- 在出现的“问题子类型”中，选择以下内容：
  - “配额或并发请求增加” - 用于增加请求
  - “配额或使用情况验证”，用于检查现有限制
- 单击“下一步:解决方案
- 进一步创建请求
- 在“详细信息”选项卡的“说明”字段中输入以下内容 ：
  - 注释，即与语音转文本配额有关的请求
  - 基础模型或自定义模型 
  - [先前收集的](#have-the-required-information-ready) Azure 资源信息
  - 输入必填信息之后，单击“审阅 + 创建”选项卡中的“创建”按钮 
  - 注意 Azure 门户通知中的支持请求编号。 我们会尽快联系你以进行进一步处理

### <a name="example-of-a-workload-pattern-best-practice"></a>工作负载模式最佳做法的示例
本示例介绍了我们建议采用的方法，以减少由于[正在进行自动缩放](#detailed-description-quota-adjustment-and-best-practices)而导致的可能的请求限制。 它不是精确的方案，而只是我们根据需要请求遵循和调整的模板。

假设语音资源的并发请求限制设置为 300。 从 20 个并发连接启动工作负载，并按每 1.5-2 分钟 20 个并发连接的方式增加负载。 如果收到太多响应代码 429，则控制服务响应并实施回退的逻辑（减少负载）。 然后以 1-2-4-4 分钟模式进行重试。 （即重试在 1 分钟内增加负载，如果仍不起作用，则在 2 分钟内重试，以此类推）

通常，强烈建议在投入生产之前测试工作负载和工作负载模式。

### <a name="text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices"></a>文本转语音：提高自定义神经语音和自定义语音的并发请求限制
默认情况下，自定义神经语音终结点和自定义语音终结点的并发请求数限制为 10。 此数量在标准定价层中可能有所增加。 提交请求之前，请确保熟悉[此部分](#detailed-description-quota-adjustment-and-best-practices)中的材料，并了解这些[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

增加并发请求限制不会直接影响成本。 语音服务使用“只需为使用的资源付费”模型。 此限制定义了服务在开始限制请求之前可缩放的程度。

并发请求限制参数的现有值不通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md)不会需要增加并发请求限制，因为容器只受其托管到的硬件的 CPU 约束。

#### <a name="prepare-the-required-information"></a>准备所需信息：
若要创建一个增加请求，需要提供部署区域和自定义终结点 ID。 若要获取这些信息，请执行以下操作：

- 转到 [Speech Studio](https://speech.microsoft.com/) 门户
- 视需要登录
- 转到“自定义语音”
- 选择项目
- 转到“部署”
- 选择所需终结点
- 复制并保存以下字段的值：
    - 服务区域（终结点区域）
    - **终结点 ID**

#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
开始增加资源的并发请求限制，或在必要时通过提交支持请求来检查今天的限制：

- 确保具有[所需的信息](#prepare-the-required-information)
- 转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加（或要检查）并发请求限制的语音资源
- 选择“新建支持请求”（支持 + 疑难解答组） 
- 将出现一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 在“摘要”中输入内容（如“增大 TTS 自定义终结点并发请求限制”）
- 在“问题类型”中选择“配额或订阅问题”
- 在出现的“问题子类型”中，选择以下内容：
  - “配额或并发请求增加” - 用于增加请求
  - “配额或使用情况验证”，用于检查现有限制
- 单击“下一步:解决方案
- 进一步创建请求
- 在“详细信息”选项卡的“说明”字段中输入以下内容 ：
  - 注释，即该请求是有关“语音转文本”配额的请求
  - [先前收集的](#prepare-the-required-information) Azure 资源信息
  - 输入必填信息之后，单击“审阅 + 创建”选项卡中的“创建”按钮 
  - 注意 Azure 门户通知中的支持请求编号。 我们会尽快联系你以进行进一步处理
