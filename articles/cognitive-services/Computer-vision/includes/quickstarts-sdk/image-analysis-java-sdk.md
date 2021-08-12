---
title: 快速入门：适用于 Java 的图像分析客户端库
description: 在本快速入门中，了解适用于 Java 的图像分析客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 12a0becf7cec5dea032d6038aa5c09fdcf58de59
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362553"
---
<a name="HOLTop"></a>

使用图像分析客户端库可以分析图像的标签、文本说明、人脸、成人内容等。

[参考文档](/java/api/overview/azure/cognitiveservices/client/computervision) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[项目 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)中找到客户端库以及其他依赖项管理器的信息。

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义为一个 Java 应用程序，其入口点为 ImageAnalysisQuickstart 类。 它将导入计算机视觉库。

```kotlin
plugins {
    java
    application
}
application { 
    mainClass.set("ImageAnalysisQuickstart")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.6-beta")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件

从工作目录运行以下命令，以创建项目源文件夹：

```console
mkdir -p src/main/java
```

导航到新文件夹，并创建名为 ImageAnalysisQuickstart.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java) 上找到它，其中包含此快速入门中的代码示例。

定义类 ImageAnalysisQuickstart。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_classdef_1)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_classdef_2)]

在 ImageAnalysisQuickstart 类中，为资源的密钥和终结点创建变量。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的计算机视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_main)]


> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client&product=computer-vision&page=image-analysis-java-sdk)

## <a name="object-model"></a>对象模型

以下类和接口用于处理图像分析 Java SDK 的某些主要功能。

|名称|说明|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 所有计算机视觉功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| 此类来自客户端对象，它直接处理所有图像操作，例如图像分析、文本检测和缩略图生成。|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| 此枚举定义可在标准分析操作中执行的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的图像分析客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端 [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 对象。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#analyze-an-image) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client&product=computer-vision&page=image-analysis-java-sdk)

## <a name="analyze-an-image"></a>分析图像

以下代码定义方法 `AnalyzeLocalImage`，该方法使用客户端对象分析本地图像并输出结果。 该方法返回文本说明、分类、标记列表、检测到的人脸、成人内容标志、主颜色和图像类型。

> [!TIP]
> 还可以使用其 URL 分析远程图像。 请参阅 [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) 方法，例如 AnalyzeImage。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java) 上的示例代码以了解涉及远程图像的方案。

### <a name="set-up-test-image"></a>设置测试图像

首先，在项目的 **src/main/** 文件夹中创建 **resources/** 文件夹，并添加要分析的图像。 然后，将以下方法定义添加到 ImageAnalysisQuickstart 类。 请更改 `pathToLocalImage` 的值，使之与图像文件相匹配。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>指定视觉特性

接下来，指定要在分析中提取的视觉特征。 有关完整列表，请参阅 [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes) 枚举。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>分析
此块根据每个图像分析范围将详细结果输出到控制台。 **analyzeImageInStream** 方法将返回包含所有提取信息的 **ImageAnalysis** 对象。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_analyze)]

以下部分说明如何详细分析此信息。

### <a name="get-image-description"></a>获取图像说明

下面的代码获取为图像生成的描述文字列表。 有关详细信息，请参阅[描述图像](../../concept-describing-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>获取图像类别

下面的代码获取所检测到的图像类别。 有关详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>获取图像标记

以下代码获取图像中检测到的标记集。 有关详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>检测人脸

下面的代码返回图像中检测到的人脸及其矩形坐标，并选择人脸属性。 有关详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-objects"></a>检测物体

下面的代码返回图像中检测到的对象及其坐标。 有关详细信息，请参阅[物体检测](../../concept-object-detection.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_objects)]


### <a name="detect-brands"></a>检测品牌

下面的代码返回图像中检测到的品牌徽标及其坐标。 有关详细信息，请参阅[品牌检测](../../concept-brand-detection.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_brands)]



### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下代码输出图像中检测到的成人内容。 有关详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下代码输出图像中检测到的颜色属性，如主色和主题色。 有关详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>获取特定于域的内容

图像分析可以使用专用模型对图像作进一步分析。 有关详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_celebrities)]

以下代码分析了图像中检测到的地标的相关数据。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>获取图像类型

以下代码输出有关图像类型的信息&mdash;无论它是剪贴画还是线条图。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [我分析了图像](?success=analyze-image#run-the-application) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image&product=computer-vision&page=image-analysis-java-sdk)

### <a name="close-out-the-method"></a>结束方法

完成 try/catch 块并关闭方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java?name=snippet_analyze_catch)]


## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `gradle run` 命令运行应用程序：

```console
gradle run
```

> [!div class="nextstepaction"]
> [我运行了应用程序](?success=run-the-application#clean-up-resources) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application&product=computer-vision&page=image-analysis-java-sdk)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [我清理了资源](?success=clean-up-resources#next-steps) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources&product=computer-vision&page=image-analysis-java-sdk)

## <a name="next-steps"></a>后续步骤

在本快速入门中了解了安装图像分析客户端库和进行基本的图像分析调用的方法。 接下来，详细了解分析 API 功能。

> [!div class="nextstepaction"]
>[调用分析 API](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [图像分析概述](../../overview-image-analysis.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ImageAnalysisQuickstart.java) 上找到此示例的源代码。
