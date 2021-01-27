---
title: 编码基于方案的迁移指南
description: 本文提供基于编码方案的指南，这些指南可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f8d25a14593c3c4a26ecafa33f7cc9fe6c4d580a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898352"
---
# <a name="encoding-scenario-based-migration-guidance"></a>编码基于方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-4.svg)

本文提供基于编码方案的指南，这些指南可帮助你从 Azure 媒体服务 v2 迁移到 v3。

## <a name="prerequisites"></a>必备条件

在更改编码工作流之前，建议您了解存储的管理方式上的差异。  在 AMS V3 中，Azure 存储 API 用于管理与媒体服务帐户关联的存储帐户 () 。

> [!NOTE]
> 在 v2 中创建的作业和任务不会显示在 v3 中，因为它们不与转换相关联。 建议切换到 v3 转换和作业。

## <a name="encoding-workflow-comparison"></a>编码工作流比较

请花几分钟时间查看下面的流程图，了解 V2 和 V3 编码工作流的直观比较。

### <a name="v2-encoding-workflow"></a>V2 编码工作流

单击下面的图像可查看更大的版本。

[![V2 ](./media/migration-guide/V2-pretty.svg) 的编码工作流](./media/migration-guide/V2-pretty.svg#lightbox)

1. 设置
    1. 创建资产或使用现有资产。 如果使用新资产，请将内容上传到该资产。 如果使用现有资产，则应该对资产中已经存在的文件进行编码。
    2. 获取以下项的值：
        - 媒体处理器 ID 或对象
        - 编码器字符串 (要使用的编码器的名称) 
        - 新资产的资产 ID 或现有资产的资产 ID
    3. 对于监视，创建作业或任务级通知订阅或 SDK 事件处理程序
2. 创建包含任务的作业。 每个任务都应包含上述项和：
    - 需要创建输出资产的指令。  输出资产由系统创建。
    - 输出资产的可选名称
3. 提交作业。
4. 监视作业。

### <a name="v3-encoding-workflow"></a>V3 编码工作流

[![V3 ](./media/migration-guide/V3-pretty.svg) 的编码工作流](./media/migration-guide/V3-pretty.svg#lightbox)

1. 开始参与
    1. 创建资产或使用现有资产。 如果使用新资产，请将内容上传到该资产。 如果使用现有资产，则应该对资产中已经存在的文件进行编码。 不 *应将更多内容上载到该资产。*
    1. 创建输出资产。  输出资产是指将在其中存储编码文件和输入元数据的位置。
    1. 获取转换的值：
        - 标准编码器预设
        - AMS 资源组
        - AMS 帐户名称
    1. 创建转换或使用现有转换。  转换可重复使用。 每次提交作业时，无需创建新的转换。
1. 创建作业
    1. 对于作业，获取以下项的值：
        - 转换名称
        - 资源的 SAS URL、文件共享的 HTTPs 源路径或文件的本地路径的基 URI。 `JobInputAsset`还可以使用资产名称作为输入。
        - 文件名 (s) 
        - 输出资产 (s) 
        - 资源组
        - AMS 帐户名称  
1. 使用 [事件网格](monitor-events-portal-how-to.md) 监视作业。
1. 提交作业。

## <a name="custom-presets-from-v2-to-v3-encoding"></a>自定义预设从 V2 到 V3 编码

如果你的 V2 代码使用自定义预设调用了标准编码器，则在提交作业之前，首先需要使用自定义标准编码器预设创建新的转换。

自定义预设现在是 JSON，不再基于 XML。 按照 [转换开放式 API (Swagger) ](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) 文档中定义的自定义预设架构，在 JSON 中重新创建预设。


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>输入和输出编码作业中的元数据文件

在 v2 中，XML 输入和输出元数据文件将作为编码作业的结果生成。 在 v3 中，元数据格式已从 XML 更改为 JSON。 有关元数据的详细信息，请参阅 [输入元数据](input-metadata-schema.md) 和 [输出元数据](output-metadata-schema.md)。

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>高级编码器到 v3 标准编码器或基于合作伙伴的解决方案

V2 API 不再支持高级编码器。 如果以前使用基于工作流的高级编码器进行 HEVC 编码，则应使用 HEVC 编码支持迁移到新的 v3 [标准编码器](media-encoder-standard-formats.md) 。

如果需要高级编码器的高级工作流功能，则建议你开始使用 [想象通信](https://imaginecommunications.com)、 [Telestream](https://www.telestream.net)或 [Bitmovin](https://bitmovin.com)中的 Azure 高级编码合作伙伴解决方案。

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>具有 HTTPS 托管 Url 上的输入的作业

你现在可以从存储在 "Azure 存储空间"、"本地存储" 或 "使用 HTTP (的外部 web 服务器" [) 作业输入支持](job-input-from-http-how-to.md)中的文件提交 V3 中的作业。

如果以前已使用工作流将文件从 Azure blob 文件复制到空的资源，则在提交作业之前，可以通过将 Azure blob 存储中的文件的 SAS URL 直接传递到作业来简化工作流。

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>索引器 v1 音频脚本到新 AudioAnalyzer "基本模式"

对于在 v2 API 中使用索引器 v1 处理器的客户，您需要创建一个 `AudioAnalyzer` 在提交作业之前在 " [基本" 模式下](how-to-create-basic-audio-transform.md) 调用新的转换。

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>编码、转换和作业概念、教程和操作方法指南

### <a name="concepts"></a>概念

- [使用媒体服务编码视频和音频](encoding-concept.md)
- [标准编码器格式和编解码器](media-encoder-standard-formats.md)
- [使用自动生成的比特率阶梯编码](autogen-bitrate-ladder.md)
- [使用内容感知编码预设来查找给定分辨率的最佳比特率值](content-aware-encoding.md)
- [媒体保留单位](concept-media-reserved-units.md)
- [输入元数据](input-metadata-schema.md)
- [输出元数据](output-metadata-schema.md)
- [媒体服务 v3 中的动态打包：音频编解码器](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>教程

- [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
- [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>操作方法指南

- [从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)
- [从本地文件创建作业输入](job-input-from-local-file-how-to.md)
- [创建基本音频转换](how-to-create-basic-audio-transform.md)
- （使用 .NET）
  - [如何对自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)
  - [如何使用 Media Encoder Standard 创建覆盖](how-to-create-overlay.md)
  - [如何使用编码器标准版通过 .NET 来生成缩略图](media-services-generate-thumbnails-dotnet.md)
- 使用 Azure CLI
  - [如何使用自定义转换进行编码 Azure CLI](custom-preset-cli-howto.md)
- 使用 REST
  - [如何对自定义转换进行编码 - REST](custom-preset-rest-howto.md)
  - [如何使用 Encoder Standard 通过 REST 来生成缩略图](media-services-generate-thumbnails-rest.md)
- [使用媒体服务编码时对视频进行子剪辑 - .NET](subclip-video-dotnet-howto.md)
- [使用媒体服务编码时对视频进行子剪辑 - REST](subclip-video-rest-howto.md)

## <a name="samples"></a>示例

你还可以 [在代码示例中比较 V2 和 V3 代码](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
