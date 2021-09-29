---
title: 使用媒体服务 v3 来分析视频
description: 了解如何使用 Azure 媒体服务分析视频。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 8f857ff3c1a17e0f17d396b8d97d9d0e27b4fa18
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646073"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>教程：使用媒体服务 v3 来分析视频

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本教程介绍如何使用 Azure 媒体服务分析视频。 在很多情况下，用户可能会希望深入了解录制的视频或音频内容。 例如，若要提高客户满意度，组织可运行语音转文本处理，将客户支持录音转换为具有索引和仪表板的可搜索目录。

本教程演示如何：

> [!div class="checklist"]
> * 下载本主题中所述的示例应用。
> * 检查用于分析指定视频的代码。
> * 运行应用。
> * 检查输出。
> * 清理资源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>符合性、隐私和安全性
 
作为一项重要提醒，你必须在使用 Azure 视频分析器媒体版（以前称“视频索引器”）时遵守所有适用法律。 你不得以违反他人权利的方式使用视频分析器媒体版或任何其他 Azure 服务。 在将任何视频（包括任何生物特征数据）上传到视频分析器媒体版服务进行处理和存储之前，你必须拥有所有适当的权利，包括获得视频中个人的所有适当同意。 若要了解视频分析器媒体版中的符合性、隐私和安全性，请参阅 Azure [认知服务条款](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)。 若要了解 Microsoft 的隐私义务以及对您的数据的处理，请查看 Microsoft 的[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[在线服务条款](https://www.microsoft.com/licensing/product-licensing/products)（“OST”）和[数据处理附录](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)（“DPA”）。 其他隐私信息（包括有关数据保留、删除/销毁的信息）在 OST 中和[此处](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml)提供。 使用视频分析器媒体版，即表示你同意接受认知服务条款、OST、DPA 和隐私声明的约束。

## <a name="prerequisites"></a>必备条件

- 安装[适用于 Windows/macOS/Linux 的 Visual Studio Code](https://code.visualstudio.com/) 或[适用于 Windows 或 Mac 的 Visual Studio 2019](https://visualstudio.microsoft.com/)。
- 安装 [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [创建媒体服务帐户](./account-create-how-to.md)。 请务必以 JSON 格式复制 API 访问详细信息，或以此示例中使用的 .env 文件格式存储连接到媒体服务帐户所需的值。
- 遵循[使用 Azure CLI 访问 Azure 媒体服务 API](./access-api-howto.md) 中的步骤并保存凭据。 你需要使用它们来访问此示例中的 API，或将它们输入为 .env 文件格式。

## <a name="download-and-configure-the-sample"></a>下载并配置示例

使用以下命令将包含 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

该示例位于 [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/AnalyzeVideos) 文件夹。

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>检查用于分析指定视频的代码

本节讨论 AnalyzeVideos 项目的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/AnalyzeVideos/Program.cs) 文件中定义的函数。

该示例执行以下操作：

1. 创建 **转换** 和用于分析视频的 **作业**。
2. 创建输入 **资产**，并将视频上传到其中。 该资产用作作业的输入。
3. 创建用于存储作业输出的输出资产。
4. 提交作业。
5. 检查作业的状态。
6. 下载运行作业产生的文件。

### <a name="start-using-media-services-apis-with-the-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 `AzureMediaServicesClient` 对象。 若要创建对象，需要提供客户端凭据以使用 Azure Active Directory 连接到 Azure。 另一个选项是使用在 `GetCredentialsInteractiveAuthAsync` 中实现的交互式身份验证。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

在文章开头克隆的代码中，`GetCredentialsAsync` 函数根据本地配置文件 (appsettings.json) 中提供的凭据或通过存储库根目录中的 .env 环境变量文件创建 `ServiceClientCredentials` 对象。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsAsync)]

在交互式身份验证的情况下，`GetCredentialsInteractiveAuthAsync` 函数根据交互式身份验证和本地配置文件 (appsettings.json) 中提供的连接参数或通过存储库根目录中的 .env 环境变量文件创建 `ServiceClientCredentials` 对象。 在本例中，配置或环境变量文件中均不需要 AADCLIENTID 和 AADSECRET。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>创建输入资产并将本地文件上传到该资产 

CreateInputAsset 函数创建新的输入[资产](/rest/api/media/assets)并将指定的本地视频文件上传到该资产  。 此资产用作编码作业的输入。 在媒体服务 v3 中，作业输入可以是资产，也可以是可通过 HTTPS URL 使用媒体服务帐户访问的内容。 要了解如何从 HTTPS URL 进行编码，请参阅[此](job-input-from-http-how-to.md)文章。  

在媒体服务 v3 中，使用 Azure 存储 API 上传文件。 以下 .NET 片段显示如何上传。

以下函数执行以下操作：

* 创建 Asset。
* 获取 Asset 的[存储中容器](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)的可写 [SAS URL](../../storage/common/storage-sas-overview.md)。

    如果使用资产的 [ListContainerSas](/rest/api/media/assets/listcontainersas) 函数获取 SAS URL，请注意，该函数将返回多个 SAS URL，因为每个存储帐户有两个存储帐户密钥。 存储帐户有两个密钥，因为它支持存储帐户密钥无缝轮换（例如，使用一个密钥时更改另一个，然后开始使用新密钥并轮换其他密钥）。 第一个 SAS URL 表示存储 key1，第二个表示存储 key2。
* 使用 SAS URL 将文件上传到存储中的容器中。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>创建一个输出资产以存储作业的结果

输出[资产](/rest/api/media/assets)会存储作业结果。 项目定义 DownloadResults 函数，该函数将结果从此输出资产中下载到 **输出** 文件夹中，便于用户查看获取的内容。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>创建转换和分析视频的作业

对媒体服务中的内容进行编码或处理时，一种常见的模式是将编码设置设为脚本。 然后，需提交 **作业**，将该脚本应用于视频。 为每个新视频提交新 Job 后，可将该脚本应用到库中的所有视频。 媒体服务中的脚本称为“转换”。 有关详细信息，请参阅[转换和作业](./transform-jobs-concept.md)。 本教程中所述的示例定义了分析指定视频的脚本。

#### <a name="transform"></a>转换

创建新[转换](/rest/api/media/transforms)实例时，需要指定希望生成的输出内容。 TransformOutput 是必需参数。 每个 TransformOutput 包含一个预设 。 预设介绍视频和/或音频处理操作的各个步骤，这些操作可生成所需 TransformOutput 。 在此示例中，使用了 **VideoAnalyzerPreset** 预设，并且将语言 (“en-US”) 传递给了其构造函数 (`new VideoAnalyzerPreset("en-US")`)。 凭借此预设，可以从视频提取多个音频和视频见解。 如需从视频提取多个音频见解，可以使用 **AudioAnalyzerPreset** 预设。

创建 Transform 时，首先检查是否其中一个已存在使用 Get 方法，如下面的代码中所示 。 在 Media Services v3 **获取** 实体上的方法返回 **null** 如果实体不存在 （不区分大小写的名称检查）。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>作业

如上所述，[转换](/rest/api/media/transforms)对象为脚本，[作业则是](/rest/api/media/jobs)对媒体服务的实际请求，请求将转换应用到给定输入视频或音频内容。 Job 指定输入视频位置和输出位置等信息。 可以使用以下项指定视频的位置：HTTPS URL、SAS URL 或媒体服务帐户中的资产。

在此示例中，作业输入是一个本地视频。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>等待作业完成

该作业需要一些时间才能完成操作。 在该过程中，你应能够接收通知。 可通过不同选项获取有关[作业](/rest/api/media/jobs)完成情况的通知。 最简单的选项（如下所示）是使用轮询。

对于生产应用程序，由于可能出现延迟，并不建议将轮询作为最佳做法。 如果在帐户上过度使用轮询，轮询会受到限制。 开发者应改用事件网格。

事件网格旨在实现高可用性、一致性能和动态缩放。 使用事件网格，应用可以侦听和响应来自几乎所有 Azure 服务和自定义源的事件。 处理基于 HTTP 的反应事件非常简单，这有助于通过对事件的智能筛选和路由生成高效的解决方案。 有关详细信息，请参阅[将事件路由到自定义 Web 终结点](monitoring/job-state-events-cli-how-to.md)。

**作业** 通常会经历以下状态：**已计划**、**已排队**、**正在处理**、**已完成**（最终状态）。 如果作业出错，则显示“错误”状态。 如果作业正处于取消过程中，则显示“正在取消”，完成时则显示“已取消” 。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](/rest/api/media/jobs/get#joberrorcode)。

### <a name="download-the-result-of-the-job"></a>下载作业结果

以下函数将输出 [Asset](/rest/api/media/assets) 的结果下载到“输出”文件夹中，以便检查作业结果。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

一般来说，除了打算重用的对象之外，应该清除所有对象（通常，将重用 Transform 并保留 StreamingLocators）。 如果希望帐户在试验后保持干净状态，则删除不打算重复使用的资源。 例如，以下代码可删除作业和输出资产：

### <a name="delete-resources-with-code"></a>删除包含代码的资源

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

也可使用 CLI。

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>运行示例应用

按 Ctrl+F5 运行 AnalyzeVideos 应用。

运行该程序时，作业会生成其在视频中发现的每张人脸的缩略图。 它还会生成 insights.json 文件。

## <a name="examine-the-output"></a>检查输出

分析视频得到的输出文件称为 insights.json。 此文件包含有关视频的见解。 可以从[媒体智能](./analyze-video-audio-files-concept.md)一文中获取对 json 文件中找到的元素的说明。

## <a name="multithreading"></a>多线程处理

> [!WARNING]
> Azure 媒体服务 v3 SDK 不是线程安全的。 使用多线程应用时，应在每个线程上生成一个新的 AzureMediaServicesClient 对象。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：上传、编码和流式处理文件](stream-files-tutorial-with-api.md)
