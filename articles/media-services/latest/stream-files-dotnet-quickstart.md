---
title: 使用 Azure 媒体服务流式传输视频文件 - .NET
description: 按照本教程的步骤，使用 .NET 创建新的 Azure 媒体服务帐户、编码文件并将文件流式传输到 Azure Media Player。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure 媒体服务, 流式传输
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2021
ms.author: inhenkel
ms.openlocfilehash: 079575148585c508224b6767b2eba61a16463fa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732737"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本教程展示了使用 Azure 媒体服务在各种浏览器和设备上对视频进行编码和流式处理有多轻松。 可以使用 HTTPS、URL、SAS URL 或位于 Azure Blob 存储中的文件路径来指定输入内容。
本主题中的示例对可通过 HTTPS URL 访问的内容进行编码。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

完成本教程后即可对视频进行流式处理。  

![播放视频](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 安装[适用于 Windows/macOS/Linux 的 Visual Studio Code](https://code.visualstudio.com/) 或[适用于 Windows 或 Mac 的 Visual Studio 2019](https://visualstudio.microsoft.com/)。
- 安装 [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [创建媒体服务帐户](./account-create-how-to.md)。 请务必以 JSON 格式复制 API 访问详细信息，或以此示例中使用的 .env 文件格式存储连接到媒体服务帐户所需的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤。 确保保存凭据。 你需要使用它们来访问此示例中的 API，或将它们输入为 .env 文件格式。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含流式处理 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

该示例位于 AMSV3Quickstarts 下的 [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) 文件夹。

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

该示例执行以下操作：

1. 创建一个 **转换**（首先，检查指定的转换是否存在）。 
2. 创建一个输出 **资产**，用作编码 **作业** 的输出。
3. 创建基于 HTTPS URL 的 **作业** 输入。
4. 使用先前创建的输入和输出提交编码 **作业**。
5. 检查作业的状态。
6. 创建 **流定位符**。
7. 生成流式处理 URL。

有关示例代码中的每个功能的内容介绍，请在[此源文件](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中查看相关代码和注释。

## <a name="run-the-sample-app"></a>运行示例应用

运行应用时，显示使用不同协议播放视频的 URL。 

1. 在 VSCode 中打开 AMSV3Quickstarts。
2. 按 Ctrl+F5 通过 .NET 运行 EncodeAndStreamFiles 应用程序。 这可能需要几分钟的时间。
3. 应用将输出三个 URL。 你将在下一步中使用这些 URL 测试流。

![Visual Studio 中 EncodeAndStreamFiles 应用输出的屏幕截图，显示了在 Azure Media Player 中使用的三个流式处理 URL。](./media/stream-files-tutorial-with-api/output.png)

在此示例的[源代码](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中，可查看 URL 的生成方式。 若要生成 URL，需要连接流式处理终结点的主机名和流式处理定位符路径。  

## <a name="test-with-azure-media-player"></a>使用 Azure Media Player 进行测试

本文使用 Azure Media Player 测试流式传输。 

> [!NOTE]
> 如果播放器在 Https 站点上进行托管，请确保将 URL 更新为“https”。

1. 打开 Web 浏览器并导航到 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)。
2. 在“URL:”框中，粘贴运行应用程序时获取的某个流式处理 URL 值  。 
 
     可以粘贴 HLS、Dash 或 Smooth 格式的 URL，Azure Media Player将切换到适当的流协议，以便在你的设备上自动播放。
3. 按“更新播放器”。 这应开始播放存储库中的视频文件。

Azure Media Player 可用于测试，但不可在生产环境中使用。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除该资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>检查代码

有关示例代码中的每个功能的内容介绍，请在[此源文件](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中查看相关代码和注释。

如需了解更高级的流式处理示例及其详细说明，请参阅[上传、编码和流式处理文件](stream-files-tutorial-with-api.md)教程。 

### <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](/rest/api/media/jobs/get#joberrorcode)。

## <a name="multithreading"></a>多线程处理

Azure 媒体服务 v3 SDK 不是线程安全的。 使用多线程应用程序时，应在每个线程上生成一个新的 AzureMediaServicesClient 对象。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：上传、编码和流式处理文件](stream-files-tutorial-with-api.md)
