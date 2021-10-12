---
title: 使用 Node.js 和 TypeScript 通过媒体服务进行实时流式传输
titleSuffix: Azure Media Services
description: 了解如何使用 Node.js、TypeScript 和 OBS Studio 流式传输实时事件。
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
ms.openlocfilehash: 2b553548111c732ca778c439232e949b0abb4707
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388596"
---
# <a name="tutorial-stream-live-with-media-services-by-using-nodejs-and-typescript"></a>教程：使用 Node.js 和 TypeScript 通过媒体服务进行实时流式传输

在 Azure 媒体服务中，[实时事件](/rest/api/media/liveevents)负责处理实时传送视频流内容。 直播活动提供输入终结点（引入 URL），然后由你将该终结点提供给实时编码器。 直播活动从实时编码器接收输入流，并通过一个或多个[流式处理终结点](/rest/api/media/streamingendpoints)使其可用于流式处理。 直播活动还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。 

本教程演示如何使用 Node.js 和 TypeScript 来创建“直通”类型的实时事件，并使用 [OBS Studio](https://obsproject.com/download) 将实时传送流广播到该事件。

在本教程中，将：

> [!div class="checklist"]
> * 下载示例代码。
> * 检查配置和执行实时传送视频流的代码。
> * 在 [Media Player 演示网站](https://ampdemo.azureedge.net)上使用 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 观看活动。
> * 清理资源。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 尽管本教程使用了 Node.js 示例，但 [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。 

## <a name="prerequisites"></a>先决条件

需要以下项才能完成本教程：

- 安装 [Node.js](https://nodejs.org/en/download/)。
- 安装 [TypeScript](https://www.typescriptlang.org/)。
- [创建媒体服务帐户](./create-account-howto.md)。 请记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 你将需要它们来访问 API 并配置环境变量文件。
- 浏览[使用 Node.js 进行配置和连接](./configure-connect-nodejs-howto.md)，了解如何使用 Node.js 客户端 SDK。
- 安装 Visual Studio Code 或 Visual Studio。
- [设置 Visual Studio Code 环境](https://code.visualstudio.com/Docs/languages/typescript)以支持 TypeScript 语言。

用于实时传送视频流的软件还需要以下配置：

- 一个用于广播事件的相机或设备（例如便携式计算机）。
- 一种本地软件编码器，可对照相机流进行编码，并通过实时消息传递协议 (RTMP) 将其发送到媒体服务实时传送视频流服务。 有关详细信息，请参阅[建议的本地实时编码器](encode-recommended-on-premises-live-encoders.md)。 流必须为 RTMP 或“平滑流式处理”  格式。

  此示例假设你将使用 Open Broadcaster Software (OBS) Studio 将 RTMP 广播到引入终结点。 [安装 OBS Studio](https://obsproject.com/download)。 

  在 OBS Studio 中使用以下编码设置：

  - 编码器：NVIDIA NVENC（如果可用）或 x264
  - 速率控制：CBR
  - 比特率：2,500 Kbps（或适合你的计算机的速率）
  - 关键帧间隔：2 秒；若要获得低延迟，则为 1 秒  
  - 预设：低延迟质量或性能 (NVENC)，或使用 x264 的“非常快”
  - 配置文件：高
  - GPU：0 (自动)
  - 最大双向预测帧：2

> [!TIP]
> 请在继续操作之前查看[使用媒体服务 v3 的实时传送视频流](stream-live-streaming-concept.md)。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含实时传送视频流 Node.js 示例的 GitHub 存储库克隆到计算机：  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

实时传送视频流示例位于 [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) 文件夹中。

在 [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) 文件夹中，将名为“sample.env”的文件复制到名为“.env”的新文件中，以存储在[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 一文中收集的环境变量设置 。
请确保该文件的“env”前包含“点”(.)，以使其正确用于代码示例。

[.env 文件](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env)包含 Azure Active Directory (Azure AD) 应用程序密钥和机密。 同时还包含对媒体服务帐户的 SDK 访问进行身份验证所需的帐户名称和订阅信息。 已配置 .gitignore 文件以避免将此文件发布到分叉存储库。 不要泄露这些凭据，因为它们是帐户的重要机密。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则最终会在帐户中有多个直播活动。 
>
> 请务必停止正在运行的直播活动， 否则，将会对你“收费”！ 在完成前一直运行程序，以便自动清理资源。 如果程序停止，或无意停止了调试器并中断了程序执行，则应仔细检查门户，确认未将任何实时事件置于“正在运行”或“等待”状态，避免产生意外的账单费用。

## <a name="examine-the-typescript-code-for-live-streaming"></a>检查 TypeScript 代码以实时传送视频流

此部分研究 Live 项目的 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) 文件中定义的函数。

此示例为每个资源创建唯一的后缀，因此即使在没有清理的情况下运行示例多次，也不会有名称冲突。

### <a name="start-using-the-media-services-sdk-for-nodejs-with-typescript"></a>开始对 Node.js 和 TypeScript 使用该媒体服务 SDK

若要开始对 Node.js 使用媒体服务 API，需要先使用 npm 包管理器添加 [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK 模块：

```bash
npm install @azure/arm-mediaservices
```

在“package.json”文件中，已为你配置了此项。 只需运行 `npm install` 即可加载模块和依赖项：

1. 打开“命令提示符”，浏览到示例的目录。
1. 将目录更改为“AMSv3Samples”文件夹：

    ```bash
    cd AMSv3Samples
    ```

1. 安装“packages.json”文件中使用的包：

    ```bash
    npm install 
    ```

1. 从“AMSv3Samples”文件夹中打开 Visual Studio Code。 （从“.vscode”文件夹和“tsconfig.json”文件所在的文件夹中开始时，必须执行此操作。） 

    ```bash
    cd ..
    code .
    ```

打开 Live 的文件夹，然后在 Visual Studio Code 编辑器中打开 index.ts 文件 。

当你在“index.ts”文件中时，选择 F5 键以打开调试器。

### <a name="create-the-media-services-client"></a>创建媒体服务客户端

以下代码片段演示了如何在 Node.js 中创建媒体服务客户端。

在此代码中，要将 `AzureMediaServicesOptions` 的 `longRunningOperationRetryTimeout` 属性从默认值 30 秒更改为 2 秒。 此更改减少了轮询 Azure 资源管理器终结点上长时间运行的操作的状态所需的时间。 它将缩短完成主要操作（如创建实时事件、启动和停止）的时间，这些操作都是异步调用。 建议在大多数情况下使用 2 秒的值。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>创建直播活动

本部分介绍如何创建基本的“直通”类型的直播活动（将 `LiveEventEncodingType` 设置为 `PassthroughBasic`）。 有关可用类型的信息，请参阅[直播活动类型](live-event-outputs-concept.md#live-event-types)。 除了基本的或标准的直通，还可以使用实时编码活动进行 720P 或 1080P 自适应比特率云编码。
 
在创建直播活动时你可能想指定以下项：

* **直播活动的引入协议**。 目前支持 RTMP、RTMPS 和平滑流式处理协议。 运行实时事件或其关联的实时输出时，无法更改协议选项。 如果需要不同的协议，请为每个流式处理协议创建单独的直播活动。  
* **对引入和预览的 IP 限制**。 可定义允许向该实时事件引入视频的 IP 地址。 允许的 IP 地址可以指定为以下选项之一：

  * 单个 IP 地址（例如 `10.0.0.1`）
  * 使用 IP 地址和无类别域际路由选择 (CIDR) 子网掩码的 IP 范围（例如 `10.0.0.1/22`）
  * 使用 IP 地址和点分十进制子网掩码的 IP 范围（例如 `10.0.0.1(255.255.252.0)`）

  如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 `0.0.0.0/0`。 IP 地址必须采用以下格式之一：具有四个数字或 CIDR 地址范围的 IPv4 地址。
* **在创建活动时自动启动它**。 如果将自动启动设置为 `true`，则直播活动会在创建后启动。 这意味着，只要直播活动开始运行，就会开始计费。 必须显式对直播活动资源调用 `Stop` 操作才能停止进一步计费。 有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing-concept.md)。

  等待模式可用于以较低成本“已分配”状态启动直播活动，使其更快地移动到“正在运行”状态。 对于需要快速向流式处理器分发通道的热池等情况，这非常有用。
* **静态主机名和唯一 GUID**。 要使引入 URL 具有预测性且易于在基于硬件的实时编码器中维护，请将 `useStaticHostname` 属性设置为 `true`。 对于 `accessToken`，请使用自定义的唯一 GUID。 有关详细信息，请参阅[直播活动引入 URL](live-event-outputs-concept.md#live-event-ingest-urls)。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>创建用于录制和存档实时事件的“资产”

在以下代码块中，你将创建一个空“资产”，用作录制实时事件存档的“磁带”。

在了解这些概念时，将资产对象视为过去你插入录像机的磁带会有所帮助。 实时输出是录像机。 直播活动只是进入机器后部的视频信号。

请记住，可以随时创建该“资产”或“磁带”。 你会将该空资产交给实时输出对象，即此类比中的“录像机”。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>创建实时输出

在本部分，创建一个实时输出，该输出使用资产名称作为输入，以告知要将实时事件录制到的位置。 此外，还将设置要在录制内容中使用的时移 (DVR) 窗口。

示例代码演示如何设置 1 小时的时移窗口。 此窗口将允许客户端播放事件的最后一小时内的任何内容。 此外，只有过去 1 小时的实时事件会保留在存档中。 如果需要，可以最多将此窗口期延长到 25 小时。  另请注意，可以控制 HTTP Live Streaming (HLS) 和 Dynamic Adaptive Streaming over HTTP (DASH) 清单在发布时在 URL 路径中使用的输出清单命名。

还可以随时创建实时输出，也就是我们比喻的“磁带录像机”。 你可以在启动信号流之前或之后创建实时输出。 如果你需要加快速度，在启动信号流之前创建输出通常很有帮助。

实时输出在创建后开始，并在删除后停止。  删除实时输出不会删除基础资产或该资产中的内容。 将其视为弹出“磁带”。 只要你愿意，含录制内容的资产可一直保留。 当它被弹出时（这意味着当实时输出被删除时），可立即点播观看。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>获取引入 URL

创建直播活动后，可以获得要提供给实时编码器的引入 URL。 编码器将使用这些 URL 来通过 RTMP 协议输入实时传送流。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>获取预览 URL

使用 `previewEndpoint` 预览并验证是否正在接收来自编码器的输入。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>创建和管理直播活动与实时输出

将流流入实时事件后，可以通过发布流式传输定位符供客户端播放器使用来开始流式传输事件。 这会使观看者可通过流式传输终结点使用该流。

首先通过创建直播活动创建信号。 在你启动直播活动并将编码器连接到输入前，信号不会流动。

若要停止“磁带录像机”，请对 `LiveOutput` 调用 `delete`。 此操作不会删除“磁带”（资产）的“内容”。 它仅删除“磁带录像机”并停止存档。 资产始终保留存档的视频内容，直到你针对资产本身显式调用 `delete`。 删除 `LiveOutput` 后，仍可通过任何发布的流式传输定位符 URL 来播放“资产”的录制内容。 

如果要删除客户端播放存档内容的能力，首先需要从“资产”中删除所有定位符。 如果使用 CDN 进行交付，还可以刷新 URL 路径上的内容分发网络 (CDN) 缓存。 否则，该内容将保留在 CDN 上标准生存时间设置的 CDN 缓存中（可能长达 72 小时）。

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>创建流式传输定位符，以发布 HLS 和 DASH 清单

> [!NOTE]
> 创建媒体服务帐户后，一个处于已停止状态的默认流式处理终结点会添加到帐户。 若要开始流式传输内容并利用[动态打包](encode-dynamic-packaging-concept.md)和动态加密，要从中流式传输内容的流式处理终结点必须处于正在运行状态。

如果已使用流式处理定位符发布了资产，则直播活动（长达 DVR 窗口长度）将继续可见，直到流式处理定位符过期或被删除（以先发生为准）。 通过这种方式，可以使虚拟“磁带”录制可供观众进行实时和点播观看。 录制完成后（当实时输出被删除时），同一 URL 可用于观看直播活动、DVR 窗口或点播资产。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>生成 HLS 和 DASH 清单的路径

示例中的方法 `BuildManifestPaths` 演示了如何明确创建流式传输路径，以用于将 HLS 或 DASH 传递到各种客户端和播放器框架。

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>观看事件

若要观看活动，请复制在运行代码以创建流式处理定位符时获得的流式处理 URL。 你可以使用所选的媒体播放器。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 可用于在 [Media Player 演示站点](https://ampdemo.azureedge.net)测试流。

直播活动在停止后会自动转换为点播内容。 即使你停止并删除了事件，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。 如果事件正在使用资产，则无法将其删除；必须先删除该事件。

## <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

如果你在整个过程中一直运行该应用程序，它将在 `cleanUpResources` 函数中自动清除使用的所有资源。 请确保应用程序或调试器一直运行到完成，否则可能会遗漏资源，导致帐户中仍有运行的实时事件。 仔细检查 Azure 门户，确认媒体服务帐户中的所有资源都已清除。 

有关详细信息，请参阅示例代码中的 `cleanUpResources` 方法。

> [!IMPORTANT]
> 让直播活动保持运行会产生费用。 请注意，如果项目或计划因某种原因而停止响应或关闭，则可能会导致直播活动保持运行状态，从而产生费用。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>更多有关“Azure 上的 Node.js”的开发人员文档

- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub 存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [面向 Node.js 开发人员的 Azure 包文档](/javascript/api/overview/azure/)


## <a name="next-steps"></a>后续步骤

[对文件进行流式处理](stream-files-tutorial-with-api.md)