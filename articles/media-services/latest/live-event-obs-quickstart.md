---
title: 使用 OBS Studio 创建实时传送流
description: 了解如何使用门户和 OBS Studio 创建 Azure 媒体服务实时传送流
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: 43f1602b22a761461f84761ed3a3806397011ae2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389507"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>使用 OBS 创建 Azure 媒体服务实时传送流

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本快速入门帮助用户使用 Azure 门户创建一个媒体服务直播活动，并使用 Open Broadcasting Studio (OBS) 进行广播。 本文假设你有一个 Azure 订阅，并已创建媒体服务帐户。

本快速入门将介绍以下方面的内容：

- 使用 OBS 设置本地编码器。
- 设置实时传送流。
- 设置实时传送流输出。
- 运行默认的流式处理终结点。
- 使用 Azure Media Player 观看实时传送流和按需输出。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

打开 Web 浏览器，转到 [Microsoft Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>使用 OBS 设置本地编码器

1. 在 [Open Broadcaster Software 网站](https://obsproject.com/)上下载适用于你的操作系统的 OBS，然后进行安装。
1. 启动应用程序，使其保持打开状态。

## <a name="run-the-default-streaming-endpoint"></a>运行默认的流式处理终结点

1. 在媒体服务列表中选择“流式处理终结点”。

   ![“流式处理终结点”菜单项。](media/live-events-obs-quickstart/streaming-endpoints.png)
1. 如果默认流式处理终结点的状态为“已停止”，请选择它。 此步骤会将你转到该终结点的页面。
1. 选择“开始”。

   ![流式处理终结点的“启动”按钮。](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>设置 Azure 媒体服务实时传送流

1. 在门户中转到 Azure 媒体服务帐户，然后从“媒体服务”列表中选择“实时传送视频流”。 

   ![“实时传送视频流”链接。](media/live-events-obs-quickstart/select-live-streaming.png)
1. 选择“添加实时事件”创建新的实时传送视频流事件。

   ![“添加直播活动”图标。](media/live-events-obs-quickstart/add-live-event.png)
1. 在“实时事件名称”框中，输入新事件的名称，例如 TestLiveEvent。

   ![“直播活动名称”框。](media/live-events-obs-quickstart/live-event-name.png)
1. 在“说明”框中输入事件的可选说明。
1. 选择基本“直通 – 无云编码”选项。

   ![云编码选项。](media/live-events-obs-quickstart/cloud-encoding.png)
1. 选择“RTMP”选项。
1. 确保选择了与“启动实时事件”对应的“无”选项，以免在该实时事件准备就绪之前产生费用。  （启动实时事件后，即会开始计费。）

   ![“启动直播活动”选项。](media/live-events-obs-quickstart/start-live-event-no.png)
1. 选择“查看 + 创建”按钮检查设置。
1. 选择“创建”按钮以创建实时事件。 随后你会返回到实时事件列表。
1. 选择已创建的直播活动的链接。 可以观察到你的事件已停止。
1. 在浏览器中将此页保持打开状态。 稍后我们将返回到该文件。

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>使用 OBS Studio 设置实时传送流

OBS 启动时有一个默认场景，但未选择任何输入。

   ![OBS 默认屏幕](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>添加视频源

1. 在“源”面板中，单击“添加”图标，选择新的源设备。 此时会打开“源”菜单。

1. 从源设备菜单中选择“视频捕获设备”。 此时会打开“创建/选择源”菜单。

   ![其中选定了视频设备的 OBS 源菜单。](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. 选择“添加现有”单选按钮，然后选择“确定”。 此时会打开“视频设备的属性”菜单。

   ![其中选定了“添加现有”的 OBS 新视频源菜单。](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. 从“设备”下拉列表中，选择要用于广播的视频输入。 暂时保留其余设置，选择“确定”。 输入源会添加到“源”面板中，视频输入视图会显示在“预览”区域中。

   ![OBS 摄像头设置](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>添加音频源

1. 在“源”面板中，选择“添加”图标，选择新的源设备。 此时会打开“源设备”菜单。

1. 从源设备菜单中选择“音频输入捕获”。 此时会打开“创建/选择源”菜单。

   ![其中选定了音频设备的 OBS 源菜单。](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. 选择“添加现有”单选按钮，然后选择“确定”。 此时会打开“音频输入捕获的属性”菜单。

   ![其中选定了“添加现有”的 OBS 音频源。](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. 从“设备”下拉列表中，选择要用于广播的音频捕获设备。 暂时保留其余设置，选择“确定”。 音频捕获设备将添加到音频混合器面板中。

   ![OBS 音频设备选择下拉列表](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>在 OBS 中设置流式处理和高级编码设置

在接下来的过程中，你将在浏览器中返回到 Azure 媒体服务来复制输入 URL，以便将其输入到输出设置中：

1. 在门户的“Azure 媒体服务”页上，选择“启动”以启动实时传送流事件。 （现在开始计费。）

   ![“启动”图标。](media/live-events-obs-quickstart/start.png)
1. 将“RTMP”开关设置为“RTMPS”。
1. 在“输入 URL”中，将 URL 复制到剪贴板。

   ![输入 URL。](media/live-events-obs-quickstart/input-url.png)

1. 切换到 OBS 应用程序。

1. 在“控制”面板中，选择“设置”按钮。 此时会打开“设置”选项。

   ![其中选中了“设置”的 OBS 控制面板。](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. 从“设置”菜单中选择“流”。 

1. 从“服务”下拉列表中，选择“全部显示”，然后选择“自定义...” 。

1. 在“服务器”字段中，粘贴已复制到剪贴板的 RTMPS URL。

1. 在“流键”字段中输入一些内容。  此字段的内容是什么并不重要，但需要有一个值。

    ![OBS 流设置。](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. 从“设置”菜单中选择“输出”。 

1. 选择页面顶部的“输出模式”下拉列表，然后选择“高级”以访问所有可用编码器设置。

1. 选择“流式处理”选项卡以设置编码器。

1. 选择适合于系统的编码器。  如果硬件支持 GPU 加速，请选择 NVIDIA **NVENC** H.264 或 Intel **QuickSync** H.264。 如果系统没有受支持的 GPU，请选择 **X264** 软件编码器选项。

#### <a name="x264-encoder-settings"></a>X264 编码器设置

1. 如果已选择 **X264** 编码选项，请选中“重新缩放输出”框。 如果在媒体服务中使用“高级直播活动”，请选择 1920x1080，如果在使用标准 (720P) 直播活动，则选择 1280x720。  如果使用的是基本或标准直通直播活动，则可以选择任何可用的分辨率。

1. 将“比特率”设置为 1500 kbps 到 4000 kbps 之间的任意值。 如果以 720P 使用标准编码直播活动，建议使用 2500 Kbps。 如果使用1080P 高级直播活动，建议使用 4000 Kbps。 可能需要根据可用的 CPU 能力和网络的带宽调整比特率，以实现所需的质量设置。

1. 在“关键帧间隔”字段中输入“2”。 此值将关键帧间隔设置为 2 秒，这会控制通过 HLS 或 DASH 从媒体服务发送的片段的最终大小。 切勿将关键帧间隔设置为大于 4 秒。  如果广播时出现高延迟，应始终仔细检查或告知应用程序用户始终将此值设置为 2 秒。 如要尝试降低直播发送延时，可以选择将此值设置为低至 1 秒。

1. 可选：将“CPU 使用率”预设值设置为“非常快”，并进行一些试验，看看本地 CPU 是否能够以合理的开销来处理比特率和预设值的组合。 尽量避免导致 CPU 平均使用率高于 80% 的设置，以避免实时传送视频流过程中出现任何问题。 要提高质量，可以通过“较快”、“快速”预设设置进行测试，直到达到 CPU 限制。

   ![OBS X264 编码器设置](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. 保留其余设置不变，然后选择“确定”。

#### <a name="nvidia-nvenc-encoder-settings"></a>Nvidia NVENC 编码器设置

1. 如果已选择 **NVENC** GPU 编码选项，请选中“重新缩放输出”框，然后，如果在媒体服务中使用高级直播活动，选择 1920x1080，如果使用标准 (720p) 直播活动，选择 1280x720。 如果使用的是基本或标准直通直播活动，则可以选择任何可用的分辨率。

1. 对于恒定比特率控制，将“比率控制”设置为“CBR”。

1. 将“比特率”设置为 1500 Kbps 到 4000 Kbps 之间的任意值。 如果以 720P 使用标准编码直播活动，建议使用 2500 Kbps。 如果使用1080P 高级直播活动，建议使用 4000 Kbps。 可以选择根据可用的 CPU 能力和网络的带宽调整该值，以实现所需的质量设置。

1. 在 X264 选项下将“关键帧间隔”设置为 2 秒，如上所述。 请勿超过 4 秒，否则可能会显著影响实时广播的延迟。

1. 根据本地计算机上的 CPU 速度，将“预设”设置为“低延迟”、“低延迟性能”或“低延迟质量”。 用这些设置来试验，以根据自己的硬件实现质量和 CPU 使用率之间的最佳平衡。

1. 如果使用更强大的硬件配置，请将“配置文件”设置为“主要”或“高”。

1. 取消选中“提前查看”。 如果计算机性能非常强大，则可以选中此选项。

1. 取消选中“Psycho 视觉优化”。 如果计算机性能非常强大，则可以选中此选项。

1. 将“GPU”设置为 0 可自动决定要分配的 GPU。 如果需要，可以限制 GPU 使用率。

1. 将“最大双向预测帧”设置为 2

   ![OBS NVidia NVidia NVENC GPU 编码器设置。](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Intel QuickSync 编码器设置

1. 如果已选择 Intel **QuickSync** GPU 编码选项，请选中“重新缩放输出”框，然后，如果在媒体服务中使用高级直播活动，选择 1920x1080，如果使用标准 (720p) 直播活动，选择 1280x720。 如果使用的是基本或标准直通直播活动，则可以选择任何可用的分辨率。

1. 将“目标使用”设置为“平衡”，或根据 CPU 和 GPU 组合负载，按需调整。 根据需要进行调整并进行试验，以在硬件能够产生的质量下实现 80% 的平均最大 CPU 利用率。 如果使用的硬件受到更多约束，请以“快速”进行试验，如果出现性能问题，则降为“非常快速”。

1. 如果使用更强大的硬件配置，请将“配置文件”设置为“主要”或“高”。

1. 在 X264 选项下将“关键帧间隔”设置为 2 秒，如上所述。 请勿超过 4 秒，否则可能会显著影响实时广播的延迟。

1. 对于恒定比特率控制，将“比率控制”设置为“CBR”。

1. 将“比特率”设置为 1500 Kbps 到 4000 Kbps 之间的任何值。  如果以 720P 使用标准编码直播活动，建议使用 2500 Kbps。 如果使用1080P 高级直播活动，建议使用 4000 Kbps。 可以选择根据可用的 CPU 能力和网络的带宽调整该值，以实现所需的质量设置。

1. 将“延迟”设置为“低”。

1. 将“双向预测帧”设置为 2。

1. 取消选中“主观视频增强功能”。

   ![OBS 的“Intel QuickSync GPU 编码器”设置。](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>“设置音频”设置

在接下来的步骤中调整音频编码设置。

1. 在“设置”中选择“输出”->“音频”选项卡。

1. 将曲目 1 的“音频比特率”设置为 128 Kbps。

   ![OBS 的“音频比特率”设置。](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. 在“设置”中选择“音频”选项卡。

1. 将“采样率”设置为 44.1 kHz。

   ![OBS 的“音频采样率”设置。](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>启动流式处理

1. 在“控制”面板中，单击“启动流式处理”。 

    ![OBS 的“启动流式处理”按钮。](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. 在浏览器中切换到 Azure 媒体服务实时事件屏幕，单击“重载播放器”链接。 现在应该可以在“预览播放器”中看到你的流。

## <a name="set-up-outputs"></a>设置输出

此部分将设置输出，并使你可以保存实时流的录制内容。  

> [!NOTE]
> 若要流式传输此输出，流式处理终结点必须正在运行。 请参阅后面的[运行默认的流式处理终结点](#run-the-default-streaming-endpoint)部分。

1. 选择“输出”视频查看器下面的“创建输出”链接。 
1. 如果需要，请将“名称”框中的输出名称编辑为用户更容易理解的内容，便于今后查找。

   ![“输出名称”框。](media/live-events-wirecast-quickstart/output-name.png)
1. 暂时不要理会其余所有框。
1. 选择“下一步”添加流定位器。
1. 如果需要，请将定位器的名称更改为用户更容易理解的内容。

   ![“定位器名称”框。](media/live-events-wirecast-quickstart/live-event-locator.png)
1. 暂时不要理会此屏幕上的其他任何设置。
1. 选择“创建”  。

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>使用 Azure Media Player 播放输出的广播

1. 复制“输出”视频播放器下面的流 URL。
1. 在 Web 浏览器中，打开 [Azure Media Player 演示](https://ampdemo.azureedge.net/azuremediaplayer.html)。
1. 将流 URL 粘贴到 Azure Media Player 的“URL”框中。
1. 选择“更新播放器”按钮。
1. 选择视频中的“播放”图标查看实时传送流。

## <a name="stop-the-broadcast"></a>停止广播

如果你认为已流式传输了足够的内容，请停止广播。

1. 在门户中选择“停止”。

1. 在 OBS 的“控制”面板中，选择“停止流式处理”按钮。  此步骤会在 OBS 中停止广播。

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>使用 Azure Media Player 播放按需输出

现在，只要流式处理终结点正在运行，创建的输出就可供按需流式处理。

1. 转到媒体服务列表并选择“资产”。
1. 找到前面创建的事件输出，然后选择指向该资产的链接。 此时会打开资产输出页。
1. 复制该资产的视频播放器下的流 URL。
1. 在浏览器中返回到 Azure Media Player，将流 URL 粘贴到“URL”框中。
1. 选择“更新播放器”。
1. 选择视频中的“播放”图标以观看按需资产。

## <a name="clean-up-resources"></a>清理资源

> [!IMPORTANT]
> 请停止服务！ 完成本快速入门中的步骤后，请务必停止实时事件和流式处理终结点，否则系统会按它们的运行时间计收费用。 若要停止实时事件，请参阅[停止广播](#stop-the-broadcast)过程的步骤 2 和 3。

若要停止流式处理终结点，请执行以下操作：

1. 从媒体服务列表中选择“流式处理终结点”。
2. 选择前面启动的默认流式处理终结点。 此步骤会打开该终结点的页面。
3. 选择“停止”  。

> [!TIP]
> 如果你不想要保留来自此事件的资产，请务必将其删除，以免产生存储费用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [媒体服务中的实时事件和实时输出](./live-event-outputs-concept.md)
