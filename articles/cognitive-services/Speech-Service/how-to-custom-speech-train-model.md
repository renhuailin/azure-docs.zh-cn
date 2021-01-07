---
title: 训练和部署自定义语音识别模型 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何定型和部署自定义语音模型。 训练语音到文本模型可以提高 Microsoft 基线模型或自定义模型的识别准确性。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 41fdb3d2e69ae39dbe80f21a953fd9fdaa6d1127
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968460"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>训练和部署自定义语音识别模型

本文介绍如何定型和部署自定义语音模型。 训练语音到文本模型可以提高 Microsoft 基线模型的识别准确性。 您可以使用人为标记的转录和相关文本来训练模型。 这些数据集和以前上传的音频数据一起用于优化和定型语音到文本模型。

## <a name="use-training-to-resolve-accuracy-problems"></a>使用培训解决准确性问题

如果基本模型遇到识别问题，则可以使用人为标记的脚本和相关数据来训练自定义模型并帮助提高准确性。 使用此表来确定用于解决问题的数据集：

| 使用案例 | 数据类型 |
| -------- | --------- |
| 提高特定于行业的词汇和语法的识别准确性，如医疗术语或 IT 术语 | 相关的文本（句子/言语） |
| 定义包含非标准发音（如产品名称或首字母缩写词）的单词或字词的拼音和显示形式 | 相关的文本（发音） |
| 提高对讲话风格、强调或特定背景噪音的识别准确性 | 音频和人为标记的听录内容 |

## <a name="train-and-evaluate-a-model"></a>训练和评估模型

训练模型的第一步是上传训练数据。 请参阅 [准备和测试你的数据](./how-to-custom-speech-test-and-train.md) ，以获取 (最谈话和发音) 准备人为标记的转录和相关文本的分步说明。 上传定型数据后，请按照以下说明开始定型模型：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 如果计划使用音频和人标记的脚本数据集来训练模型，请在 [具有专用硬件](custom-speech-overview.md#set-up-your-azure-account) 培训的区域中选择一个语音订阅。
2. 转到 "**语音到文本**"  >  **自定义语音**  >  **[项目的名称]**  >  **训练**。
3. 选择 " **定型模型**"。
4. 为训练指定 **名称** 和 **说明**。
5. 在 " **方案和基线模型** " 列表中，选择最适合你的域的方案。 如果不确定选择哪种方案，请选择 " **常规**"。 该基线模型是训练的起点。 最新的模型通常是最佳选择。
6. 在 " **选择定型数据** " 页上，选择一个或多个相关文本数据集，或选择要用于定型的音频和人工标记的脚本数据集。 训练新模型时，请从相关文本开始;使用音频和人为标记的脚本训练可能需要更长的时间， (多达 [几天](how-to-custom-speech-evaluate-data.md#improve-model-recognition)) 。
7. 训练完成后，可以在新训练的模型上执行准确性测试。 此步骤是可选的。
8. 选择“创建”，生成自定义模型。

**定型** 表将显示与新模型相对应的新条目。 该表还显示状态： " **正在处理**"、"已 **成功**" 或 " **失败**"。

请参阅[操作说明](how-to-custom-speech-evaluate-data.md)，了解如何评估和提高自定义语音识别模型准确度。 如果选择测试准确性，请务必选择与模型中使用的数据集不同的声音数据集，以获得模型性能的真实认识。

> [!NOTE]
> 基本模型和自定义模型只能使用特定的日期 (请参阅 [模型生命周期](custom-speech-overview.md#model-lifecycle)) 。 对于每个模型和终结点，Speech Studio 在 " **到期** " 列中显示此日期。 在该日期后，对终结点或批处理的请求可能会失败或回退到基本模型。
>
> 使用最新的基本模型重新训练模型，以便从准确性改进中获益，并避免模型过期。

## <a name="deploy-a-custom-model"></a>部署自定义模型

上载并检查数据、评估准确性并为自定义模型定型后，可以部署自定义终结点，以便与应用、工具和产品一起使用。 

若要创建自定义终结点，请登录到 [自定义语音门户](https://speech.microsoft.com/customspeech)。 在页面顶部的 "**自定义语音**" 菜单中选择 "**部署**"。 如果是第一次运行，你会注意到表中未列出任何终结点。 创建终结点后，可以使用此页跟踪每个已部署的终结点。

接下来，选择“添加终结点”，并输入自定义终结点的 **名称** 和 **说明**。  然后选择要与终结点关联的自定义模型。  还可以从此页启用日志记录。 可以通过日志记录监视终结点流量。 如果禁用日志记录，则不会存储流量。

![显示 "新建终结点" 页的屏幕截图。](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 请勿忘记接受有关使用和定价详细信息的条款。

接下来，选择“创建”。  执行此操作后会返回到“部署”  页。 表中现在有自定义终结点的对应条目。 终结点的状态显示其当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态更改为“完成”  时，终结点便可供使用。

部署终结点后，其名称将以链接的形式显示。 选择该链接可查看特定于你的终结点的信息，如终结点密钥、终结点 URL 和示例代码。 记下截止日期，并在该日期之前更新终结点的模型，以确保服务不中断。

## <a name="view-logging-data"></a>查看日志记录数据

如果在 " **部署**" 下访问终结点的页，则可以导出日志记录数据。
> [!NOTE]
>在 Microsoft 拥有的存储上，可以使用30天的日志记录数据。 以后会将其删除。 如果客户拥有的存储帐户已链接到认知服务订阅，则不会自动删除日志记录数据。

## <a name="next-steps"></a>后续步骤

* [了解如何使用自定义模型](how-to-specify-source-language.md)

## <a name="additional-resources"></a>其他资源

- [准备和测试数据](./how-to-custom-speech-test-and-train.md)
- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)
