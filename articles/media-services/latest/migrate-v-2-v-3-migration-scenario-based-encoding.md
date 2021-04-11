---
title: 编码迁移指南
description: 本文提供基于编码方案的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: a01571f4a1f852deb84b7f20d61b8048e8000790
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490091"
---
# <a name="encoding-scenario-based-migration-guidance"></a>基于编码方案的迁移指南

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-4.svg)

本文提供基于编码方案的指南，可帮助你从 Azure 媒体服务 v2 迁移到 v3。

## <a name="prerequisites"></a>先决条件

在开始更改编码工作流之前，建议了解存储管理方式的区别。  在 AMS V3 中，Azure 存储 API 用于管理与媒体服务帐户关联的存储帐户。

> [!NOTE]
> 在 v2 中创建作业和任务不会在 v3 中显示，因为它们不与转换相关联。 建议切换到 v3 转换和作业。

## <a name="encoding-workflow-comparison"></a>编码工作流比较

请花几分钟时间查看下面的流程图，以直观地比较 V2 和 V3 的编码工作流。

### <a name="v2-encoding-workflow"></a>V2 编码工作流

单击下面的图像查看大图。

[![V2 编码工作流](./media/migration-guide/V2-pretty.svg) ](./media/migration-guide/V2-pretty.svg#lightbox)

1. 设置
    1. 创建资产或使用现有资产。 如果使用新资产，请将内容上传到该资产。 如果使用现有资源，则应该对资产中已存在的文件进行编码。
    2. 获取以下项的值：
        - 媒体处理器 ID 或对象
        - 要使用的编码器的编码器字符串（名称）
        - 新资产的资产 ID 或现有资产的资产 ID
    3. 对于监视，请创建作业或任务级别的通知订阅或 SDK 事件处理程序
2. 创建包含任务的作业。 每项任务应包括上述各项和：
    - 需要创建输出资产的指令。  输出资产由系统创建。
    - 输出资产的可选名称
3. 提交作业。
4. 监视作业。

### <a name="v3-encoding-workflow"></a>V3 编码工作流

[![V3 编码工作流](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. 设置
    1. 创建资产或使用现有资产。 如果使用新资产，请将内容上传到该资产。 如果使用现有资源，则应该对资产中已存在的文件进行编码。 不应将更多内容上传到该资产。
    1. 创建输出资产。  输出资产是编码文件以及输入和输出元数据的存储位置。
    1. 获取转换的值：
        - 标准编码器预设
        - AMS 资源组
        - AMS 帐户名称
    1. 创建转换或使用现有转换。  转换是可重用的。 每次要提交作业时，都不必创建新的转换。
1. 创建作业
    1. 为作业获取以下各项的值：
        - 转换名称
        - 资产的 SAS URL、文件共享的 HTTPs 源路径或文件的本地路径的基本 URI。 `JobInputAsset` 还可以使用资产名称作为输入。
        - 文件名
        - 输出资产
        - 资源组
        - AMS 帐户名称  
1. 使用[事件网格](monitoring/monitor-events-portal-how-to.md)来监视作业。
1. 提交作业。

## <a name="custom-presets-from-v2-to-v3-encoding"></a>从 V2 到 V3 编码的自定义预设

如果你的 V2 代码使用自定义预设调用了标准编码器，则在提交作业之前，首先需要使用自定义标准编码器预设创建新的转换。

自定义预设现在为 JSON，不再基于 XML。 按照[转换 Open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) 文档中定义的自定义预设架构，在 JSON 中重新创建预设。

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>编码作业中的输入和输出元数据文件

在 v2 中，XML 输入和输出元数据文件将作为编码作业的结果生成。 在 v3 中，元数据格式已从 XML 更改为 JSON。 有关元数据的详细信息，请参阅[输入元数据](input-metadata-schema.md)和[输出元数据](output-metadata-schema.md)。

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>高级编码器到 v3 标准编码器或基于合作伙伴的解决方案

v2 API 不再支持高级编码器。 如果以前使用基于工作流的高级编码器进行 HEVC 编码，则应迁移到支持 HEVC 编码的新 v3 [标准编码器](encode-media-encoder-standard-formats-reference.md)。

如果需要高级编码器的高级工作流功能，建议你开始使用来自 [Imagine Communications](https://imaginecommunications.com)、[Telestream](https://www.telestream.net) 或 [Bitmovin](https://bitmovin.com) 的 Azure 高级编码合作伙伴解决方案。

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>具有 HTTPS 托管 URL 上的输入的作业

现在，可以使用 [HTTP 作业输入支持](job-input-from-http-how-to.md)从存储在 Azure 存储、本地存储或外部 Web 服务器中的文件提交 V3 中的作业。

如果以前在提交作业之前使用工作流将文件从 Azure blob 文件复制到空资产中，则可以通过将 Azure blob 存储中的文件的 SAS URL 直接传递到作业中来简化工作流。

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>索引器 v1 音频听录到新 AudioAnalyzer“基本模式”

对于在 v2 API 中使用索引器 v1 处理器的客户，需要创建一个转换，以在提交作业之前以[基本模式](transform-create-basic-audio-how-to.md)调用新的 `AudioAnalyzer`。

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>编码、转换和作业概念、教程以及操作方法指南

### <a name="concepts"></a>概念

- [使用媒体服务编码视频和音频](encode-concept.md)
- [标准编码器格式和编解码器](encode-media-encoder-standard-formats-reference.md)
- [使用自动生成的比特率阶梯进行编码](encode-autogen-bitrate-ladder.md)
- [使用内容感知编码预设来查找给定分辨率的最佳比特率值](encode-content-aware-concept.md)
- [媒体保留单位](concept-media-reserved-units.md)
- [输入元数据](input-metadata-schema.md)
- [输出元数据](output-metadata-schema.md)
- [媒体服务 v3 中的动态打包：音频编解码器](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>教程

- [教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)
- [教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>操作方法指南

- [从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)
- [从本地文件创建作业输入](job-input-from-local-file-how-to.md)
- [创建基本音频转换](transform-create-basic-audio-how-to.md)
- （使用 .NET）
  - [如何对自定义转换进行编码 - .NET](transform-custom-presets-how-to.md)
  - [如何使用 Media Encoder Standard 创建覆盖](transform-create-overlay-how-to.md)
  - [如何使用编码器标准版通过 .NET 来生成缩略图](transform-generate-thumbnails-dotnet-how-to.md)
- 使用 Azure CLI
  - [如何对自定义转换进行编码 - Azure CLI](transform-custom-preset-cli-how-to.md)
- 使用 REST
  - [如何对自定义转换进行编码 - REST](transform-custom-preset-rest-how-to.md)
  - [如何使用 Encoder Standard 通过 REST 来生成缩略图](transform-generate-thumbnails-rest-how-to.md)
- [使用媒体服务编码时对视频进行子剪辑 - .NET](transform-subclip-video-dotnet-how-to.md)
- [使用媒体服务编码时对视频进行子剪辑 - REST](transform-subclip-video-rest-how-to.md)

## <a name="samples"></a>示例

还可[将代码示例中的 V2 和 V3 代码进行比较](migrate-v-2-v-3-migration-samples.md)。
