---
title: 训练和部署自定义语音模型-语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍如何定型和部署自定义语音模型。 训练语音转文本模型可以提高 Microsoft 的基线模型或某个自定义模型的识别准确度。 模型使用人为标记的听录和相关的文本进行训练。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025593"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>训练和部署自定义语音识别模型

本文介绍如何定型和部署自定义语音模型。 训练语音转文本模型可以提高 Microsoft 的基线模型的识别准确度。 模型使用人为标记的听录和相关的文本进行训练。 这些数据集以及以前上传的音频数据用于优化和训练语音转文本模型。

## <a name="use-training-to-resolve-accuracy-issues"></a>通过训练解决准确度问题

如果基本模型遇到识别问题，使用带标签的脚本和相关数据训练自定义模型可以帮助提高准确性。 使用此表可确定应使用哪个数据集来解决问题：

| 使用案例 | 数据类型 |
| -------- | --------- |
| 提高特定于行业的词汇和语法（例如医疗术语或 IT 行话）的识别准确度。 | 相关的文本（句子/言语） |
| 定义发音不标准的字词或术语（例如产品名或首字母缩写）的语音和显示形式。 | 相关的文本（发音） |
| 提高说话风格、口音或特定背景杂音的识别准确度。 | 音频和人为标记的听录内容 |

## <a name="train-and-evaluate-a-model"></a>训练和评估模型

训练模型的第一步是上传训练数据。 请参阅[准备和测试数据](./how-to-custom-speech-test-and-train.md)以获取分步说明，了解如何准备人为标记的听录和相关的文本（言语和发音）。 上传训练数据以后，请按以下说明开始训练模型：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。
2. 导航到“语音转文本”>“自定义语音识别”> [项目名称] >“训练”。
3. 单击“训练模型”。
4. 接下来，为训练提供 **名称** 和 **说明**。
5. 从“方案和基线模型”下拉菜单中，选择最适合你的领域的方案。 如果不确定要选择哪个方案，请选择“通用”。 该基线模型是训练的起点。 最新的模型通常是最佳选择。
6. 在“选择训练数据”页中，选择一个或多个要用于训练的音频和人为标记的听录数据集。
7. 完成训练后，可以选择对新训练的模型执行准确度测试。 此步骤是可选的。
8. 选择“创建”，生成自定义模型。

“训练”表将显示对应于此新建模型的新条目。 该表还会显示以下状态：“正在处理”、“成功”、“失败”。

请参阅 [如何](how-to-custom-speech-evaluate-data.md) 评估和提高自定义语音模型准确性。 如果选择测试准确度，则选择的声学数据集必须不同于你对自己的模型使用的数据集，这样才能获得真正有意义的模型性能。

## <a name="deploy-a-custom-model"></a>部署自定义模型

上传并检查数据、评估准确度以及训练自定义模型以后，即可部署可以与应用、工具和产品配合使用的自定义终结点。 

若要新建自定义终结点，请登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)，选择页面顶部“自定义语音识别”菜单中的“部署”。  如果是第一次运行，你会注意到表中未列出任何终结点。 创建终结点后，可以使用此页跟踪每个已部署的终结点。

接下来，选择“添加终结点”，并输入自定义终结点的 **名称** 和 **说明**。  然后选择要与此终结点关联的自定义模型。 也可以通过此页启用日志记录。 可以通过日志记录监视终结点流量。 在禁用的情况下，流量不存储。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 请勿忘记接受有关使用和定价详细信息的条款。

接下来，选择“创建”。  执行此操作后会返回到“部署”  页。 表中现在有自定义终结点的对应条目。 终结点的状态显示其当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态更改为“完成”  时，终结点便可供使用。

部署终结点后，其名称将以链接的形式显示。 单击此链接可显示特定于该终结点的信息，例如终结点密钥、终结点 URL 和示例代码。

## <a name="view-logging-data"></a>查看日志记录数据

可以在“终结点”>“详细信息”下下载日志记录数据。 
> [!NOTE]
>日志记录数据在 Microsoft 拥有的存储上可以使用 30 天，之后会被删除。 如果客户拥有的存储帐户已关联到认知服务订阅，则不会自动删除日志记录数据。

## <a name="next-steps"></a>后续步骤

* 了解 [如何使用自定义模型](how-to-specify-source-language.md)。

## <a name="additional-resources"></a>其他资源

- [准备和测试数据](./how-to-custom-speech-test-and-train.md)
- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)
