---
title: 快速入门：适用于 Go 的光学字符识别客户端库
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，开始使用适用于 Go 的光学字符识别客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: b935b8a467097eb60c095c5d317f13693ef68b62
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284740"
---
<a name="HOLTop"></a>

使用 OCR 客户端库从图像中读取印刷体文本和手写文本。

[参考文档](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [库源代码](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [包](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Go](https://golang.org/dl/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-go-project-directory"></a>创建 Go 项目目录

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个名为 `my-app` 的新工作区并导航到该工作区。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

工作区包含三个文件夹：

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包都将在此目录中。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件使用 `.a` 扩展名。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 若要了解有关 Go 工作区结构的详细信息，请参阅 [Go 语言文档](https://golang.org/doc/code.html#Workspaces)。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

### <a name="install-the-client-library-for-go"></a>安装适用于 Go 的客户端库

接下来，安装适用于 Go 的客户端库：

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

或者，如果使用 dep，则在存储库中运行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>创建 Go 应用程序 

接下来，在 src 目录中创建名为 `sample-app.go` 的文件：

```bash
cd src
touch sample-app.go
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上找到它，其中包含此快速入门中的代码示例。

在首选 IDE 或文本编辑器中打开 `sample-app.go`。

在脚本的根目录中声明上下文。 你将需要此对象来执行大多数计算机视觉函数调用。

### <a name="find-the-subscription-key-and-endpoint"></a>查找订阅密钥和终结点

转到 Azure 门户。 如果在“先决条件”部分中创建的计算机视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 

为计算机视觉订阅密钥和终结点创建变量。 将你的订阅密钥和终结点粘贴到以下代码中的指定位置。 计算机视觉终结点的格式为 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 记住在完成后将订阅密钥从代码中删除，永远不要公开发布它。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../../key-vault/general/overview.md)。

接下来，你将开始添加代码以执行不同的 OCR 操作。

> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>对象模型

以下类和接口用于处理 OCR Go SDK 的某些主要功能。

|名称|说明|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 所有计算机视觉功能（如图像分析和文本阅读）都需要此类。 可以使用订阅信息实例化此类，然后使用它来执行大多数图像操作。|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| 此类型包含批读取操作的结果。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Go 的 OCR 客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 此步骤假设已经为计算机视觉密钥和终结点（分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`）[创建了环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

创建 `main` 函数，并向其添加以下代码，以使用终结点和密钥实例化客户端。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#read-printed-and-handwritten-text) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

OCR 服务可以读取图像中的可见文本，并将其转换为字符流。 本部分的代码定义了函数 `RecognizeTextReadAPIRemoteImage`，该函数使用客户端对象检测并提取图像中的印刷体文本或手写文本。

在 `main` 函数中添加示例图像引用和函数调用。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> 还可以从本地图像提取文本。 请参阅 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 方法，例如 BatchReadFileInStream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的示例代码，了解涉及本地图像的方案。

### <a name="call-the-read-api"></a>调用读取 API

定义用于读取文本的新函数 `RecognizeTextReadAPIRemoteImage`。 添加以下代码，该代码对给定图像调用 BatchReadFile 方法。 此方法返回一个操作 ID 并启动异步进程来读取图像的内容。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>获取读取结果

接下来，获取 BatchReadFile 调用返回的操作 ID，并将其用于 GetReadOperationResult 方法，向服务查询操作结果 。 下面的代码每隔一秒钟检查一次操作，直到返回结果。 然后，它将提取的文本数据输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>显示读取结果

添加以下代码来分析和显示检索到的文本数据，并完成函数定义。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [我阅读了文字](?success=read-printed-handwritten-text#run-the-application) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `go run` 命令运行应用程序。

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [我运行了应用程序](?success=run-the-application#clean-up-resources) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [我清理了资源](?success=clean-up-resources#next-steps) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [OCR API 参考 (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [OCR 概述](../../overview-ocr.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上找到此示例的源代码。
