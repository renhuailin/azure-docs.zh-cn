---
title: 使用 .NET 5.0 通过媒体服务进行实时流式传输
titleSuffix: Azure Media Services
description: 了解如何使用 .NET 5.0 流式传输直播活动
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
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: f10ef55a44aa917fd8f0fb3783dcd29284512d7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732670"
---
# <a name="tutorial-stream-live-with-media-services-by-using-net-50"></a>教程：使用 .NET 5.0 通过媒体服务进行实时流式传输

在 Azure 媒体服务中，[实时事件](/rest/api/media/liveevents)负责处理实时传送视频流内容。 直播活动提供输入终结点（引入 URL），然后由你将该终结点提供给实时编码器。 直播活动从实时编码器接收输入流，并通过一个或多个[流式处理终结点](/rest/api/media/streamingendpoints)使其可用于流式处理。 直播活动还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。 

本教程演示如何使用 .NET 5.0 创建直通类型的直播活动。 在本教程中，将：

> [!div class="checklist"]
> * 下载示例应用。
> * 检查执行实时传送视频流的代码。
> * 在 [Media Player 演示网站](https://ampdemo.azureedge.net)上使用 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 观看活动。
> * 清理资源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 尽管本教程使用了 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) 示例，但 [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。 

## <a name="prerequisites"></a>先决条件

需要以下项才能完成本教程：

- 安装[适用于 Windows/macOS/Linux 的 Visual Studio Code](https://code.visualstudio.com/) 或[适用于 Windows 或 Mac 的 Visual Studio 2019](https://visualstudio.microsoft.com/)。
- 安装 [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [创建媒体服务帐户](./account-create-how-to.md)。 请务必以 JSON 格式复制 API 访问详细信息，或以此示例中使用的 .env 文件格式存储连接到媒体服务帐户所需的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 你需要使用它们来访问此示例中的 API，或将它们输入为 .env 文件格式。 

用于实时传送视频流的软件还需要以下配置：

- 一个用于广播事件的相机或设备（例如便携式计算机）。
- 一种本地软件编码器，可对照相机流进行编码，并通过实时消息传递协议 (RTMP) 将其发送到媒体服务实时传送视频流服务。 有关详细信息，请参阅[建议的本地实时编码器](encode-recommended-on-premises-live-encoders.md)。 流必须为 RTMP 或“平滑流式处理”  格式。  

  此示例假设你将使用 Open Broadcaster Software (OBS) Studio 将 RTMP 广播到引入终结点。 [安装 OBS Studio](https://obsproject.com/download)。 

> [!TIP]
> 请在继续操作之前查看[使用媒体服务 v3 的实时传送视频流](stream-live-streaming-concept.md)。 

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含实时传送视频流 .NET 示例的 GitHub 存储库克隆到计算机：  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

实时传送视频流示例位于 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live) 文件夹中。

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则最终会在帐户中有多个直播活动。
>
> 请务必停止正在运行的直播活动， 否则，将会对你“收费”！

## <a name="examine-the-code-that-performs-live-streaming"></a>检查执行实时传送视频流的代码

此部分研究 LiveEventWithDVR 项目的 [Authentication.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Common_Utils/Authentication.cs) 文件（在 Common_Utils 文件夹中）和 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) 文件中定义的函数。

此示例为每个资源创建唯一的后缀，因此即使在没有清理的情况下运行示例多次，也不会有名称冲突。


### <a name="start-using-media-services-apis-with-the-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

Authentication.cs 使用本地配置文件 (appsettings.json 或 .env) 中提供的凭据创建 `AzureMediaServicesClient` 对象。

通过 `AzureMediaServicesClient` 对象可以开始将媒体服务 API 与 .NET 结合使用。 若要创建对象，需要提供客户端凭据以使用 Azure Active Directory 连接到 Azure（在 `GetCredentailsAsync` 中实现）。 另一个选项是使用在 `GetCredentialsInteractiveAuthAsync` 中实现的交互式身份验证。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

在文章开头克隆的代码中，`GetCredentialsAsync` 函数根据本地配置文件 (appsettings.json) 中提供的凭据或通过存储库根目录中的 .env 环境变量文件创建 `ServiceClientCredentials` 对象。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsAsync)]

在交互式身份验证的情况下，`GetCredentialsInteractiveAuthAsync` 函数根据交互式身份验证和本地配置文件 (appsettings.json) 中提供的连接参数或通过存储库根目录中的 .env 环境变量文件创建 `ServiceClientCredentials` 对象。 在本例中，配置或环境变量文件中均不需要 AADCLIENTID 和 AADSECRET。

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]


### <a name="create-a-live-event"></a>创建直播活动

