---
title: 媒体服务 v3 术语和实体更改
description: 本文介绍 Azure 媒体服务 v2 与 v3 之间的术语差异。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 42c4c91b6715ffec4c734632c69623206bc6dbae
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075996"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>媒体服务 V2 与 V3 之间的术语和实体更改

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-2.svg)

本文介绍 Azure 媒体服务 v2 与 v3 之间的术语差异。

## <a name="naming-conventions"></a>命名约定

查看适用于媒体服务 V3 资源的命名约定。 还要查看[命名 Blob](assets-concept.md#naming)

## <a name="terminology-changes"></a>术语更改

- 定位器现在称为流定位器 。
- 频道现在称为直播活动 。
- 节目现在称为实时输出 。
- 任务现在称为 JobOutput，它是作业的一部分 。

## <a name="entity-changes"></a>实体更改
| **V2 实体**<!-- row --> | **V3 实体** | **指南** | **可以访问 V3** | **已通过 V3 更新** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  V3 中不存在实体 `AccessPolicies`。 | 否 | 否 |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | 是 | 是 |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | 是 | 否 |
| `AssetFile`<!-- row --> | <!-- empty --> |V3 中不存在实体 `AssetFiles`。 尽管你上传的文件（存储 Blob）仍被视为文件。<br/><br/> 请改用 Azure 存储 API 来枚举容器中的 Blob。 可以通过两种方法将转换应用于带有作业的文件：<br/><br/>已上传到存储的文件：该 URI 将包含要在存储帐户中的资产上完成的作业的资产 ID。<br/><br/>要在转换和作业过程中上传的文件：在存储中创建资产，返回 SAS URL，将文件上传到存储，然后将转换应用于文件。 | 否 | 否 |
| `Channel`<!-- row --> | `LiveEvent` | 直播活动替换 v2 API 中的频道。 它们保留了大多数功能，并具有更多新功能，例如实时听录、待机模式以及对 RTMPS 引入的支持。 <br/><br/>请参阅[基于场景的实时传送视频流中的直播活动](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | 否 | 否 |
| `ContentKey`<!-- row --> | <!-- empty --> | 现在，`ContentKeys` 不再是实体，而是流定位器的一个属性。<br/><br/>  在 v3 中，内容密钥数据与 `StreamingLocator`（用于输出加密）或资产本身（用于客户端存储加密）相关联。 | 是 | 否 |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | 是 | 否 |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` 包含在 `ContentKeyPolicy` 中。 | 是 | 否 |
| `IngestManifest`<!-- row --> | <!-- empty --> | V3 中不存在实体 `IngestManifests`。 在 V3 中上传文件涉及 Azure 存储 API。 首先创建资产，然后将文件上传到关联的存储容器。 可以通过多种方法将数据导入到可以替代使用的 Azure 存储容器中。 `JobInputHttp` 还提供了一种方法，用于从给定的 URL 下载作业输入（如果需要）。 | 否 | 否 |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 可以通过多种方法将数据导入到可以替代使用的 Azure 存储容器中。 `JobInputHttp` 还提供了一种方法，用于从给定的 URL 下载作业输入（如果需要）。 | 否 | 否 |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 可以通过多种方法将数据导入到可以替代使用的 Azure 存储容器中。 `JobInputHttp` 还提供了一种方法，用于从给定的 URL 下载作业输入（如果需要）。 | 否 | 否 |
| `Job`<!-- row --> | `Job` | 创建 `Job` 之前，请创建 `Transform`。 | 否 | 否 |
| `JobTemplate`<!-- row --> | `Transform` | 改用 `Transform`。 转换是独立于作业的实体，并且可以重复使用。 | 否 | 否 |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | 是 | 否 |
| `MediaProcessor`<!-- row --> | <!-- empty --> | 定义转换时，请使用所需的预设，而不是按名称查找要使用的 `MediaProcessor`。 使用的预设将确定作业系统所使用的媒体处理器。 请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | 否 | NA（V2 中为只读） |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3 中的通知通过 Azure 事件网格进行处理。 `NotificationEndpoint` 被替换为事件网格订阅注册，该注册还会封装要接收的通知类型的配置（在 v2 中由作业的 `JobNotificationSubscription`、任务的 `TaskNotificationSubscription` 和遥测 `ComponentMonitoringSetting` 处理）。 V2 遥测在 Azure 事件网格和 Azure Monitor 之间进行了拆分，以适应更大的 Azure 生态系统的增强功能。 | 否 | 否 |
| `Program`<!-- row --> | `LiveOutput` | 实时输出现在会替换 v3 API 中的节目。  | 否 | 否 |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | 流式处理终结点基本上保持不变。 它们用于动态打包、加密和传递 HLS 和 DASH 内容，直接从原始来源或通过 CDN 进行实时和按需流式处理。 新功能包括对更好 Azure Monitor 集成和图表制作的支持。 |  是 | 是 |
| `Task`<!-- row --> | `JobOutput` | 替换为 `JobOutput`（不再是 API 中的单独实体）。  请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 | 否 | 否 |
| `TaskTemplate`<!-- row --> | `TransformOutput` | 替换为 `TransformOutput`（不再是 API 中的单独实体）。 请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题。 | 否 | 否 |
| `Inputs`<!-- row --> | `Inputs` | 输入和输出现在处于作业级别。 请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题 | 否 | 否 |
| `Outputs`<!-- row --> | `Outputs` | 输入和输出现在处于作业级别。  在 V3 中，元数据格式已从 XML 更改为 JSON。  实时输出在创建时启动，在删除后停止。 请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题 | 否 | 否 |


| **其他更改** | **V2**  | **V3** |
|---|---|---|
| **存储** <!--new row --> |||
| 存储 <!--new row --> | | V3 SDK 现在已与存储 SDK 分离，可让你更精细地控制所要使用的存储 SDK 版本，并避免版本控制问题。                      |
| **编码** <!--new row --> |||
| 编码比特率 <!--new row --> | 比特率（以 kbps 为单位），例如：128 (kbps)| 每秒位数，例如：128000（位/秒）|
| 编码 DRM FairPlay <!--new row --> | 在媒体服务 V2 中，可以指定初始化向量 (IV)。 | 在媒体服务 V3 中，无法指定 FairPlay IV。|
| 高级编码器 <!--new row --> | 高级编码器和旧索引器| [Premium Encoder](../previous/media-services-encode-asset.md) 和旧版[媒体分析处理器](../previous/legacy-components.md)（Azure 媒体服务索引器 2 预览版、Face Redactor 等）不可通过 V3 访问。 添加了对音频通道映射到标准编码器的支持。  请参阅[媒体服务编码 Swagger 文档中的音频](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)。  <br/> 请参阅[基于场景的编码](migrate-v-2-v-3-migration-scenario-based-encoding.md)中的编码主题 |
| **转换和作业** <!--new row -->|||
| 基于作业的处理 HTTPS <!--new row --> |<!-- empty -->| 对于基于文件的作业处理，可以使用 HTTPS URL 作为输入。 无需事先在 Azure 中存储内容，无需创建资产。 |
| 作业的 ARM 模板 <!--new row --> | V2 中不存在 ARM 模板。 | 使用转换可以生成可重用的配置、创建 Azure 资源管理器模板，并隔离多个客户或租户之间的处理设置。 |
| **直播活动** <!--new row --> |||
| 流式处理终结点 <!--new row --> | 流式传输终结点表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容分发网络 (CDN) 以进一步分发。 | 流式处理终结点基本上保持不变。 它们用于动态打包、加密和传递 HLS 和 DASH 内容，直接从原始来源或通过 CDN 进行实时和按需流式处理。  新功能包括对更好 Azure Monitor 集成和图表制作的支持。 |
| 直播活动频道 <!--new row --> | 频道负责处理实时传送视频流内容。 通道提供输入终结点（引入 URL），并将该终结点提供给实时转码器。 频道从实时转码器接收实时输入流，并通过一个或多个流式处理终结点使其可用于流式处理。 频道还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。| 直播活动替换 v2 API 中的频道。 它们保留了大多数功能，并具有更多新功能，例如实时听录、待机模式以及对 RTMPS 引入的支持。 |
| 直播活动节目 <!--new row --> | 节目用于控制实时流中片段的发布和存储。 频道管理节目。 频道和节目的关系类似于传统媒体，其中频道具有恒定的内容流，而节目的范围限定为该频道上的一些定时事件。 可以通过设置 `ArchiveWindowLength` 属性，指定希望保留节目录制内容的小时数。 此值的设置范围是最短 5 分钟，最长 25 小时。| 实时输出现在会替换 v3 API 中的节目。 |
| 直播活动长度 <!--new row --> |<!-- empty -->| 使用媒体服务将单比特率贡献源转码为具有多比特率的输出流时，可以全天候流式传输直播活动。|
| 直播活动延迟 <!--new row --> |<!-- empty -->| 直播活动支持新的低延迟实时传送视频流。 |
| 直播活动预览 <!--new row --> |<!-- empty -->| 直播活动预览版支持动态打包和动态加密。 这样，用户便可以使用预览版中的内容保护以及 DASH 和 HLS 打包。 |
| 直播活动 RTMPS <!--new row --> |<!-- empty-->| 改进的 RTMPS 支持（提高了稳定性并提供了更多的源编码器支持）。 |
| 直播活动 RTMPS 安全引入 <!--new row --> | | 创建直播活动时，将获得 4 个引入 URL。 这 4 个引入 URL 几乎是相同的，具有相同的流式处理令牌 `AppId`，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。| 
| 实时事件听录 <!--new row --> |<!-- empty--> | Azure 媒体服务提供了不同协议的视频、音频和文本。 如果使用 MPEG-DASH 或 HLS/CMAF 发布实时传送流，并包含视频和音频，我们的服务将以兼容 IMSC 1.1 的 TTML 格式提供听录文本。|
| 直播活动待机模式 <!--new row --> | V2 没有待机模式。 | 待机模式是一项新的 v3 功能，可帮助管理直播活动的热池。 客户现在可以较低的成本在待机模式下启动直播活动，然后再将其转换为运行状态。 这会缩短频道启动时间，并降低操作热池以加快启动速度的成本。 |
| 直播活动计费 <!--new row --> | <!-- empty-->| 直播活动计费基于实时频道计量器。 |
| 实时输出 <!--new row --> | 节目必须在创建后启动。 | 实时输出在创建时启动，在删除后停止。 |
