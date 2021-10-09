---
title: 开发使用媒体服务 v3 的 Azure Functions
description: 本文说明如何使用 Visual Studio Code 中开始开发使用媒体服务 v3 的 Azure Functions。
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661709"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>开发使用媒体服务 v3 的 Azure Functions

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何开始创建使用媒体服务的 Azure Functions。 本文中定义的 Azure 函数使用 Media Encoder Standard 对视频文件进行编码。 一旦创建了编码作业，该函数就会返回作业名称和输出资产名称。 要查看 Azure Functions，请参阅“Azure Functions”部分的[概述](../../azure-functions/functions-overview.md)和其他主题。

如果你想要浏览并部署使用 Azure Media Services 的现有 Azure 功能，请查看[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)。 此存储库包含几个示例，示例中将使用媒体服务来演示有关直接从 Blob 存储引入内容、编码以及实时传送流操作。

## <a name="prerequisites"></a>必备条件

- 必须先具有有效的 Azure 帐户，然后才能创建第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。
- 若要创建针对 Azure 媒体服务 (AMS) 帐户执行操作或者侦听媒体服务发送的事件的 Azure Functions，应该根据[此文](account-create-how-to.md)中所述创建一个 AMS 帐户。
- 在某一[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安装 [Visual Studio Code](https://code.visualstudio.com/)。

本文介绍如何创建与 Azure 媒体服务通信的 C# .NET 5 函数。 若要使用另一种语言创建函数，请查看[这篇文章](../../azure-functions/functions-develop-vs-code.md)。

### <a name="run-local-requirements"></a>运行本地要求

仅在本地运行和调试函数时需要这些先决条件。 不需要它们创建项目或将项目发布到 Azure Functions。

- [.NET Core 3.1 和 .NET 5 SDK](https://dotnet.microsoft.com/download/dotnet)。

- [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x 或更高版本。 在本地启动项目时，系统会自动下载并安装 Core Tools 包。 Core Tools 包含整个 Azure Functions 运行时，因此下载和安装可能需要一段时间。

- Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

## <a name="install-the-azure-functions-extension"></a>安装 Azure Functions 扩展

可以使用 Azure Functions 扩展创建和测试函数，并将其部署到 Azure。

1. Visual Studio Code 中打开“扩展”并搜索 **Azure functions**，或者在 Visual Studio Code 中选择以下链接：[`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions)。

1. 选择“安装”以安装 Visual Studio Code 的扩展：

    ![安装适用于 Azure Functions 的扩展](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. 安装后，在活动栏上选择 Azure 图标。 此时侧边栏中应显示 Azure Functions 区域。

    ![侧边栏中的 Azure Functions 区域](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

使用 Functions 扩展可以创建函数应用项目以及第一个函数。 以下步骤说明如何在新的 Functions 项目中创建 HTTP 触发的函数。 HTTP 触发器是用于演示的最简单函数触发器模板。

1. 从 **Azure：Functions** 中，选择“创建函数”图标：

    ![创建函数](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. 为函数应用项目选择文件夹，然后为函数项目选择 C#，为运行时选择 .NET 5 独立。 

1. 选择“HTTP 触发器”函数模板。

    ![选择 HTTP 触发器模板](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. 键入 HttpTriggerEncode 作为函数名称，按 Enter 键，接受 Company.Function 作为命名空间，然后选择“函数”作为访问权限。   调用函数终结点时，此授权级别需要提供[函数密钥](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。

    ![选择“函数”授权](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    此时将使用 HTTP 触发的函数的模板，以所选语言创建函数。

    ![Visual Studio Code 中的 HTTP 触发的函数模板](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>安装媒体服务和其他扩展

在终端窗口中运行 dotnet add package 命令，在项目中安装所需的扩展包。 以下命令将安装媒体服务包和示例所需的其他扩展。

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>生成的项目文件

项目模板将以所选语言创建一个项目，并安装所需的依赖项。 新项目包含以下文件：

* **host.json**：用于配置 Functions 主机。 在本地和 Azure 中运行函数时，将应用这些设置。 有关详细信息，请参阅 [host.json 参考](./../../azure-functions/functions-host-json.md)。

* **local.settings.json**：维护本地运行函数时使用的设置。 仅当在本地运行函数时，才使用这些设置。

    >[!IMPORTANT]
    >由于 local.settings.json 文件可能包含机密，因此需要将其从项目源代码管理中排除。

* 实现函数的 HttpTriggerEncode.cs 类文件。

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

这是函数的 C# 代码。 此代码的作用是使用媒体服务或源 URL，并通过媒体服务启动编码作业。 此代码使用某种转换（如果不存在，则会创建）。 创建转换时，此代码使用输入正文中提供的预设。 

>[!IMPORTANT]
>将 HttpTriggerEncode.cs 文件的全部内容替换为[此存储库中的 `HttpTriggerEncode.cs`](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs)。

完成函数的定义后，请选择“保存并运行”。

该函数的 Run 方法的源代码是：

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

使用以下内容更新文件（并替换值）。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>测试函数

在 VS Code 中本地运行该函数时，该函数应公开为： 

```url
http://localhost:7071/api/HttpTriggerEncode
```

要测试该函数，可以借助 Postman 使用 JSON 输入正文在此 URL 上执行 POST。

JSON 输入正文示例：

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

函数应返回 200 OK，输出正文包含作业和输出资产名称。

![使用 Postman 测试函数](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>后续步骤

此时，你已准备好开始开发调用媒体服务 API 的函数。

有关将 Azure Functions 与 Azure 媒体服务 v3 结合使用的详细信息和完整示例，请参阅[媒体服务 v3 Azure Functions 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)。
