---
title: Azure 媒体服务 v2 与 v3 之间的术语和实体更改 |Microsoft Docs
description: 本文介绍 Azure 媒体服务 v2 与 v3 之间的术语差异。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9f657b853298cf0065da931835ea56ef2e0e0fe7
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690260"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>媒体服务 V2 和 V3 之间的术语和实体更改

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤2](./media/migration-guide/steps-2.svg)

本文介绍 Azure 媒体服务 v2 与 v3 之间的术语差异。

## <a name="naming-conventions"></a>命名约定

查看适用于媒体服务 V3 资源的命名约定。 另请参阅 [命名 blob](assets-concept.md#naming)

## <a name="terminology-changes"></a>术语更改

- *定位符* 现在称为 *流式处理定位符*。
- *通道* 现在称为 *实时事件*。
- *程序* 现在称为 *活动输出*。
- 现在， *任务* 称为 *JobOutput*，它是作业的一部分。

## <a name="entity-changes"></a>实体更改
| **V2 实体**<!-- row --> | **V3 实体** | **指南** | **可以访问 V3** | **已通过 V3 更新** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  此实体 `AccessPolicies` 在 V3 中不存在。 | 否 | 否 |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | 是 | 是 |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | 是 | 否 |
| `AssetFile`<!-- row --> | <!-- empty --> |此实体 `AssetFiles` 在 V3 中不存在。 尽管你上传)  (存储 blob 的文件仍被视为文件。<br/><br/> 请改用 Azure 存储 Api 来枚举容器中的 blob。 可以通过两种方法将转换应用于带有作业的文件：<br/><br/>已上传到存储的文件： URI 将包含要在存储帐户中的资产上完成的作业的资产 ID。<br/><br/>要在转换和作业过程中上载的文件：在存储中创建资产，返回 SAS URL，将文件上传到存储，然后将转换应用于文件。 | 否 | 否 |
| `Channel`<!-- row --> | `LiveEvent` | 实时事件替换 v2 API 中的通道。 它们具有更多的功能，并具有更多新功能，如 live 转录、备用模式以及对 RTMPS 引入的支持。 <br/><br/>查看 [基于方案的实时流式处理中的实时事件](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | 否 | 否 |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` 不再是实体，它现在是流式处理定位符的属性。<br/><br/>  在 v3 中，内容密钥数据要么与 `StreamingLocator` 用于输出加密) 的 (关联，要么与客户端存储加密)  (的资产本身相关联。 | 是 | 否 |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | 是 | 否 |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` 包含在中 `ContentKeyPolicy` 。 | 是 | 否 |
| `IngestManifest`<!-- row --> | <!-- empty --> | 此实体 `IngestManifests` 在 V3 中不存在。 在 V3 中上载文件涉及 Azure 存储 API。 首先创建资产，然后将文件上传到关联的存储容器。 可以通过多种方法将数据导入到 Azure 存储容器中，以供使用。 `JobInputHttp` 还提供了一种方法，用于从给定的 url 下载作业输入（如果需要）。 | 否 | 否 |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 可以通过多种方法将数据导入到 Azure 存储容器中，以供使用。 `JobInputHttp` 还提供了一种方法，用于从给定的 url 下载作业输入（如果需要）。 | 否 | 否 |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 可以通过多种方法将数据导入到 Azure 存储容器中，以供使用。 `JobInputHttp` 还提供了一种方法，用于从给定的 url 下载作业输入（如果需要）。 | 否 | 否 |
| `Job`<!-- row --> | `Job` | 创建 `Transform` 之前创建 `Job` 。 | 否 | 否 |
| `JobTemplate`<!-- row --> | `Transform` | 改为使用 `Transform` 。 转换是一种独立于作业的实体，它是 reuseable。 | 否 | 否 |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | 是 | 否 |
| `MediaProcessor`<!-- row --> | <!-- empty --> | `MediaProcessor`定义转换时，请使用所需预设，而不是按名称查找使用。 使用的预设将确定作业系统所使用的媒体处理器。 请参阅 [基于方案的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | 否 | NA (V2 中的 readonly)  |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3 中的通知通过 Azure 事件网格进行处理。 `NotificationEndpoint`被替换为事件网格订阅注册，该注册还会封装用于接收 (的通知类型的配置，该配置将在 v2 中由 `JobNotificationSubscription` 作业的、任务的 `TaskNotificationSubscription` 和遥测 `ComponentMonitoringSetting`) 处理。 V2 遥测在 Azure 事件网格和 Azure Monitor 之间剥离，以适应更大的 Azure 生态系统的增强功能。 | 否 | 否 |
| `Program`<!-- row --> | `LiveOutput` | 实时输出现在会替换 v3 API 中的程序。  | 否 | 否 |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | 流式处理终结点的主要目的是相同。 它们用于实时和按需流式处理实时和按需流式处理的 HLS 和短划线内容，或通过 CDN。 新功能包括对更好 Azure Monitor 集成和制作图表的支持。 |  是 | 是 |
| `Task`<!-- row --> | `JobOutput` | 被 `JobOutput` (替换为 API) 中不再是单独的实体。  请参阅 [基于方案的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 | 否 | 否 |
| `TaskTemplate`<!-- row --> | `TransformOutput` | 被 `TransformOutput` (替换为 API) 中不再是单独的实体。 请参阅 [基于方案的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 | 否 | 否 |
| `Inputs`<!-- row --> | `Inputs` | 输入和输出现在处于作业级别。 请参阅[基于方案编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)的编码主题 | 否 | 否 |
| `Outputs`<!-- row --> | `Outputs` | 输入和输出现在处于作业级别。  在 V3 中，元数据格式已从 XML 转换为 JSON。  实时输出在创建时启动，在删除后停止。 请参阅[基于方案编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)的编码主题 | 否 | 否 |


| **其他更改** | **V2**  | **V3** |
|---|---|---|
| **存储** <!--new row --> |||
| 存储 <!--new row --> | | V3 Sdk 现已与存储 SDK 分离，这使你可以更好地控制要使用的存储 SDK 版本，并避免版本控制问题。                      |
| **编码** <!--new row --> |||
| 编码比特率 <!--new row --> | 以 kbps 度量的比特率，例如： 128 (kbps) | 每秒位数，例如： 128000 (位/秒) |
| 编码 DRM FairPlay <!--new row --> | 在 Media Services V2 中，可以指定初始化向量 (IV) 。 | 在媒体服务 V3 中，无法指定 FairPlay IV。|
| 高级编码器 <!--new row --> | 高级编码器和旧索引器| [高级编码器](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset)和旧[媒体分析处理器](https://docs.microsoft.com/azure/media-services/previous/legacy-components) (Azure 媒体服务索引器2预览版、面部编修器等 ) 无法通过 V3 进行访问。 添加了对标准编码器的音频通道映射的支持。  请参阅 [媒体服务编码 Swagger 文档中的音频](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。  | 请参阅[基于方案编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)的编码主题 |
| **转换和作业** <!--new row -->|||
| 基于作业的处理 HTTPS <!--new row --> |<!-- empty -->| 对于基于文件的作业处理，可使用 HTTPS URL 作为输入。 不需要将内容存储在 Azure 中，也不需要创建资产。 |
| 作业的 ARM 模板 <!--new row --> | 版本2中不存在 ARM 模板。 | 转换可用于构建可重复使用的配置、创建 Azure 资源管理器模板，以及在多个客户或租户之间隔离处理设置。 |
| **实时事件** <!--new row --> |||
| 流式处理终结点 <!--new row --> | 流式传输终结点表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容分发网络 (CDN) 以进一步分发。 | 流式处理终结点的主要目的是相同。 它们用于实时和按需流式处理实时和按需流式处理的 HLS 和短划线内容，或通过 CDN。  新功能包括对更好 Azure Monitor 集成和制作图表的支持。 |
| 实时事件通道 <!--new row --> | 通道负责处理实时流内容。 通道提供输入终结点（引入 URL），并将该终结点提供给实时转码器。 通道从实时转码器接收实时输入流，并使其可用于通过一个或多个流式处理终结点进行流式处理。 频道还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。| 实时事件替换 v2 API 中的通道。 它们具有更多的功能，并具有更多新功能，如 live 转录、备用模式以及对 RTMPS 引入的支持。 |
| 实时事件程序 <!--new row --> | 节目用于控制实时流中片段的发布和存储。 频道管理节目。 频道和节目的关系类似于传统媒体，其中频道具有恒定的内容流，而节目的范围限定为该频道上的一些定时事件。 您可以通过设置属性来指定要保留节目录制内容的小时数 `ArchiveWindowLength` 。 此值的设置范围是最短 5 分钟，最长 25 小时。| 实时输出现在会替换 v3 API 中的程序。 |
| 实时事件长度 <!--new row --> |<!-- empty -->| 使用媒体服务时，可以流式传输实时事件24/7，将单比特率贡献源转换为具有多个比特率的输出流。|
| 实时事件延迟 <!--new row --> |<!-- empty -->| 实时事件的新低延迟实时流式处理支持。 |
| 实时事件预览 <!--new row --> |<!-- empty -->| 直播活动预览版支持动态打包和动态加密。 这样，用户便可以使用预览版中的内容保护以及 DASH 和 HLS 打包。 |
| 实时事件 RTMPS <!--new row --> |<!-- empty-->| 改进了 RTMPS 支持，同时增加了稳定性和更多源编码器支持。 |
| 实时事件 RTMPS 安全引入 <!--new row --> | | 当你创建一个实时事件时，会收到4个摄取 Url。 4个引入的 Url 几乎完全相同，具有相同的流标记 `AppId` ，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。| 
| 实时事件脚本 <!--new row --> |<!-- empty--> | Azure 媒体服务提供了不同协议中的视频、音频和文本。 使用 MPEG 破折号或 HLS/CMAF 发布实时流时，如果使用视频和音频，我们的服务将在 IMSC 1.1 兼容的 TTML 中提供转录文本。|
| 实时事件备用模式 <!--new row --> | V2 没有备用模式。 | 备用模式是一项新的 v3 功能，可帮助管理实时事件的热池。 现在，客户可以在将其转换为 "正在运行" 状态之前，以较低的成本在备用模式下启动实时事件。 这会缩短通道启动时间，并降低操作热池的成本以实现更快的启动。 |
| 实时事件计费 <!--new row --> | <!-- empty-->| 实时事件计费基于实时通道计量。 |
| 实时输出 <!--new row --> | 程序必须在创建后启动。 | 实时输出在创建时启动，在删除后停止。 |

## <a name="next-steps"></a>后续步骤

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
