---
title: 自定义语音识别概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组联机工具，通过这些工具，你可以为应用程序、工具和产品评估和改进 Microsoft 语音到文本的准确性。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 96b8e4031520e1a0540fca7d84b3f9ae1cca9c22
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490340"
---
# <a name="what-is-custom-speech"></a>什么是自定义语音识别？

[自定义语音](https://aka.ms/customspeech) 是一组基于 UI 的工具，通过这些工具，你可以为应用程序和产品评估和改进 Microsoft 语音到文本的准确性。 开始使用只是几个测试音频文件。 请按照本文中的链接开始创建自定义语音到文本体验。

## <a name="whats-in-custom-speech"></a>什么是自定义语音识别？

在开始使用自定义语音识别执行任何操作之前，需要一个 Azure 帐户和一个语音服务订阅。 拥有帐户后，可以准备数据、训练和测试模型、检查识别质量、评估准确性，并最终部署和使用自定义语音到文本模型。

此图突出显示了[自定义语音识别门户](https://aka.ms/customspeech)的组件。 使用以下链接详细了解每个步骤。

![突出显示组成自定义语音门户的组件的关系图。](./media/custom-speech/custom-speech-overview.png)

1. [订阅和创建项目](#set-up-your-azure-account)。 创建 Azure 帐户并订阅语音服务。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 然后使用语音服务订阅创建第一个自定义语音项目。

1. [上载测试数据](./how-to-custom-speech-test-and-train.md)。 )  (音频文件上传测试数据，以便为应用程序、工具和产品评估 Microsoft 语音到文本产品/服务。

1. [检查识别质量](how-to-custom-speech-inspect-data.md)。 使用 [自定义语音门户](https://speech.microsoft.com/customspeech) 播放上传的音频，并检查测试数据的语音识别质量。 如需进行量化度量，请参阅[检查数据](how-to-custom-speech-inspect-data.md)。

1. [评估并改善准确性](how-to-custom-speech-evaluate-data.md)。 评估并改善语音到文本模型的准确性。 [自定义语音门户](https://speech.microsoft.com/customspeech)将提供 *Word 错误率*，可用于确定是否需要额外的培训。 如果对准确度满意，可以直接使用语音服务 API。 如果要以5% 到20% 的相对平均值提高准确性，请使用门户中的 " **培训** " 选项卡上传附加的定型数据，如人为的 "记录" 和 "相关文本"。

1. [训练和部署模型](how-to-custom-speech-train-model.md)。 通过提供书面记录 (10 到1000小时) 和相关文本（ ( # B0 200 MB) 连同音频测试数据），提高语音到文本模型的准确性。 该数据有助于训练语音转文本模型。 训练后，请重新测试。 如果对结果满意，则可以将模型部署到自定义终结点。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

你需要具有 Azure 帐户和语音服务订阅才能使用 [自定义语音门户](https://speech.microsoft.com/customspeech) 来创建自定义模型。 如果你没有帐户和订阅，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。

> [!NOTE]
> 请确保创建标准 (S0) 订阅。 不支持免费 (F0) 订阅。

创建 Azure 帐户和语音服务订阅后，需要登录到 [自定义语音门户](https://speech.microsoft.com/customspeech) 并连接订阅。

1. 登录到[自定义语音识别门户](https://aka.ms/custom-speech)。
1. 选择需要使用的订阅，并创建一个语音项目。
1. 如果要修改订阅，请在顶部菜单中选择 "齿轮" 按钮。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容组织到 [自定义语音门户](https://speech.microsoft.com/customspeech)中的 *项目* 中。 每个项目特定于域和国家/地区或语言。 例如，你可以为在美国中使用英语的呼叫中心创建项目。

若要创建第一个项目，请选择 " **语音到文本"/"自定义语音**"，然后选择 " **新建项目**"。 遵照向导中的说明创建项目。 创建项目后，应该会看到四个选项卡： **数据**、 **测试**、 **定型** 和 **部署**。 使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [“自定义语音识别”门户](https://aka.ms/custom-speech)最近已更新！ 如果以前已在 CRIS.ai 门户或使用 API 创建了数据、模型、测试并已发布了终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="model-lifecycle"></a>模型生命周期

自定义语音使用 *基本模型* 和 *自定义模型*。 每个语言都有一个或多个基础模型。 通常，当将新的语音模型发布到常规语音服务时，它也作为新的基本模型导入到自定义语音服务。 每3到6个月更新一次。 随着时间的推移，较旧的模型通常不太有用，因为最新的模型通常具有更高的准确性。

而“自定义模型”是通过根据特定客户场景调整所选基础模型来创建的。 你可以在有满足你的需求的情况后，长时间使用特定的自定义模型。 但是，我们建议您定期更新到最新的基本模型，并随着时间的推移与其他数据重新训练。 

与模型生命周期相关的其他关键术语包括：

* **适配**：采用基本模型并使用文本数据和/或音频数据将其自定义到你的域/方案。
* **解码**：使用模型和执行语音识别 (将音频解码为文本) 。
* **终结点**： *仅* 供给定用户访问的基本模型或自定义模型的用户特定的部署。

### <a name="expiration-timeline"></a>过期时间线

由于新的模型和新功能变得可用，更旧的模型将被停用，请参阅模型和终结点过期的以下时间线：

基础模型 

* 适配：可用于一年。 导入模型后，可在一年中创建自定义模型。 一年后，必须从较新的基本模型版本创建新的自定义模型。  
* 解码：导入后两年内可用。 因此，你可以创建一个终结点，并使用此模型将批处理脚本用于两年。 
* 终结点：与解码在同一时间线上可用。

**自定义模式**

* 解码：创建模型后两年内可用。 因此，在创建自定义模型后，可将其用于两年 (批/实时/测试) 。 在两年后， *您应该* 重新训练您的模型，因为基础模型通常已不推荐进行调整。  
* 终结点：与解码在同一时间线上可用。

当基础模型或自定义模型过期时，它将始终回退到最新的基础模型版本。 因此，您的实现将永远不会中断，但如果自定义模式过期，则可能会降低 *特定数据* 的准确性。 可以在自定义语音识别门户的以下位置查看模型的过期时间：

* 模型训练摘要
* 模型训练详细信息
* 部署摘要
* 部署详细信息

还可以通过 [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) JSON 响应中的属性下的和自定义语音 api 检查到期日期 `deprecationDates` 。

请注意，可以通过在自定义语音门户的 "部署" 部分中更改终结点使用的模型，或通过自定义语音 API，在不停机的情况下升级自定义语音终结点上的模型。

## <a name="next-steps"></a>后续步骤

* [准备和测试数据](./how-to-custom-speech-test-and-train.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估和提高模型准确性](how-to-custom-speech-evaluate-data.md)
* [训练和部署模型](how-to-custom-speech-train-model.md)