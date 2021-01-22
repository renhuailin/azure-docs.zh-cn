---
title: Microsoft Azure 媒体服务常见方案 |Microsoft Docs
description: 本文概述 Microsoft Azure 媒体服务方案。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: d195ad6715c47b9b4c14dc2e65ba1d07ebf79ce8
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696255"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure 媒体服务常见方案

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 查看最新版本：[媒体服务 v3](../latest/media-services-overview.md)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-v-2-v-3-migration-introduction.md)

可以使用 Microsoft Azure 媒体服务 (AMS) 安全地上传、存储、编码和打包视频或音频内容，以便通过点播和实时流形式传送到各种客户端（例如，电视、电脑和移动设备）。

本文演示了实时传递内容或点播传递内容的常见方案。

## <a name="overview"></a>概述

### <a name="prerequisites"></a>先决条件

* 一个 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com)。
* Azure 媒体服务帐户。 有关详细信息，请参阅[创建帐户](media-services-portal-create-account.md)。
* 要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 

    创建 AMS 帐户后，系统会将一个处于“已停止”状态的 **默认** 流式处理终结点添加到帐户。  若要开始流式传输内容并利用动态打包和动态加密，流式处理终结点必须处于“正在运行”状态。 

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>针对 AMS OData 模型进行开发时的常用对象

针对媒体服务 OData 模型进行开发时，以下图像会显示某些最常用的对象。

单击图像查看其完整大小。  

[![显示针对 Azure 媒体服务对象数据模型进行开发时的某些最常用对象的关系图。](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

可在 [此处](https://media.windows.net/API/$metadata?api-version=2.15)查看完整模型。  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>保护存储中的内容并以明文（非加密）形式交付流式处理媒体

![VoD 工作流](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 将优质媒体文件上传到资产中。

    建议向资产应用存储加密选项，以便在内容上传期间和内容在存储中处于静态时，为其提供保护。

1. 编码为一组自适应比特率 MP4 文件。

    建议向输出资产应用存储加密选项，以便保护静态内容。

1. 配置资产传送策略（由动态打包使用）。

    如果资产已经过存储加密，则 **必须** 配置资产传送策略。
1. 通过创建 OnDemand 定位符发布资产。
1. 流式传输已发布的内容。

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>在存储中保护内容，并以动态方式交付加密的流媒体

![使用 PlayReady 进行保护](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 将优质媒体文件上传到资产中。 向资产应用存储加密选项。
1. 编码为一组自适应比特率 MP4 文件。 向输出资产应用存储加密选项。
1. 为播放期间想要动态加密的资产创建加密内容密钥。
1. 配置内容密钥授权策略。
1. 配置资产传送策略（由动态打包和动态加密使用）。
1. 通过创建 OnDemand 定位符发布资产。
1. 流式传输已发布的内容。

## <a name="deliver-progressive-download"></a>提供渐进式下载

1. 将优质媒体文件上传到资产中。
1. 编码为单个 MP4 文件。
1. 通过创建 OnDemand 或 SAS 定位符来发布资产。 如果使用 SAS 定位符，将从 Azure Blob 存储中下载内容。 不需要让流式处理终结点处于已启动状态。
1. 渐进式下载内容。

## <a name="delivering-live-streaming-events"></a>传送实时传送视频流事件

1. 使用多种实时传送视频流协议（例如 RTMP 或平滑流式处理）引入实时内容。
1. （可选）将流编码为自适应比特率流。
1. 预览实时流。
1. 通过以下方式传递内容：
    1. 常用流式处理协议 (例如，将 MPEG 破折号、平滑、HLS) 直接传递给客户，
    1.  (CDN) 的内容交付网络用于进一步分发，或
    1. 记录并存储引入的内容，以便稍后进行流式处理（视频点播）。

执行实时传送视频流时，可以选择以下路由之一：

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>使用从本地编码器（直通）接收多比特率实时流的频道

下图显示的是直通工作流中涉及的 AMS 平台的主要组成部分  。

![显示“直通”工作流中涉及的 AMS 平台的主要组成部分的图示。](./media/scenarios-and-availability/media-services-live-streaming-current.png)

有关详细信息，请参阅[使用从本地编码器接收多比特率实时流的频道](media-services-live-streaming-with-onprem-encoders.md)。

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>使用能够通过 Azure 媒体服务执行实时编码的频道

下图显示的是实时流式处理工作流中涉及的 AMS 平台的主要组成部分，该工作流中的频道能够通过媒体服务执行实时编码。

![实时工作流](./media/scenarios-and-availability/media-services-live-streaming-new.png)

有关详细信息，请参阅[使用能够通过 Azure 媒体服务执行实时编码的频道](media-services-manage-live-encoder-enabled-channels.md)。

## <a name="consuming-content"></a>使用内容

Azure 媒体服务提供所需的工具，以便创建适用于大多数平台的丰富、动态的客户端播放器应用程序，这些平台包括：iOS 设备、Android 设备、Windows、Windows Phone、Xbox 和机顶盒。

## <a name="enabling-azure-cdn"></a>启用 Azure CDN

媒体服务支持与 Azure CDN 集成。 有关如何启用 Azure CDN 的信息，请参阅 [如何在媒体服务帐户中管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)。

## <a name="scaling-a-media-services-account"></a>缩放媒体服务帐户

AMS 客户可以缩放其 AMS 帐户中的流式处理终结点、媒体处理和存储。

* 媒体服务客户可以选择“标准”  或“高级”  流式处理终结点。 “标准”流式处理终结点适用于大多数流式处理工作负荷。  它拥有与“高级”流式处理终结点相同的功能，且可以自动缩放出站带宽。 

    “高级”  流式处理终结点适用于高级工作负荷，可提供专用且可缩放的带宽容量。 默认情况下，使用“高级”  流式处理终结点的客户会获得一个流式处理单位 (SU)。 可通过添加 SU 来缩放流式处理终结点。 每个 SU 为应用程序提供额外的带宽容量。 若要详细了解如何缩放“高级”  流式处理终结点，请参阅[缩放流式处理终结点](media-services-portal-scale-streaming-endpoints.md)主题。

* 媒体服务帐户与预留单位类型关联，后者决定了编码处理任务的处理速度。 可以在以下预留单位类型中进行选择：**S1**、**S2** 或 **S3**。 例如，与 **S1** 预留单位类型相比，使用 **S2** 预留单位类型时，同一编码作业运行速度更快。

    除了指定预留单位类型，还可以指定为帐户预配预留单位  (RU)。 预配的 RU 数决定了给定帐户中可并发处理的媒体任务数。

    > [!NOTE]
    > RU 可用于并行化所有媒体处理，包括使用 Azure Media Indexer 为作业编制索引。 但是，与编码不同，索引作业使用更快的预留单位并不能更快地完成处理。

    有关详细信息，请参阅[缩放媒体处理](media-services-portal-scale-media-processing.md)。

* 也可以通过向媒体服务帐户添加存储帐户来缩放该帐户。 每个存储帐户大小限制为 500 TB。 有关详细信息，请参阅[管理存储帐户](./media-services-managing-multiple-storage-accounts.md)。

## <a name="next-steps"></a>后续步骤

[迁移到媒体服务 v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]