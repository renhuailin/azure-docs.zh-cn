---
title: 内容审查器 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用适用于 Java 的 Azure 内容审查器客户端库。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: d1574d9728036bac393cbecb37e2097be6ef90e7
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105699"
---
适用于 Java 的 Azure 内容审查器客户端库入门。 请按照以下步骤安装 Maven 包并试用基本任务的示例代码。 

内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 使用 AI 支持型内容审核服务扫描文本、图像和视频，并自动应用内容标志。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。

使用适用于 Java 的内容审查器客户端库可以：

* 审查文本
* 审查图像

[参考文档](/java/api/overview/azure/cognitiveservices/client/contentmoderator) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[项目 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [示例](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="创建内容审查器资源"  target="_blank">创建内容审查器资源</a>，以获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到内容审查器。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
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

## <a name="install-the-client-library"></a>安装客户端库

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ContentModeratorQuickstart** 类。 它会导入内容审查器客户端库以及用于 JSON 序列化的 GSON SDK。

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件


从工作目录运行以下命令，以创建项目源文件夹：

```console
mkdir -p src/main/java
```

导航到新文件夹，创建名为 ContentModeratorQuickstart.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java) 上找到它，其中包含此快速入门中的代码示例。

在应用程序的 ContentModeratorQuickstart 类中，为资源的密钥和终结点创建变量。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的内容审查器资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

在应用程序的 main 方法中，添加对本快速入门中使用的方法的调用。 稍后将对这些调用进行定义。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Java 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
|[评审](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查文本](#moderate-text)
* [审查图像](#moderate-images)


## <a name="authenticate-the-client"></a>验证客户端

在应用程序的 `main` 方法中，使用订阅终结点值和订阅密钥创建一个 [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) 对象。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>审查文本

### <a name="set-up-sample-text"></a>设置示例文本

在 ContentModeratorQuickstart 类的顶部，定义对本地文本文件的引用。 在项目目录中添加一个 .txt 文件，然后输入要分析的文本。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>分析文本

创建一个新方法，读取 .txt 文件并在每一行调用 screenText 方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>打印文本审查结果

添加以下代码，将审查结果打印到项目目录中的 .json 文件中。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

结束 `try` 和 `catch` 语句以完成该方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>审查图像

### <a name="set-up-sample-image"></a>设置示例图像

在新方法中，使用指向图像的给定 URL 字符串构造一个 **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** 对象。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>使用帮助器类

然后，在 *ContentModeratorQuickstart* 文件中，将以下类定义添加到 **ContentModeratorQuickstart** 类中。 将在图像审查过程中使用此内部类。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>分析内容
以下代码行在给定 URL 处的图像中检查成人或猥亵内容。 有关这些术语的信息，请参阅《图像审查概念指南》。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>检查文本
以代码行在图像中检查可见文本。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>检查人脸
以代码行在图像中检查人脸。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

最后，将返回的信息存储在 `EvaluationData` 列表中。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>输出结果

在 `while` 循环的后面添加以下代码，用于将结果输出到控制台和输出文件 *src/main/resources/ModerationOutput.json*。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

结束 `try` 语句并添加 `catch` 语句以完成该方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `gradle run` 命令运行应用程序：

```console
gradle run
```

然后导航到 *src/main/resources/ModerationOutput.json* 文件并查看内容审查的结果。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Java 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
> [图像审查的概念](../../image-moderation-api.md)
