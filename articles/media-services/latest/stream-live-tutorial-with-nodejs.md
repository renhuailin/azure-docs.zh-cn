---
title: 通过媒体服务 v3 Node.js 进行实时流式传输
titleSuffix: Azure Media Services
description: 了解如何使用 Node.js 进行实时流式传输。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730194"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>教程：使用 Node.js 和 TypeScript 通过媒体服务进行实时流式传输

> [!NOTE]
> 尽管本教程使用了 Node.js 示例，但 [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。 

在 Azure 媒体服务中，[直播活动](/rest/api/media/liveevents)负责处理实时传送视频流内容。 直播活动提供输入终结点（引入 URL），然后由你将该终结点提供给实时编码器。 直播活动从实时编码器接收实时输入流，并通过一个或多个[流式处理终结点](/rest/api/media/streamingendpoints)使其可用于流式处理。 直播活动还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。 本教程演示如何使用 Node.js 来创建“直通”类型的实时事件，并使用 [OBS Studio](https://obsproject.com/download) 将实时传送流广播到该事件。

本教程介绍如何：

> [!div class="checklist"]
> * 下载本主题中所述的示例代码。
> * 检查配置和执行实时传送视频流的代码。
> * 使用 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 在 [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) 观看活动。
> * 清理资源。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

以下项目是完成本教程所需具备的条件：

- 安装 [Node.js](https://nodejs.org/en/download/)
- 安装 [TypeScript](https://www.typescriptlang.org/)
- [创建媒体服务帐户](./create-account-howto.md)。<br/>请务必记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 你将需要使用它们来访问 API 并配置环境变量文件。
- 首先浏览[使用 Node.js 进行配置和连接](./configure-connect-nodejs-howto.md)操作说明，了解如何使用 Node.js 客户端 SDK
- 安装 Visual Studio Code 或 Visual Studio。
- [设置 Visual Studio Code 环境](https://code.visualstudio.com/Docs/languages/typescript)以支持 TypeScript 语言。

## <a name="additional-settings-for-live-streaming-software"></a>实时传送视频流软件的其他设置

- 一个用于广播事件的相机或设备（例如便携式计算机）。
- 使用 RTMP 协议对照相机流进行编码并将其发送到媒体服务实时流服务的本地软件编码器，请参阅[推荐的本地实时编码器](encode-recommended-on-premises-live-encoders.md)。 流必须为 **RTMP** 或“平滑流式处理”  格式。  
- 对于本示例，建议从软件编码器（如免费的 [Open Broadcast Software OBS Studio](https://obsproject.com/download)）开始，以便于上手。

此示例假设你将使用 OBS Studio 将 RTMP 广播到引入终结点。 首先安装 OBS Studio。
在 OBS Studio 中使用以下编码设置：

- 编码器：NVIDIA NVENC（如果可用）或 x264
- 速率控制：CBR
- 比特率：2500 Kbps（或适合你的笔记本电脑的速率）
- 关键帧间隔：2 秒；若要获得低延迟，则为 1 秒  
- 预设：低延迟质量或性能 (NVENC)，或使用 x264 的“非常快”
- 配置文件：高
- GPU：0 (自动)
- 最大双向预测帧：2

> [!TIP]
> 确保在继续操作之前查看[使用媒体服务 v3 的实时传送视频流](stream-live-streaming-concept.md)。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将以下包含实时传送视频流 Node.js 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

实时传送视频流示例位于 [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) 文件夹中。

在 [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) 文件夹中，将名为“sample.env”的文件复制到名为“.env”的新文件中，以存储在[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 一文中收集的环境变量设置。
请确保该文件的 .env 中包含“点”(.)，以使其正确用于代码示例。

[.env 文件](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env)包含 AAD 应用程序密钥和机密，以及对媒体服务帐户的 SDK 访问进行身份验证所需的帐户名称和订阅信息。 已配置 .gitignore 文件以避免将此文件发布到分叉存储库。 不要泄露这些凭据，因为它们是帐户的重要机密。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则帐户中会有多个直播活动。 <br/>请务必停止正在运行的直播活动， 否则，将会对你“收费”  ！ 在完成前一直运行程序，以便自动清理资源。 如果程序崩溃，或无意停止了调试器并中断了程序执行，则应仔细检查门户，确认未将任何实时事件置于“正在运行”或“等待”状态，避免产生意外的账单费用。

## <a name="examine-the-typescript-code-for-live-streaming"></a>检查 TypeScript 代码以实时传送视频流

此部分研究 Live 项目的 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) 文件中定义的函数。

此示例为每个资源创建唯一的后缀，因此即使在没有清理的情况下运行示例多次，也不会有名称冲突。

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>开始对 Node.js 和 TypeScript 使用媒体服务 SDK

若要开始对 Node.js 使用媒体服务 API，需要先使用 npm 包管理器添加 [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK 模块

```bash
npm install @azure/arm-mediaservices
```

package.json 中已配置了此项，因此只需运行 npm install 即可加载模块和依赖项。

1. 打开“命令提示符”，浏览到示例的目录。
1. 将目录更改为 AMSv3Samples 文件夹。

    ```bash
    cd AMSv3Samples
    ```

1. 安装 packages.json 文件中使用的包。

    ```bash
    npm install 
    ```

1. 从 AMSv3Samples 文件夹中启动 Visual Studio Code。 （需要从 .vscode 文件夹和 tsconfig.json 文件所在的文件夹中启动 。）

    ```bash
    cd ..
    code .
    ```

打开 Live 的文件夹，然后在 Visual Studio Code 编辑器中打开 index.ts 文件 。
在 index.ts 文件中，按 F5 启动调试器。

### <a name="create-the-media-services-client"></a>创建媒体服务客户端

以下代码片段演示了如何在 Node.js 中创建媒体服务客户端。
请注意，在此代码中，我们首先将 AzureMediaServicesOptions 的 longRunningOperationRetryTimeout 属性设置为 2 秒，以减少在 Azure 资源管理终结点上轮询长期运行操作的状态所花的时间。  由于实时事件的大多数操作将是异步操作，并且可能需要一段时间才能完成，因此应将 SDK 上的此轮询间隔减少到默认值 30 秒，以缩减完成主要操作（如创建实时事件、启动和停止）所需的时间，这些操作都是异步调用。 对于大多数用例方案，建议值均为 2 秒。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>创建直播活动

本部分介绍如何创建 **直通** 类型的实时事件（LiveEventEncodingType 设置为 None）。 有关实时事件的其他可用类型的详细信息，请参阅[实时事件类型](live-event-outputs-concept.md#live-event-types)。 除了直通，还可以使用实时转码直播活动进行 720P 或 1080P 自适应比特率云编码。
 
可能需要在创建直播活动时指定的一些事项包括：

* 直播活动的引入协议（目前支持 RTMP 和平滑流式处理协议）。<br/>运行直播活动或其关联的实时输出时，无法更改协议选项。 如果需要其他协议，请为每个流式处理协议创建单独的直播活动。  
* 对引入和预览的 IP 限制。 可定义允许向该直播活动引入视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。<br/>如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建规则并设置 0.0.0.0/0。<br/>IP 地址必须采用以下格式之一：具有四个数字或 CIDR 地址范围的 IpV4 地址。
* 创建事件时，可以将其启动方式指定为自动启动。 <br/>如果将 autostart 设置为 true，则直播活动会在创建后启动。 这意味着，只要直播活动开始运行，就会开始计费。 必须显式对直播活动资源调用停止操作才能停止进一步计费。 有关详细信息，请参阅[直播活动状态和计费](live-event-states-billing-concept.md)。
此外还有备用模式，可用于以较低成本“已分配”状态启动直播活动，使其更快地移动到“正在运行”状态。 对于需要快速向流式处理器分发通道的热池等情况，这非常有用。
* 要使引入 URL 具有预测性且更易于在基于硬件的实时编码器中维护，请将“useStaticHostname”属性设置为 true，并在“accessToken”中使用自定义的唯一 GUID。 有关详细信息，请参阅[实时事件引入 URL](live-event-outputs-concept.md#live-event-ingest-urls)。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>创建用于录制和存档实时事件的“资产”

在此代码块中，你将创建一个空“资产”，用作录制实时事件存档的“磁带”。
在了解这些概念时，最好将“资产”对象视为过去你插入录像机的磁带。 “实时输出”是录像机。 “实时输出”只是进入机器后部的视频信号。

请记住，可以随时创建该“资产”或“磁带”。 这只是一个空“资产”，你会将其交给“实时输出”对象，即此类比中的录像机。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>创建实时输出

在本部分，我们将创建一个实时输出，该输出使用资产名称作为输入，以告知要将实时事件录制到的位置。 此外，还将设置要在录制内容中使用的时移 (DVR) 窗口。
示例代码演示如何设置 1 小时的时移窗口。 这将允许客户端在事件后一小时内在任意位置播放事件。  此外，只有过去 1 小时的实时事件会保留在存档中。 如果需要，可以最多将其延长到 25 个小时。  另请注意，在发布时，你可以控制 URL 路径中的 HLS 和 DASH 清单使用的输出清单命名。

还可以随时创建实时输出，也就是我们比喻的“磁带录像机”。 这意味着你可以在启动信号流之前或之后创建实时输出。 如果你需要加快速度，在启动信号流之前创建它往往很有帮助。

实时输出在创建时启动，在删除后停止。  删除实时输出不会删除基础资产或该资产中的内容。 将其视为弹出磁带。 只要你愿意，录制的资产将一直保留，当它被弹出时（这意味着当实时输出被删除时），它将立即可供点播观看。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>获取引入 URL

创建直播活动后，可以获得要提供给实时编码器的引入 URL。 编码器将使用这些 URL 来通过 RTMP 协议输入实时传送流

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>获取预览 URL

使用 previewEndpoint 预览来自编码器的输入并验证其是否已确实收到。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>创建和管理直播活动与实时输出

将流流入实时事件后，可以通过发布流式传输定位符供客户端播放器使用来开始流式传输事件。 这会使观看者可通过流式传输终结点使用该流。

首先通过创建“直播活动”创建信号。  在你启动直播活动并将编码器连接到输入前，信号不会流动。

若要停止“磁带录像机”，可以针对 LiveOutput 调用删除。 这不会实际删除磁带“资产”上存档的 内容，只会删除“磁带录像机”并停止存档。 资产始终保留存档的视频内容，直到你针对资产本身明确调用删除。 删除 liveOutput 后，仍可通过任何已发布的流式传输定位符 URL 来播放“资产”的录制内容。 如果要使客户无法播放存档内容，首先需要删除该资产的所有定位符，如果使用 CDN 进行传递，则还需要刷新 URL 路径上的 CDN 缓存。 否则，该内容将保留在 CDN 上标准生存时间设置的 CDN 缓存中（可长达 72 小时）。

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>创建流式传输定位符，以发布 HLS 和 DASH 清单

> [!NOTE]
> 创建媒体服务帐户后，一个处于“已停止”  状态的“默认”  流式处理终结点会添加到帐户。 若要开始流式传输内容并利用[动态打包](encode-dynamic-packaging-concept.md)和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态  。

如果已使用流定位符发布了资产，则直播活动（长达 DVR 窗口长度）将继续可见，直到流定位符过期或被删除（以先发生为准）。 通过这种方式，可以使虚拟“磁带”录制可供观众进行实时和点播观看。 录制完成后（当实时输出被删除时），同一 URL 可用于观看直播活动、DVR 窗口或点播资产。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>生成 HLS 和 DASH 清单的路径

示例中的方法 BuildManifestPaths 演示了如何明确创建流式传输路径，以用于将 DASH 或 HLS 传递到各种客户端和播放器框架。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>观看事件

若要观看事件，请复制流式传输 URL（在运行“创建流定位符”中所述的代码时获得）。 你可以使用所选的媒体播放器。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 可用于测试 https://ampdemo.azureedge.net 中的流。

直播活动在停止后会自动转换为点播内容。 即使你停止并删除了事件，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。 如果资产被某个事件使用，则无法将其删除，必须先删除该事件。

### <a name="cleaning-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

如果你在整个过程中一直运行该应用程序，它将在名为“cleanUpResources”的函数中自动清除使用的所有资源。 请确保应用程序或调试器一直运行到完成，否则可能会遗漏资源，导致帐户中仍有运行的实时事件。 仔细检查 Azure 门户，确认媒体服务帐户中的所有资源都已清除。  

有关详细信息，请参阅示例代码中的 cleanUpResources 方法。

> [!IMPORTANT]
> 让直播活动保持运行会产生费用。 请注意，如果项目/节目崩溃或因某种原因而关闭，可能会导致直播活动保持运行状态，从而产生费用。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>更多有关“Azure 上的 Node.js”的开发人员文档

- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js Git 中心存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [面向 Node.js 开发人员的 Azure 包文档](/javascript/api/overview/azure/)


## <a name="next-steps"></a>后续步骤

[对文件进行流式处理](stream-files-tutorial-with-api.md)