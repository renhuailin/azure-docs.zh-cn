---
title: 训练和部署自定义语音识别模型 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何训练和部署自定义语音识别模型。 训练语音转文本模型可以提高 Microsoft 基线模型或某个自定义模型的识别准确度。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: pafarley
ms.openlocfilehash: 73076c141f28bc22450c87491053130ace6b5793
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540750"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>训练和部署自定义语音识别模型

本文介绍如何训练和部署自定义语音识别模型。 训练语音转文本模型可以提高 Microsoft 基线模型的识别准确度。 使用人为标记的听录内容和相关文本训练模型。 这些数据集以及以前上传的音频数据用于优化和训练语音转文本模型。

## <a name="use-training-to-resolve-accuracy-problems"></a>通过训练解决准确度问题

如果遇到基本模型识别问题，则可以使用人为标记的脚本和相关数据来训练自定义模型和帮助提高准确度。 使用此表可确定应使用哪个数据集来解决问题：

| 使用案例 | 数据类型 |
| -------- | --------- |
| 提高特定于行业的词汇和语法（例如医疗术语或 IT 行话）的识别准确度 | 相关的文本（句子/言语） |
| 定义发音不标准的字词或术语（例如产品名或首字母缩写）的语音和显示形式 | 相关的文本（发音） |
| 提高说话风格、口音或特定背景杂音的识别准确度 | 音频和人为标记的听录内容 |

## <a name="train-and-evaluate-a-model"></a>训练和评估模型

训练模型的第一步是上传训练数据。 请参阅[准备和测试数据](./how-to-custom-speech-test-and-train.md)以获取分步说明，了解如何准备人为标记的听录内容和相关文本（言语和发音）。 上传训练数据以后，请按以下说明开始训练模型：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 如果计划使用音频和人为标记的听录内容数据集来训练模型，请在[具有专用硬件的区域](custom-speech-overview.md#set-up-your-azure-account)选择一个语音订阅进行训练。
2. 转到“语音转文本” > “自定义语音识别” >  [项目名称]  > “训练”   。
3. 选择“训练模型”。
4. 为训练提供“名称”和“描述” 。
5. 在“方案和基线模型”列表中，选择最适合你的领域的方案。 如果不确定要选择哪个方案，请选择“通用”。 该基线模型是训练的起点。 最新的模型通常是最佳选择。
6. 在“选择训练数据”页中，选择一个或多个要用于训练的相关文本数据集或音频和人为标记的听录内容数据集。

> [!NOTE]
> 在训练新模型时，请从相关文本开始；使用音频和人为标记的听录内容进行训练所需的时间可能会长得多（长达[数天](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)）。

> [!NOTE]
> 并非所有基本模型都支持通过音频训练。 如果基本模型不支持该训练，语音服务将仅使用脚本中的文本，而忽略音频。 有关支持使用音频数据进行训练的基础模型的列表，请参阅[语言支持](language-support.md#speech-to-text)。

> [!NOTE]
> 如果要更改用于训练的基础模型，并且你的训练数据集内有音频，请务必检查新选择的基础模型是否[支持使用音频数据进行训练](language-support.md#speech-to-text)。 如果以前使用的基础模型不支持使用音频数据进行训练，而训练数据集包含音频，则新的基础模型的训练时间将会 **大幅** 增加，并且可能会轻易地从几个小时增加到几天及更长时间。 如果语音服务订阅所在区域没有[专用于训练的硬件](custom-speech-overview.md#set-up-your-azure-account)，则更是如此。
>
> 如果你面临以上段落中所述的问题，则可以通过减少数据集内的音频量或完全删除音频并仅留下文本，来快速缩短训练时间。 如果语音服务订阅所在区域没有[专用于训练的硬件](custom-speech-overview.md#set-up-your-azure-account)，我们强烈建议你完全删除音频并仅留下文本。

7. 完成训练后，可以对新训练的模型执行准确度测试。 此步骤是可选的。
8. 选择“创建”，生成自定义模型。

“训练”表将显示对应于新模型的新条目。 该表还会显示以下状态：“正在处理”、“成功”或“失败”  。

请参阅[操作说明](how-to-custom-speech-evaluate-data.md)，了解如何评估和提高自定义语音识别模型准确度。 如果选择测试准确度，则选择的声学数据集必须不同于你对自己的模型使用的数据集，这样才能获得真正有意义的模型性能。

> [!NOTE]
> 基础模型和自定义模型最多都只能用到某个特定日期（请参阅[模型和终结点生命周期](./how-to-custom-speech-model-and-endpoint-lifecycle.md)）。 Speech Studio 在每个模型和终结点的“过期时间”列中显示此日期。 在该日期之后，对终结点或对批量听录的请求可能会失败或回退到基础模型。
>
> 请使用当时最新的基础模型来重新训练你的模型，以受益于准确度的提高，并避免模型过期。

## <a name="deploy-a-custom-model"></a>部署自定义模型

上传并检查数据、评估准确度以及训练自定义模型以后，即可部署可以与应用、工具和产品配合使用的自定义终结点。 

要创建自定义终结点，请登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 在页面顶部的“自定义语音识别”菜单中选择“部署” 。 如果是第一次运行，你会注意到表中未列出任何终结点。 创建一个终结点后，即可使用此页面跟踪每个已部署的终结点。

接下来，选择“添加终结点”，并输入自定义终结点的 **名称** 和 **说明**。  然后选择要与此终结点关联的自定义模型。  也可以通过此页启用日志记录。 可以通过日志记录监视终结点流量。 在禁用日志记录的情况下，不会存储流量。

![显示“新建终结点”页的屏幕截图。](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 请勿忘记接受有关使用和定价详细信息的条款。

接下来，选择“创建”。  执行此操作后会返回到“部署”  页。 表中现在有自定义终结点的对应条目。 终结点的状态显示其当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态更改为“完成”  时，终结点便可供使用。

部署终结点后，其名称将以链接的形式显示。 选择此链接可查看特定于该终结点的信息，例如终结点密钥、终结点 URL 和示例代码。 请记下过期日期，并在该日期之前更新终结点的模型，以确保服务不会中断。

## <a name="view-logging-data"></a>查看日志记录数据

如果转到“部署”下的终结点页，则可以导出日志记录数据。
> [!NOTE]
>日志记录数据在 Microsoft 拥有的存储上可供使用 30 天。 之后会被删除。 如果客户拥有的存储帐户已关联到认知服务订阅，则不会自动删除日志记录数据。

## <a name="next-steps"></a>后续步骤

* [了解如何使用自定义模型](how-to-specify-source-language.md)

## <a name="additional-resources"></a>其他资源

- [准备和测试数据](./how-to-custom-speech-test-and-train.md)
- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)
