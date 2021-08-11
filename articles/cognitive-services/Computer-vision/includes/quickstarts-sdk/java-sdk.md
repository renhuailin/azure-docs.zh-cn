---
title: 快速入门：适用于 Java 的光学字符识别客户端库
description: 在本快速入门中，开始使用适用于 Java 的光学字符识别客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 0df856c00a77fa73ec5065f3e62febb55f1423d0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362717"
---
<a name="HOLTop"></a>

使用光学字符识别客户端库读取图像中的印刷体文本和手写文本。

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

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义为一个 Java 应用程序，其入口点为 ComputerVisionQuickstart 类。 它将导入计算机视觉库。

```kotlin
plugins {
    java
    application
}
application { 
    mainClass.set("ComputerVisionQuickstart")
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

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到它，其中包含此快速入门中的代码示例。

导航到新文件夹，创建名为 ComputerVisionQuickstart.java 的文件。 在你喜欢的编辑器或 IDE 中打开该文件。

### <a name="find-the-subscription-key-and-endpoint"></a>查找订阅密钥和终结点

转到 Azure 门户。 如果在“先决条件”部分中创建的计算机视觉资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 

定义 ComputerVisionQuickstart 类。 为计算机视觉订阅密钥和终结点创建变量。 将你的订阅密钥和终结点粘贴到以下代码中的指定位置。 计算机视觉终结点的格式为 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 记住在完成后将订阅密钥从代码中删除，永远不要公开发布它。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../../key-vault/general/overview.md)。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_main)]


> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client&product=computer-vision&page=java-sdk)

## <a name="object-model"></a>对象模型

以下类和接口用于处理 OCR Java SDK 的某些主要功能。

|名称|说明|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 所有计算机视觉功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的 OCR 客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端 [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 对象。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#read-printed-and-handwritten-text) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client&product=computer-vision&page=java-sdk)



## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

OCR 服务可以读取图像中的可见文本，并将其转换为字符流。 本部分定义方法 `ReadFromFile`，该方法采用本地文件路径并将图像的文本输出到控制台。

> [!TIP]
> 还可以读取 URL 引用的远程图像中的文本。 请参阅 [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) 方法，例如 read。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上的示例代码以了解涉及远程图像的方案。

### <a name="set-up-test-image"></a>设置测试图像

在项目的 src/main/ 文件夹中创建 resources/ 文件夹，并添加要从中读取文本的图像 。 可下载[示例映像](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)在此使用。

然后，将以下方法定义添加到 ComputerVisionQuickstart 类。 请更改 `localFilePath` 的值，使之与图像文件相匹配。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>调用读取 API

然后，添加以下代码调用给定图像的 readInStreamWithServiceResponseAsync 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

下面的代码块从 Read 调用的响应中提取操作 ID。 它将此 ID 与 helper 方法一起使用，以将文本读取结果输出到控制台。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

结束 try/catch 块和方法定义。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>获取读取结果

然后，添加 helper 方法的定义。 此方法使用上一步中的操作 ID 来查询读取操作并在 OCR 结果可用时获取该结果。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

此方法的其余部分会分析 OCR 结果，并将其输出到控制台。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

最后，添加上面使用的其他 helper 方法，该方法从初始响应中提取操作 ID。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [我阅读了文字](?success=read-printed-handwritten-text#run-the-application) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text&product=computer-vision&page=java-sdk)

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
> [我运行了应用程序](?success=run-the-application#clean-up-resources) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application&product=computer-vision&page=java-sdk)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [我清理了资源](?success=clean-up-resources#next-steps) [我遇到问题](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources&product=computer-vision&page=java-sdk)

## <a name="next-steps"></a>后续步骤

本快速入门了解了安装 OCR 客户端库和使用读取 API 的方法。 接下来，详细了解读取 API 功能。

> [!div class="nextstepaction"]
>[调用读取 API](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 概述](../../overview-ocr.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上找到此示例的源代码。
