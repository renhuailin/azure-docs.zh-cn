---
title: 检查自定义语音识别的数据质量 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 你可以播放上传的音频，确定提供的识别结果是否正确。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: lajanuar
ms.openlocfilehash: f7e9d16584e19bb54eb14500ae67553f9557e60a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066515"
---
# <a name="inspect-custom-speech-data"></a>检查自定义语音识别数据

> [!NOTE]
> 此页假定你已阅读[准备自定义语音识别的测试数据](./how-to-custom-speech-test-and-train.md)，并已上传用于检查的数据集。

自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 在 [Speech Studio](https://speech.microsoft.com/customspeech) 中，你可以播放上传的音频，确定提供的识别结果是否正确。 此工具可帮助检查 Microsoft 的基线语音转文本模型的质量，检查经过训练的自定义模型，或者比较两个模型的听录内容。

本文介绍了如何直观地检查 Microsoft 的基线语音转文本模型和/或经过训练的自定义模型的质量。 另外还介绍了如何使用联机听录内容编辑器来创建和优化经过标记的音频数据集。

## <a name="create-a-test"></a>创建测试

按这些说明创建测试：

1. 登录 [Speech Studio](https://speech.microsoft.com/customspeech)。
2. 导航到“语音转文本”>“自定义语音识别”> [项目名称] >“测试”。
3. 单击“添加测试”。
4. 选择“检查质量(仅音频数据)”。 为测试提供名称和说明，然后选择你的音频数据集。
5. 选择最多两个要测试的模型。
6. 单击 **创建**。

成功创建测试后，你可以查看模型对指定的音频数据集的转录方式，或将两个模型中的结果并行进行比较。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>并排进行模型比较

当测试状态为“成功”时，请单击测试项名称，查看测试详细信息。 此详细信息页列出了数据集中的所有话语，并显示了要比较的两个模型的识别结果。

可以通过切换各种错误类型（包括插入、删除和替换）来查看并排比较的结果。 通过听音频并比较每个列（显示人为标记的听录和两个语音转文本模型的结果）中的识别结果，你可以确定哪个模型符合自己的需求，以及需要在哪些方面进行改进。

并行模型测试可用于验证哪个语音识别模型最适合应用程序。 若要客观度量准确度，需对音频进行听录，并按[评估准确度](how-to-custom-speech-evaluate-data.md)中的说明操作。

## <a name="online-transcription-editor"></a>联机听录内容编辑器

使用联机听录内容编辑器，可以轻松地在自定义语音识别中处理音频听录内容。 该编辑器的主要用例如下： 

* 你只有音频数据，但需要从头开始构建准确的音频和人为标记的数据集，以便用于模型训练。
* 你已经有了音频和人为标记的数据集，但听录内容中存在错误或瑕疵。 利用该编辑器，可以快速修改听录内容，从而最大程度地提高训练准确度。

对于使用听录内容编辑器，唯一的要求是要上传音频数据（可以只上传音频，或者也可以将音频和听录内容都上传）。

### <a name="import-datasets-to-editor"></a>将数据集导入到编辑器

若要将数据导入到编辑器中，请先导航到“自定义语音识别”>“[你的项目]”>“编辑器”。

![编辑器选项卡](media/custom-speech/custom-speech-editor-detail.png)

接下来，使用以下步骤来导入数据。

1. 单击“导入数据”
1. 创建新数据集并为其添加说明
1. 选择数据集。 支持选择多项，你可以只选择音频数据，也可以选择音频和人为标记的数据。
1. 如果只有音频数据，可以选择使用默认模型，以便在导入到编辑器后自动生成计算机听录内容
1. 单击“导入” 

成功导入数据后，可单击数据集并开始编辑。

> [!TIP]
> 也可以通过选择数据集并单击“导出到编辑器”，直接将数据集导入到编辑器中

### <a name="edit-transcription-by-listening-to-audio"></a>通过倾听音频来编辑听录内容

数据上传成功后，请单击各项的名称来查看数据的详细信息。 也可以使用“上一个”和“下一个”在各个文件之间移动 。

详细信息页会列出每个音频文件中的所有句段，并且你可以单击所需的言语。 对于每一条言语，可以播放音频并检查听录内容，如果发现有任何插入、删除或替换方面的错误，可以编辑听录内容。 有关错误类型的详细信息，请参阅[数据评估操作方法](how-to-custom-speech-evaluate-data.md)。

![编辑器页](media/custom-speech/custom-speech-editor.png)

在完成了编辑之后，请单击“保存”按钮。

### <a name="export-datasets-from-the-editor"></a>从编辑器导出数据集

若要将数据集导出到“数据”选项卡，请导航到数据详细信息页并单击“导出”按钮，以将所有文件导出为新的数据集 。 也可以按上次编辑时间、音频持续时间等条件对这些文件进行筛选，从而部分选择所需要的文件。 

![导出数据](media/custom-speech/custom-speech-editor-export.png)

导出到“数据”的文件将会用作全新的数据集，并且不会影响任何现有的数据/训练/测试实体。

## <a name="next-steps"></a>后续步骤

- [评估数据](how-to-custom-speech-evaluate-data.md)
- [训练模型](how-to-custom-speech-train-model.md)
- [改进模型](./how-to-custom-speech-evaluate-data.md)
- [部署模型](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>其他资源

- [准备自定义语音识别的测试数据](./how-to-custom-speech-test-and-train.md)