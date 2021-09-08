---
title: 自定义语音识别概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别是一组联机工具，可用于针对你的应用程序、工具和产品评估并改进 Microsoft 的语音转文本准确度。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.custom: contperf-fy21q2, references_regions
ms.openlocfilehash: 01cd31d60306968580df5bd6d67a3136d49d2277
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542154"
---
# <a name="what-is-custom-speech"></a>什么是自定义语音识别？

自定义语音识别可用于针对你的应用程序和产品，评估并改进 Microsoft 语音转文本准确度。 请遵循本文中的链接开始创建自定义语音转文本体验。

## <a name="whats-in-custom-speech"></a>什么是自定义语音识别？

在开始使用自定义语音识别执行任何操作之前，需要一个 Azure 帐户和一个语音服务订阅。 有了帐户后，即可准备数据、训练和测试模型、查看识别质量、评估准确度，并最终部署和使用自定义语音转文本模型。

此关系图突出显示了组成 [Speech Studio 自定义语音识别区域](https://aka.ms/speechstudio/customspeech)的部分。 使用以下链接详细了解每个步骤。

![此关系图突出显示了组成 Speech Studio 自定义语音识别区域的部分。](./media/custom-speech/custom-speech-overview.png)

1. [订阅和创建项目](#set-up-your-azure-account)。 创建 Azure 帐户并订阅语音服务。 此统一订阅使你可以访问语音转文本、文本转语音、语音翻译和 [Speech Studio](https://speech.microsoft.com/customspeech)。 然后，使用语音服务订阅创建第一个“自定义语音识别”项目。

1. [上传测试数据](./how-to-custom-speech-test-and-train.md)。 上传测试数据（音频文件），以便针对你的应用程序、工具和产品评估 Microsoft 语音转文本产品/服务。

1. [检查识别质量](how-to-custom-speech-inspect-data.md)。 使用 [Speech Studio](https://speech.microsoft.com/customspeech) 播放上传的音频，检查测试数据的语音识别质量。 如需进行量化度量，请参阅[检查数据](how-to-custom-speech-inspect-data.md)。

1. [评估和提高准确度](how-to-custom-speech-evaluate-data.md)。 评估和提高语音转文本模型的准确度。 [Speech Studio](https://speech.microsoft.com/customspeech) 会提供误字率，该指标可以用来确定是否需要更多的训练。 如果对准确度满意，可以直接使用语音服务 API。 如果想要提高准确度 5% - 20%（相对平均值），请在门户中使用“训练”选项卡上传更多的训练数据，例如人为标记的听录和相关的文本。

1. [训练和部署模型](how-to-custom-speech-train-model.md)。 提供编写的脚本（10-1,000 小时）和相关的文本 (<200 MB) 以及音频测试数据，以便提高语音转文本模型的准确度。 该数据有助于训练语音转文本模型。 训练后，请重新测试。 如果对结果感到满意，则可将模型部署到自定义终结点。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

你需要拥有 Azure 帐户和语音服务订阅，才能使用 [Speech Studio](https://speech.microsoft.com/customspeech) 创建自定义模型。 如果你没有帐户和订阅，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。

如果打算使用音频数据训练自定义模型，请选择具有专用硬件的区域之一进行训练。 这将减少训练模型所用的时间，并使你可以使用更多的音频进行训练。 在这些区域，语音服务将使用长达 20 小时的音频进行训练；而在其他区域，最多只能使用 8 小时的音频。

* 澳大利亚东部
* 加拿大中部
* 印度中部
* 美国东部
* 美国东部 2
* 美国中北部
* 北欧
* 美国中南部
* 东南亚
* 英国南部
* US Gov 亚利桑那州
* US Gov 弗吉尼亚州
* 西欧
* 美国西部 2

创建 Azure 帐户和语音服务订阅后，需要登录到 [Speech Studio](https://speech.microsoft.com/customspeech) 并连接订阅。

1. 登录 [Speech Studio](https://aka.ms/custom-speech)。
1. 选择需要使用的订阅并创建语音项目。
1. 如果要修改订阅，请选择顶部菜单中的齿轮按钮。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在 [Speech Studio](https://speech.microsoft.com/customspeech) 中组织成项目。 每个项目特定于域和国家/地区或语言。 例如，可以为使用美式英语的呼叫中心创建一个项目。

若要创建第一个项目，请选择“语音转文本/自定义语音识别”，然后选择“新建项目” 。 遵照向导中的说明创建项目。 创建项目后，应该看到四个选项卡：“数据”、“测试”、“训练”和“部署”。    使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> 刚刚更新了 [Speech Studio](https://aka.ms/custom-speech)（以前称为“自定义语音识别门户”）！ 如果以前已在 CRIS.ai 门户或使用 API 创建了数据、模型、测试并已发布了终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="model-and-endpoint-lifecycle"></a>模型和终结点生命周期

较旧的模型通常逐渐变得没什么用，因为最新的模型通常具有更高的准确度。 因此，基础模型以及通过门户创建的自定义模型和终结点将在 1 年到期以进行调整，在 2 年后到期进行解码。 有关详细说明，请参阅[模型和终结点生命周期](./how-to-custom-speech-model-and-endpoint-lifecycle.md)一文。

## <a name="next-steps"></a>后续步骤

* [准备和测试数据](./how-to-custom-speech-test-and-train.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估和提高模型准确性](how-to-custom-speech-evaluate-data.md)
* [训练和部署模型](how-to-custom-speech-train-model.md)
