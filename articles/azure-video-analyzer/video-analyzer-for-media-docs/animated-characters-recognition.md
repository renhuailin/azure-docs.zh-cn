---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）进行动画字符检测
description: 本主题演示如何通过 Azure 视频分析器媒体版（以前称为视频索引器）进行动画字符检测。
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: d12b7a1911bb0c63d7f546a8b7a30e0287a05759
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610591"
---
# <a name="animated-character-detection-preview"></a>动画字符检测（预览版）

Azure 视频分析器媒体版（以前称为视频索引器）通过与[认知服务自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成来支持检测、分组和识别动画内容中的字符。 此功能可通过门户和 API 使用。

使用特定动画模型上传动画视频后，视频分析器媒体版将提取关键帧，检测这些帧中的动画字符，将类似字符分组，然后选择最佳示例。 然后，它将分组字符发送到自定义视觉，以根据它所定型的模型来标识字符。 

在开始对模型进行定型之前，将匿名检测字符。 添加名称和对模型定型时，视频分析器媒体版将识别这些字符并对它们进行相应的命名。

## <a name="flow-diagram"></a>流程图

下图演示了动画字符检测过程流。

![流程图](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帐户

根据视频分析器媒体版帐户的类型，可以使用不同的功能集。 要了解如何将帐户连接到 Azure，请参阅[创建连接到 Azure 的视频分析器媒体版帐户](connect-to-azure.md)。

* 试用帐户：视频分析器媒体版使用内部自定义视觉帐户创建模型并将其连接到视频分析器媒体版帐户。 
* 付费帐户：将自定义视觉帐户连接到视频分析器媒体版帐户（如果还没有帐户，则需要先创建帐户）。

### <a name="trial-vs-paid"></a>试用版和付费版

|功能|试用|已付|
|---|---|---|
|自定义视觉帐户|由视频分析器媒体版在后台进行管理。 |自定义视觉帐户连接到视频分析器媒体版。|
|动画模型的数目|一个|每个帐户最多 100 个模型（自定义视觉限制）。|
|训练模型|视频分析器媒体版对现有字符的新字符其他示例的模型进行训练。|当帐户所有者准备好进行更改时，会对该模型进行定型。|
|自定义视觉中的高级选项|无自定义视觉门户访问权限。|可以在“自定义视觉”门户中自行调整模型。|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>通过门户和 API 使用动画字符检测

有关详细信息，请参阅[通过门户和 API 使用动画字符检测](animated-characters-recognition-how-to.md)。

## <a name="limitations"></a>限制

* 目前，东亚区域不支持“动画识别”功能。
* 如果视频质量较差，可能无法正确识别在视频中看起来较小或较远的人物。
* 建议对每组动画人物（例如每个动画序列）使用一个模型。

## <a name="next-steps"></a>后续步骤

[视频分析器媒体版概述](video-indexer-overview.md)
