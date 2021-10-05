---
title: 提交作业时使用预设替代来更改转换设置
description: 本文介绍如何使用预设替代来调整每个作业的实例上的转换设置
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699092"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>使用预设替代来控制每个作业的设置

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>转换和作业概述

若要使用媒体服务 v3 进行编码，需创建[转换](/rest/api/media/transforms)和[作业](/rest/api/media/jobs)。 转换用于定义编码设置和输出的配方，作业则是该配方的一个实例。 有关详细信息，请参阅[转换和作业](transform-jobs-concept.md)。

在媒体服务中编码或使用分析时，可以在转换中定义自定义的预设，以定义要使用的设置。 有时需要按作业替代有关某个转换的设置，这样就不必为每种方案都创建自定义转换。 若要替代转换预设上的任何设置，可以在将作业提交到转换之前使用[作业输出资产](/dotnet/api/microsoft.azure.management.media.models.joboutputasset)的预设替代属性。

## <a name="preset-overrides"></a>预设替代

预设替代使你能够传入自定义的预设，在首次创建转换对象后，该预设会替代提供给该对象的设置。  将新作业提交到转换时，此属性在[作业输出资产](/dotnet/api/microsoft.azure.management.media.models.joboutputasset)上可用。

如果你需要替代自定义转换的某些属性或内置预设上的属性，此功能可能很有用。 例如，假设你创建了使用[音频分析器内置预设](/rest/api/media/transforms/create-or-update#audioanalyzerpreset)的自定义转换，但最初将该预设设置为使用“en-us”（英语）音频语言设置。  这会生成一个将提交的每个作业仅作为美国英语版本发送到语音转文本听录引擎的转换。 提交到该转换的每个作业将锁定为“en-us”语言设置。 可以通过为每种语言定义一个转换来解决此问题，但这种方法的管理难度要大得多，并且可能会达到帐户中的转换配额限制。
若要以最好的办法解决此问题，可以在将作业提交到转换之前在作业输出资产上使用预设替代。  然后可以定义单个“音频听录”转换，并传入每个作业所需的语言设置。

通过预设替代可以在每个作业提交到转换时传入新的自定义预设定义。 此属性在基于 API 版本 2021-06-01 的所有 SDK 版本中的[作业输出](/dotnet/api/microsoft.azure.management.media.models.joboutput)实体上可用。

有关参考，请参阅 REST 文档中作业输出实体上的 [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) 属性。

> [!NOTE]
> 只能使用预设替代来替代转换中定义的预设上的设置。  无法从一个特定的预设切换到另一种类型。 例如，如果尝试替代使用内置内容感知编码预设创建的转换以使用另一个预设（例如音频分析器），则会导致出现错误消息。


## <a name="example-of-preset-override-in-net"></a>以 .NET 编写的预设替代示例

Github 中提供了一个使用适用于媒体服务的 .NET SDK 的完整示例，其中演示了如何将预设替代与基本音频分析器转换结合使用。
有关如何使用作业输出的预设替代属性的详细信息，请参阅[使用音频分析器预设分析媒体文件](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)示例。

## <a name="sample-code-of-preset-override-in-net"></a>以 .NET 编写的预设替代示例代码

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务上传、编码和流式传输](stream-files-tutorial-with-api.md)。
* [使用内置预设从 HTTPS URL 进行编码](job-input-from-http-how-to.md)。
* [使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)。
* [构建自定义预设，以确定特定方案或设备要求](transform-custom-presets-how-to.md)。
