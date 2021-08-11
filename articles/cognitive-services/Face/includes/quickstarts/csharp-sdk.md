---
title: 人脸 .NET 客户端库快速入门
description: 使用适用于 .NET 的人脸客户端库来检测人脸、查找相似的人脸（按图像进行人脸搜索）、识别人脸（人脸识别搜索）并迁移人脸数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: ee861896020f41dd841f538f546242a65992b8d3
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364866"
---
开始使用适用于 .NET 的人脸客户端库进行人脸识别。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用适用于 .NET 的人脸客户端库可以：

* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建人员组](#create-a-persongroup)
* [识别人脸](#identify-a-face)

[参考文档](/dotnet/api/overview/azure/cognitiveservices/face-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.7.0-preview.1) | [示例](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>先决条件


* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="创建人脸资源"  target="_blank">创建人脸资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 创建新的 .NET Core 应用程序。 

### <a name="install-the-client-library"></a>安装客户端库 

创建新项目后，右键单击“解决方案资源管理器”中的项目解决方案，然后选择“管理 NuGet 包”，以安装客户端库 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Microsoft.Azure.CognitiveServices.Vision.Face`。 选择版本 `2.7.0-preview.1`，然后选择“安装”。 

#### <a name="cli"></a>[CLI](#tab/cli)

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `face-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。 

```console
dotnet new console -n face-quickstart
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

在应用程序目录中，使用以下命令安装适用于 .NET 的人脸客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.7.0-preview.1
```

---

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) 上找到它，其中包含此快速入门中的代码示例。


从项目目录中，打开 Program.cs 文件，并添加以下 `using` 指令：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

在应用程序的“Program”类中，为资源的密钥和终结点创建变量。


> [!IMPORTANT]
> 转到 Azure 门户。 如果你在“先决条件”部分创建的人脸资源部署成功，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

在应用程序的“Main”方法中，添加对本快速入门中使用的方法的调用。 稍后将实现这些操作。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>对象模型

以下类和接口将处理人脸 .NET 客户端库的某些主要功能：

|名称|说明|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |

## <a name="code-examples"></a>代码示例

以下代码片段演示如何使用适用于 .NET 的人脸客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测图像中的人脸](#detect-faces-in-an-image)
* [查找相似人脸](#find-similar-faces)
* [创建人员组](#create-a-persongroup)
* [识别人脸](#identify-a-face)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端。 使用密钥创建一个 **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** 对象，并在终结点中使用该对象创建一个 **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** 对象。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>声明帮助程序字段

你稍后将添加的几个人脸操作需要以下字段。 在“Program”类的根目录中定义以下 URL 字符串。 此 URL 指向示例图像的文件夹。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

在“Main”方法中，定义字符串以指向不同的识别模型类型。 稍后，你将能够指定要用于人脸检测的识别模型。 有关这些选项的信息，请参阅[指定识别模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>在图像中检测人脸

### <a name="get-detected-face-objects"></a>获取检测到的人脸对象

创建新方法以检测人脸。 `DetectFaceExtract` 方法处理给定 URL 处的三个图像，并在程序内存中创建 [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) 对象的列表。 **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** 值列表指定要提取的特征。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> 还可以检测本地图像中的人脸。 请参阅 [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) 方法，例如 DetectWithStreamAsync。

### <a name="display-detected-face-data"></a>显示检测到的人脸数据

`DetectFaceExtract` 方法的其余部分将分析和打印每个检测到的人脸的属性数据。 每个属性必须在原始人脸检测 API 调用中单独指定（在 **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** 列表中）。 下面的代码处理每个属性，但你可能只需要使用一个或一些属性。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项（按图像进行人脸搜索）。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先定义另一个人脸检测方法。 需要先检测图像中的人脸，然后才能对其进行比较；此检测方法已针对比较操作进行优化。 它不会提取以上部分所示的详细人脸属性，而是使用另一个识别模型。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>查找匹配项

以下方法检测一组目标图像和单个源图像中的人脸。 然后，它将比较这些人脸，并查找与源图像类似的所有目标图像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>输出匹配项

以下代码将匹配详细信息输出到控制台：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>识别人脸

识别操作采用一个（或多个）人员的图像，并在图像中查找每个人脸的标识（人脸识别搜索）。 它将每个检测到的人脸与某个 **PersonGroup**（面部特征已知的不同 **Person** 对象的数据库）进行比较。 为了执行“识别”操作，你首先需要创建并训练 PersonGroup

### <a name="create-a-persongroup"></a>创建人员组

以下代码创建包含六个不同 **Person** 对象的 **PersonGroup**。 它将每个 **Person** 与一组示例图像相关联，然后进行训练以按面部特征识别每个人。 **Person** 和 **PersonGroup** 对象在验证、识别和分组操作中使用。

在类的根目录中声明一个字符串变量，用于表示要创建的 **PersonGroup** 的 ID。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

在新方法中添加以下代码。 此方法将执行“识别”操作。 第一个代码块将人员的姓名与其示例图像相关联。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

请注意，此代码定义 `sourceImageFileName` 变量。 此变量对应于源图像 &mdash; 包含要识别的人员的图像。

接下来添加以下代码，以便为字典中的每个人员创建一个 **Person** 对象，并从相应的图像添加人脸数据。 每个 **Person** 对象通过其唯一 ID 字符串来与同一个 **PersonGroup** 相关联。 请记得将变量 `client`、 `url` 和 `RECOGNITION_MODEL1` 传入此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> 你还可以从本地图像创建 PersonGroup。 请参阅 [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) 方法，例如 AddFaceFromStreamAsync.

### <a name="train-the-persongroup"></a>训练 PersonGroup

从图像中提取人脸数据并将其分类成不同的 **Person** 对象后，必须训练 **PersonGroup** 才能识别与其每个 **Person** 对象关联的视觉特征。 以下代码调用异步 **train** 方法并轮询结果，然后将状态输出到控制台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> 人脸 API 在一组预构建的模型呢上运行，这些模型在本质上是静态的（模型的性能不会因为运行服务而提高或降低）。 如果 Microsoft 更新模型的后端，但不迁移整个新模型版本，那么模型生成的结果可能会变化。 若要使用更新的模型版本，可重新训练 PersonGroup，将更新的模型指定为具有相同注册映像的参数。

现已准备好在验证、识别或分组操作中使用此 **Person** 组及其关联的 **Person** 对象。

### <a name="identify-faces"></a>标识人脸

以下代码采用源映像，创建在图像中检测到的所有人脸的列表。 将会根据 **PersonGroup** 识别这些人脸。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

下一代码片段将调用 IdentifyAsync 操作，并将结果输出到控制台。 此处，服务会尝试将源图像中的每个人脸与给定 **PersonGroup** 中的某个 **Person** 进行匹配。 Identify 方法就此结束。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

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

如果你在本快速入门中创建了 **PersonGroup** 并想要删除它，请在程序中运行以下代码：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

使用以下代码定义删除方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用适用于 .NET 的人脸客户端库来执行基本人脸识别任务。 接下来，了解不同的人脸检测模型，学习如何为你的用例指定适当的模型。

> [!div class="nextstepaction"]
> [指定人脸检测模型版本](../../Face-API-How-to-Topics/specify-detection-model.md)

* [什么是人脸服务？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) 上找到此示例的源代码。