本部分介绍如何创建直通类型的直播活动（将 `LiveEventEncodingType` 设置为 `None`）。 有关可用类型的信息，请参阅[直播活动类型](live-event-outputs-concept.md#live-event-types)。 除了直通，还可以使用实时转码活动进行 720P 或 1080P 自适应比特率云编码。
 
在创建直播活动时你可能想指定以下项：

* **直播活动的引入协议**。 目前支持 RTMP、RTMPS 和平滑流式处理协议。 运行实时事件或其关联的实时输出时，无法更改协议选项。 如果需要不同的协议，请为每个流式处理协议创建单独的直播活动。 
* **对引入和预览的 IP 限制**。 可定义允许向该实时事件引入视频的 IP 地址。 允许的 IP 地址可以指定为以下选项之一：

  * 单个 IP 地址（例如 `10.0.0.1`）
  * 使用 IP 地址和无类别域际路由选择 (CIDR) 子网掩码的 IP 范围（例如 `10.0.0.1/22`）
  * 使用 IP 地址和点分十进制子网掩码的 IP 范围（例如 `10.0.0.1(255.255.252.0)`）

  如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 `0.0.0.0/0`。 IP 地址必须采用以下格式之一：具有四个数字或 CIDR 地址范围的 IPv4 地址。  
* **在创建活动时自动启动它**。 如果将自动启动设置为 `true`，则直播活动会在创建后启动。 这意味着，只要直播活动开始运行，就会开始计费。 必须显式对直播活动资源调用 `Stop` 操作才能停止进一步计费。 有关详细信息，请参阅[实时事件状态和计费](live-event-states-billing-concept.md)。

  等待模式可用于以较低成本“已分配”状态启动直播活动，使其更快地移动到“正在运行”状态。 对于需要快速向流式处理器分发通道的热池等情况，这非常有用。
* **静态主机名和唯一 GUID**。 要使引入 URL 具有预测性且易于在基于硬件的实时编码器中维护，请将 `useStaticHostname` 属性设置为 `true`。 有关详细信息，请参阅[直播活动引入 URL](live-event-outputs-concept.md#live-event-ingest-urls)。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>获取引入 URL

创建直播活动后，可以获得要提供给实时编码器的引入 URL。 编码器将使用这些 URL 来输入实时流。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>获取预览 URL

使用 `previewEndpoint` 预览并验证是否正在接收来自编码器的输入。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>创建和管理直播活动与实时输出

将流传输到直播活动后，可以通过创建资产、实时输出和流式处理定位符来启动流式传输活动。 这会存档流，并使观看者可通过流式处理终结点使用该流。

在了解这些概念时，将资产对象视为过去你插入录像机的磁带会有所帮助。 实时输出是录像机。 直播活动只是进入机器后部的视频信号。

首先通过创建直播活动创建信号。 在你启动直播活动并将编码器连接到输入前，信号不会流动。

可以随时创建“磁带”。 这只是一个空资产，你会将其交给实时输出对象，即此类比中的“录像机”。

也可随时创建“录像机”。 你可以在启动信号流之前或之后创建实时输出。 如果你需要加快速度，在启动信号流之前创建输出有时很有帮助。

若要停止“磁带录像机”，请对 `LiveOutput` 调用 `delete`。 此操作不会删除“磁带”（资产）的内容。 资产始终保留存档的视频内容，直到你针对资产本身显式调用 `delete`。 

下一部分将介绍如何创建资产和实时输出。

#### <a name="create-an-asset"></a>创建资产

创建供实时输出使用的资产。 在我们的类比中，资产就是录制实时视频信号的“磁带”。 观看者将能够从此虚拟磁带以实时或点播方式查看内容。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>创建实时输出

实时输出在创建后开始，并在删除后停止。  删除实时输出不会删除基础资产或该资产中的内容。 将其视为弹出“磁带”。 只要你愿意，含录制内容的资产可一直保留。 当它被弹出时（这意味着当实时输出被删除时），可立即点播观看。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>创建流式处理定位符

> [!NOTE]
> 创建媒体服务帐户后，一个处于已停止状态的默认流式处理终结点会添加到帐户。 若要开始流式传输内容并利用[动态打包](encode-dynamic-packaging-concept.md)和动态加密，要从中流式传输内容的流式处理终结点必须处于正在运行状态。

如果已使用流式处理定位符发布了资产，则直播活动（长达 DVR 窗口长度）将继续可见，直到流式处理定位符过期或被删除（以先发生为准）。 通过这种方式，可以使虚拟“磁带”录制可供观众进行实时和点播观看。 录制完成后（当实时输出被删除时），同一 URL 可用于观看直播活动、DVR 窗口或点播资产。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the URL to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

如果你已完成活动的流式传输，并想要清理先前预配的资源，请使用以下过程：

1. 停止从编码器推送流。
1. 停止直播活动。 直播活动在停止后，不会产生任何费用。 需要重新启动它时，它会采用相同的引入 URL，因此无需重新配置编码器。
1. 除非想要继续以点播流形式提供直播活动的存档，否则会停止流式处理终结点。 如果直播活动处于停止状态，则不会产生任何费用。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>观看事件

按 Ctrl+F5 运行代码。 这会输出可用于观看直播活动的流式处理 URL。 复制为创建流式处理定位符而获得的流式处理 URL。 你可以使用所选的媒体播放器。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 可用于在 [Media Player 演示站点](https://ampdemo.azureedge.net)测试流。

直播活动在停止后会自动转换为点播内容。 即使你停止并删除了事件，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。 如果事件正在使用资产，则无法将其删除；必须先删除该事件。

## <a name="clean-up-remaining-resources"></a>清理剩余资源

如果不再需要资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除之前创建的资源组。

运行以下 CLI 命令：

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 让直播活动保持运行会产生费用。 请注意，如果项目或计划因某种原因而停止响应或关闭，则可能会导致直播活动保持运行状态，从而产生费用。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[对文件进行流式处理](stream-files-tutorial-with-api.md)
 
