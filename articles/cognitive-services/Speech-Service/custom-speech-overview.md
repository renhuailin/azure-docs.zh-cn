---
title: 自定义语音识别概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别是一组联机工具，可用于针对你的应用程序、工具和产品评估并改进我们的语音转文本准确度。 只需准备几个测试性音频文件即可开始。 请遵循以下链接开始创建自定义语音转文本体验。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 7f8fb0488c863868b1076ad5b3fa7868d1880a6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353776"
---
# <a name="what-is-custom-speech"></a>什么是自定义语音识别？

[自定义语音识别](https://aka.ms/customspeech)是一组基于 UI 的工具，可用于针对你的应用程序和产品，评估并改进 Microsoft 的语音转文本准确度。 只需准备几个测试性音频文件即可开始。 请遵循以下链接开始创建自定义语音转文本体验。

## <a name="whats-in-custom-speech"></a>什么是自定义语音识别？

在开始使用自定义语音识别执行任何操作之前，需要一个 Azure 帐户和一个语音服务订阅。 有了帐户后，即可准备数据、训练和测试模型、查看识别质量、评估准确度，并最终部署和使用自定义语音转文本模型。

此图突出显示了[自定义语音识别门户](https://aka.ms/customspeech)的组件。 使用以下链接详细了解每个步骤。

![突出显示组成自定义语音识别门户的不同组件。](./media/custom-speech/custom-speech-overview.png)

1. [订阅和创建项目](#set-up-your-azure-account) - 创建 Azure 帐户并订阅语音服务。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 然后，可以使用语音服务订阅创建第一个“自定义语音识别”项目。

1. [上传测试数据](./how-to-custom-speech-test-and-train.md) - 上传测试数据（音频文件），以便针对你的应用程序、工具和产品评估 Microsoft 的语音转文本产品/服务。

1. [检查识别质量](how-to-custom-speech-inspect-data.md) - 使用[自定义语音识别门户](https://speech.microsoft.com/customspeech)播放上传的音频，检查测试数据的语音识别质量。 如需进行量化度量，请参阅[检查数据](how-to-custom-speech-inspect-data.md)。

1. [评估和提高准确度](how-to-custom-speech-evaluate-data.md) - 评估和提高语音转文本模型的准确度。 [自定义语音识别门户](https://speech.microsoft.com/customspeech)会提供误字率，该指标可以用来确定是否需要更多的训练。 如果对准确度满意，可以直接使用语音服务 API。 如果希望提高准确度 5% - 20%（相对平均值），请在门户中使用“训练”选项卡上传更多的训练数据，例如人为标记的听录和相关的文本。

1. [训练和部署模型](how-to-custom-speech-train-model.md) - 提供编写的脚本（10-1,000 小时）和相关的文本 (<200 MB) 以及音频测试数据，以便提高语音转文本模型的准确度。 该数据有助于训练语音转文本模型。 训练并重新测试后，如果对结果感到满意，则可将模型部署到自定义终结点。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要拥有 Azure 帐户和语音服务订阅，才能使用[自定义语音识别门户](https://speech.microsoft.com/customspeech)创建自定义模型。 如果你没有帐户和订阅，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。

> [!NOTE]
> 请务必创建标准 (S0) 订阅，免费 (F0) 订阅不受支持。

创建 Azure 帐户和语音服务订阅后，需要登录到[“自定义语音识别”门户](https://speech.microsoft.com/customspeech)并连接订阅。

1. 登录到[自定义语音识别门户](https://aka.ms/custom-speech)。
1. 选择需在其上工作的订阅并创建语音项目。
1. 若要修改订阅，请使用顶部导航栏中的 **齿轮** 图标。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在 [自定义语音识别门户](https://speech.microsoft.com/customspeech)中组织成 **项目**。 每个项目特定于域和国家/地区或语言。 例如，可以为使用美式英语的呼叫中心创建一个项目。

若要创建第一个项目，请选择“语音转文本/自定义语音识别”选项卡，然后单击“新建项目”。  遵照向导中的说明创建项目。 创建项目后，应该看到四个选项卡：“数据”、“测试”、“训练”和“部署”。    使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [“自定义语音识别”门户](https://aka.ms/custom-speech)最近已更新！ 如果以前已在 CRIS.ai 门户或使用 API 创建了数据、模型、测试并已发布了终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="model-lifecycle"></a>模型生命周期

自定义语音识别同时使用“基础模型”和“自定义模型” 。 每个语言都有一个或多个基础模型。 通常，在将新语音模型发布到常规语音服务时，同时会将其作为新基础模型导入到自定义语音识别服务。 它们通常每3-6 个月更新一次，较旧的模型在时间上变得不太有用，因为最新的模型通常具有更高的准确性。

而“自定义模型”是通过根据特定客户场景调整所选基础模型来创建的。 一旦到达满足你的需求的情况下，你可能会持续使用特定的自定义模型，但建议你定期更新到最新的基本模型，并使用其他数据重新训练。

与模型生命周期相关的其他关键术语包括：

* 调整：使用基础模型，并使用文本数据和/或音频数据，根据你的域/场景对其进行自定义
* 解码：使用模型并执行语音识别（将音频解码为文本）
* 终结点：以特定于用户的方式部署仅给定用户可访问的基础模型或自定义模型。

### <a name="expiration-timeline"></a>过期时间线

由于新的模型和新功能变得可用，更旧的模型将被停用，请参阅模型和终结点过期的以下时间线：

基础模型 

* 调整：可使用 1 年。 导入模型后，可用来创建自定义模型的时间为 1 年。 1 年后，需要通过更新的基础模型版本创建新的自定义模型。  
* 解码：导入后可使用 2 年。 这意味着你可以创建一个终结点，并可对此模型使用批处理听录 2 年。 
* 终结点：使用时间线与解码的时间线相同

**自定义模式**

* 解码：创建模型后可使用 2 年。 这意味着可以在创建自定义模型（批处理/实时/测试）后使用该模型 2 年（批处理/实时/测试）。 2 年后，你应该重新训练模型，因为通常应停用基础模型以进行调整。  
* 终结点：使用时间线与解码的时间线相同

当基础模型或自定义模型过期时，它将始终回退到最新的基础模型版本。 因此，你的实现永远不会中断，但如果自定义模型到期，模型对于特定数据的准确度可能会降低。 可以在自定义语音识别门户的以下位置查看模型的过期时间：

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