---
title: 快速入门：适用于 Go 的图像分析客户端库
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，开始使用适用于 Go 的图像分析客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 6a0d13dbc0beb90e0e276c5f594ff09c721f06f8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073765"
---
<a name="HOLTop"></a>

使用图像分析客户端库可以分析图像的标签、文本说明、人脸、成人内容等。

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

在首选 IDE 或文本编辑器中打开 `sample-app.go`。 然后添加包名称并导入以下库：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

此外，还可以在脚本的根目录中声明一个上下文。 你将需要此对象来执行大多数图像分析函数调用：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

接下来，将开始添加代码以执行不同的计算机视觉操作。

> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>对象模型

以下类和接口用于处理图像分析 Go SDK 的某些主要功能。

|名称|说明|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 所有计算机视觉功能（如图像分析和文本阅读）都需要此类。 可以使用订阅信息实例化此类，然后使用它来执行大多数图像操作。|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| 此类型包含 AnalyzeImage 函数调用的结果。 每个特定于类别的函数都具有相似的类型。|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| 此类型定义了可在标准分析操作中完成的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Go 的图像分析客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 此步骤假设已经为计算机视觉密钥和终结点（分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`）[创建了环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

创建 `main` 函数，并向其添加以下代码，以使用终结点和密钥实例化客户端。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#analyze-an-image) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)

## <a name="analyze-an-image"></a>分析图像

以下代码使用客户端对象分析远程映像，并将结果打印到控制台。 可获取文本说明、分类、标记列表、检测到的物体、检测到的品牌、检测到的人脸、成人内容标志、主要颜色和图像类型。

### <a name="set-up-test-image"></a>设置测试图像

首先，保存对要分析的图像的 URL 的引用。 将其放入 `main` 函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> 还可以分析本地图像。 请参阅 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 方法，例如 DescribeImageInStream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的示例代码，了解涉及本地图像的方案。

### <a name="specify-visual-features"></a>指定视觉特性

以下函数调用从示例图像中提取不同的视觉特征。 将在以下各节中定义这些函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>获取图像说明

以下函数获取为图像生成的描述文字列表。 有关图像描述的详细信息，请参阅[描述图像](../../concept-describing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>获取图像类别

以下函数获取已检测到的映像类别。 有关详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>获取图像标记

以下函数获取映像中检测到的标记集。 有关详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>检测物体

以下函数检测图像中的常见物体并将其打印到控制台。 有关详细信息，请参阅[物体检测](../../concept-object-detection.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>检测品牌

以下代码检测图像中的公司品牌和徽标，并将其输出到控制台。 有关详细信息，请参阅[品牌检测](../../concept-brand-detection.md)。

首先，在 `main` 函数内声明对新图像的引用。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

以下代码定义品牌检测函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>检测人脸

以下函数返回图像中检测到的人脸及其矩形坐标，以及某些人脸属性。 有关详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下函数打印图像中检测到的成人内容。 有关详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下函数打印图像中检测到的颜色属性，如主色和主题色。 有关详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>获取特定于域的内容

图像分析可以使用专用模型对图像作进一步分析。 有关详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

以下代码分析了图像中检测到的地标的相关数据。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>获取图像类型

以下函数将打印有关图像类型的信息，即表明它是剪贴画还是素描。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [我分析了图像](?success=analyze-image#run-the-application) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image)


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

在本快速入门中了解了安装图像分析客户端库和进行基本的图像分析调用的方法。 接下来，详细了解分析 API 功能。

> [!div class="nextstepaction"]
>[调用分析 API](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [图像分析概述](../../overview-image-analysis.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上找到此示例的源代码。

