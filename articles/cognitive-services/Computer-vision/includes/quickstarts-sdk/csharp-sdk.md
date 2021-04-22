---
title: 快速入门：适用于 .NET 的光学字符识别客户端库
description: 在本快速入门中，开始使用适用于 .NET 的光学字符识别客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327551"
---
<a name="HOLTop"></a>

使用 OCR 客户端库从图像中读取印刷体文本和手写文本。

[参考文档](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 创建新的 .NET Core 应用程序。 

### <a name="install-the-client-library"></a>安装客户端库 

创建新项目后，右键单击“解决方案资源管理器”中的项目解决方案，然后选择“管理 NuGet 包”，以安装客户端库 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`。 选择版本 `7.0.0`，然后选择“安装”。 

#### <a name="cli"></a>[CLI](#tab/cli)

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `computer-vision-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。

```console
dotnet new console -n computer-vision-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的计算机视觉客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上找到它，其中包含此快速入门中的代码示例。

在首选的编辑器或 IDE 中，从项目目录打开 *Program.cs* 文件。

### <a name="find-the-subscription-key-and-endpoint"></a>查找订阅密钥和终结点

转到 Azure 门户。 如果在“先决条件”部分中创建的计算机视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 

在应用程序的“Program”类中，为计算机视觉的订阅密钥和终结点创建变量。 将你的订阅密钥和终结点粘贴到以下代码中的指定位置。 计算机视觉终结点的格式为 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> 记住在完成后将订阅密钥从代码中删除，永远不要公开发布它。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../../key-vault/general/overview.md)。

在应用程序的 `Main` 方法中，添加对本快速入门中使用的方法的调用。 稍后将创建这些内容。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>对象模型

以下类和接口用于处理 OCR .NET SDK 的某些主要功能。

|名称|说明|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 所有计算机视觉功能都需要此类。 可以使用订阅信息实例化此类，然后使用它来执行大多数图像操作。|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| 此类包含 ComputerVisionClient 的其他方法。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的 OCR 客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

在 Program 类的新方法中，使用终结点和订阅密钥实例化客户端。 使用订阅密钥创建一个 [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials) 对象，并在终结点中使用该对象创建一个 [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#read-printed-and-handwritten-text) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

OCR 服务可以读取图像中的可见文本，并将其转换为字符流。 有关文本识别的更多详细信息，请参阅[光学字符识别 (OCR)](../../overview-ocr.md) 概述。 本部分中的代码使用[适用于 Read 3.0 的最新计算机视觉 SDK 版本](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)，并定义了 `BatchReadFileUrl` 方法，该方法使用客户端对象来检测和提取图像中的文本。

> [!TIP]
> 还可以从本地图像提取文本。 请参阅 [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 方法，例如 ReadInStreamAsync。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上的示例代码，了解涉及本地图像的方案。

### <a name="set-up-test-image"></a>设置测试图像

在 Program 类中，保存要从中提取文本的图像的 URL 的引用。 此代码段包含打印文本和手写文本的示例图像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>调用读取 API

定义用于读取文本的新方法。 添加以下代码，该代码对给定图像调用 ReadAsync 方法。 这会返回一个操作 ID 并启动异步进程来读取图像的内容。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>获取读取结果

接下来，获取从 ReadAsync 调用返回的操作 ID，并使用它查询服务以获取操作结果。 下面的代码检查操作，直到返回结果。 然后，它将提取的文本数据输出到控制台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>显示读取结果

添加以下代码来分析和显示检索到的文本数据，并完成方法定义。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>运行应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

单击 IDE 窗口顶部的“调试”按钮，运行应用程序。

#### <a name="cli"></a>[CLI](#tab/cli)

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门了解了安装 OCR 客户端库和使用读取 API 的方法。 接下来，详细了解读取 API 功能。

> [!div class="nextstepaction"]
>[调用读取 API](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 概述](../../overview-ocr.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上找到此示例的源代码。
