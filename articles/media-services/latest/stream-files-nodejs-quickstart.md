---
title: 如何使用 Node.js 对视频文件进行编码和流式传输
description: 如何使用 Node.js 对视频文件进行流式传输。 按照本教程的步骤，创建新的 Azure 媒体服务帐户、编码文件并将文件流式传输到 Azure Media Player。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure 媒体服务，流式传输，Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: b7efa37747e6d116b4cf26b4cf54377037f22cdd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212750"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>如何使用 Node.js 对视频文件进行编码和流式传输

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本快速入门展示了使用 Azure 媒体服务在各种浏览器和设备上对视频进行编码和流式处理有多轻松。 可以使用 HTTPS、URL、SAS URL 或位于 Azure Blob 存储中的文件路径来指定输入视频文件。

完成本快速入门后，你将了解：

- 如何使用 Node.JS 进行编码
- 如何使用 Node.JS 进行流式传输
- 如何使用 Node.JS 从 HTTPS URL 上传文件
- 如何将 HLS 或 DASH 客户端播放器与 Node.JS 配合使用

本文中的示例对可通过 HTTPS URL 访问的内容进行编码。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

![播放视频](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 安装 [Node.js](https://nodejs.org/en/download/)
- [创建媒体服务帐户](./create-account-howto.md)。<br/>请务必记住用于资源组名称和媒体服务帐户名称的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 需要使用这些凭据来访问 API。
- 首先浏览[使用 Node.js 进行配置和连接](./configure-connect-nodejs-howto.md)操作说明，了解如何使用 Node.js 客户端 SDK

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含流式处理 Node.js 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

该示例位于 [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) 文件夹。

在下载的项目中打开 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts)。 使用从[访问 API](./access-api-howto.md) 获得的值和凭据更新根文件夹中的 sample.env 文件。 将 sample.env 文件重命名为 .env（是的，只是扩展名） 。

该示例执行以下操作：

1. 使用[内容感知编码预设](./content-aware-encoding.md)创建转换。 首先检查指定的转换是否存在。
1. 创建一个输出资产，编码作业将其用于包含输出 
1. （可选）使用存储 Blob SDK 上传本地文件
1. 创建基于 HTTPS URL 或上传文件的作业输入
1. 使用先前创建的输入和输出提交编码 **作业**
1. 检查作业的状态
1. 将编码作业的输出下载到本地文件夹
1. 创建要在播放器中使用的流式处理定位符
1. 为 HLS 和 DASH 生成流式处理 URL
1. 在播放器应用程序中播放内容 - Azure Media Player

## <a name="run-the-sample"></a>运行示例

1. 应用程序下载编码文件。 创建想要输出文件位于的文件夹，并更新 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) 文件中 outputFolder 变量的值。 默认情况下，它设置为“Temp”。
1. 打开“命令提示符”，浏览到示例的目录。
1. 将目录更改为 AMSv3Samples 文件夹。

    ```bash
    cd AMSv3Samples
    ```

1. 安装 packages.json 文件中使用的包。

    ```bash
    npm install 
    ```

1. 将目录更改为 StreamFilesSample 文件夹。

    ```bash
    cd StreamFilesSample
    ```

1. 从 AMSv3Samples 文件夹中启动 Visual Studio Code。 （需要从 .vscode 文件夹和 tsconfig.json 文件所在的文件夹中启动 。）

    ```bash
    cd ..
    code .
    ```

打开 StreamFilesSample 的文件夹，然后在 Visual Studio Code 编辑器中打开 index.ts 文件 。
在 index.ts 文件中，按 F5 启动调试器。

## <a name="test-with-azure-media-player"></a>使用 Azure Media Player 进行测试

使用 Azure Media Player 测试流。 还可以使用任何符合 HLS 或 DASH 的播放器，例如 Shaka 播放机、HLS.js、Dash.js 或其他播放机。

你应该能够单击示例中生成的链接，并在已加载 DASH 清单的情况下启动 AMP 播放器。

> [!NOTE]
> 如果播放器在 Https 站点上进行托管，请确保将 URL 更新为“https”。

1. 打开 Web 浏览器并导航到 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)。
2. 在“URL:”框中，粘贴运行应用程序时获取的某个流式处理 URL 值。可以粘贴 HLS、Dash 或 Smooth 格式的 URL，Azure Media Player 将切换到适当的流式处理协议，以便在你的设备上自动播放。
3. 按“更新播放器”。

Azure Media Player 可用于测试，但不可在生产环境中使用。

## <a name="clean-up-resources"></a>清理资源

如果不再需要你的资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除该资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>更多有关“Azure 上的 Node.js”的开发人员文档

- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js Git 中心存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [面向 Node.js 开发人员的 Azure 包文档](/javascript/api/overview/azure/)

## <a name="see-also"></a>另请参阅

- [作业错误代码](/rest/api/media/jobs/get#joberrorcode)。
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js 存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>后续步骤

> [媒体服务概念](concepts-overview.md)
