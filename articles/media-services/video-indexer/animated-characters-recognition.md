---
title: 视频索引器的动画字符检测
titleSuffix: Azure Media Services
description: 本主题演示如何通过视频索引器使用动画字符检测。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854744"
---
# <a name="animated-character-detection-preview"></a>动画字符检测（预览版）

Azure 媒体服务视频索引器支持通过与 [认知服务自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成来检测、分组和识别动画内容中的字符。 此功能可通过门户和 API 使用。

使用特定动画模型上传动画视频后，视频索引器将提取关键帧，检测这些帧中的动画字符，将类似字符分组，然后选择最佳示例。 然后，它将分组字符发送到自定义视觉，以根据它所定型的模型来标识字符。 

在开始对模型进行定型之前，将在 namelessly 中检测到字符。 添加名称和训练模型时，视频索引器将识别这些字符并对它们进行相应的命名。

## <a name="flow-diagram"></a>流程图

下图演示了动画字符检测过程的流动。

![流程图](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帐户

根据视频索引器帐户的类型，可以使用不同的功能集。 若要了解如何将帐户连接到 Azure，请参阅 [创建连接到 azure 的视频索引器帐户](connect-to-azure.md)。

* 试用帐户：视频索引器使用内部自定义视觉帐户创建模型并将其连接到视频索引器帐户。 
* 付费帐户：将自定义视觉帐户连接到视频索引器帐户 (如果你还没有帐户，则需要先) 创建帐户。

### <a name="trial-vs-paid"></a>试用版和付费版

|功能|试用|已付|
|---|---|---|
|自定义视觉帐户|由视频索引器在后台进行管理。 |自定义视觉帐户已连接到视频索引器。|
|动画模型的数目|一个|每个帐户最多100个型号 (自定义视觉限制) 。|
|训练模型|视频索引器训练新字符的模型其他现有字符的示例。|当客户准备好进行更改时，帐户所有者训练该模型。|
|自定义视觉中的高级选项|无权访问自定义视觉门户。|您可以在自定义视觉门户中自行调整模型。|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>通过门户和 API 使用动画字符检测

有关详细信息，请参阅对 [门户和 API 使用动画字符检测](animated-characters-recognition-how-to.md)。

## <a name="limitations"></a>限制

* 目前 East-Asia 区域中不支持 "动画标识" 功能。
* 如果视频质量较差，可能无法正确识别在视频中显示较小或更多的字符。
* 建议使用每组动画字符集的模型， (例如每个动画系列) 。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)