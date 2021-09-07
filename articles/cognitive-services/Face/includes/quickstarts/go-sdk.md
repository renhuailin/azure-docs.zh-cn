---
title: 人脸 Go 客户端库快速入门
description: 使用适用于 Go 的人脸客户端库来检测人脸、查找相似的人脸（按图像进行人脸搜索）、识别人脸（人脸识别搜索）并迁移人脸数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 5534fc3b82119295dc744e98054fead2f12d4a7d
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442386"
---
开始使用适用于 Go 的人脸客户端库进行人脸识别。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用适用于 Go 的人脸服务客户端库可以：

* [检测和分析人脸](#detect-and-analyze-faces)
* [识别人脸](#identify-a-face)
* [验证人脸](#verify-faces)
* [查找相似人脸](#find-similar-faces)

[参考文档](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [库源代码](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK 下载](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Go](https://golang.org/dl/)
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="创建人脸资源"  target="_blank">创建人脸资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 获取密钥和终结点后，请为该密钥和终结点[创建环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)，分别命名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`。

## <a name="setting-up"></a>设置

### <a name="create-a-go-project-directory"></a>创建 Go 项目目录

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个名为 `my-app` 的新工作区并导航到该工作区。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

工作区包含三个文件夹：

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包都驻留在此文件夹中。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件的扩展名为 `.a`。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 若要了解有关 Go 工作区结构的详细信息，请参阅 [Go 语言文档](https://golang.org/doc/code.html#Workspaces)。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

### <a name="install-the-client-library-for-go"></a>安装适用于 Go 的客户端库

接下来，安装适用于 Go 的客户端库：

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

或者，如果使用 dep，则在存储库中运行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>创建 Go 应用程序

接下来，在 src 目录中创建名为 `sample-app.go` 的文件：

```bash
cd src
touch sample-app.go
```

在首选 IDE 或文本编辑器中打开 `sample-app.go`。 然后添加包名称并导入以下库：

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

接下来，开始添加代码以执行不同的人脸服务操作。

## <a name="object-model"></a>对象模型

以下类和接口用于处理人脸服务 Go 客户端库的某些主要功能。

|名称|说明|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[客户端](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|此类管理快照功能。 可以使用它来暂时保存所有基于云的人脸数据，并将这些数据迁移到新的 Azure 订阅。 |

## <a name="code-examples"></a>代码示例

这些代码示例演示如何使用适用于 Go 的人脸服务客户端库来完成基本任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测和分析人脸](#detect-and-analyze-faces)
* [识别人脸](#identify-a-face)
* [验证人脸](#verify-faces)
* [查找相似人脸](#find-similar-faces)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE] 
> 本快速入门假设已经为人脸密钥和终结点（分别名为 `FACE_SUBSCRIPTION_KEY` 和 `FACE_ENDPOINT`）[创建了环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

创建 **main** 函数，并在其中添加以下代码，以使用终结点和密钥实例化客户端。 使用密钥创建 **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** 对象，然后在终结点上使用该对象创建 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** 对象。 此代码还将实例化一个上下文对象，创建客户端对象时需要该上下文对象。 它还会定义一个远程位置，可在其中找到本快速入门中的一些示例图像。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-and-analyze-faces"></a>检测和分析人脸

人脸检测是进行人脸分析和身份验证的第一步。 本部分介绍如何返回额外的人脸属性数据。 如果只想检测人脸以进行人脸识别或验证，请跳到后面的部分。


在 **main** 方法中添加以下代码。 此代码定义一个远程示例图像，并指定要从该图像中提取哪些人脸特征。 它还会指定要使用哪个 AI 模型从检测到的人脸中提取数据。 有关这些选项的信息，请参阅[指定识别模型](../../Face-API-How-to-Topics/specify-recognition-model.md)。 最后， **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** 方法针对图像执行人脸检测操作，并将结果保存到程序内存中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> 还可以检测本地图像中的人脸。 请参阅 [Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) 方法，如 DetectWithStream。

### <a name="display-detected-face-data"></a>显示检测到的人脸数据

下一个代码块采用 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** 对象数组中的第一个元素，并将其特性输出到控制台。 如果使用了包含多个人脸的图像，则应改为迭代该数组。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]





## <a name="identify-a-face"></a>识别人脸

识别操作采用一个（或多个）人员的图像，并在图像中查找每个人脸的标识（人脸识别搜索）。 它将每个检测到的人脸与某个 **PersonGroup**（面部特征已知的不同 **Person** 对象的数据库）进行比较。

### <a name="get-person-images"></a>获取人的图像

若要逐步完成此方案，需将以下图像保存到项目的根目录： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 。

此图像组包含三组单一人脸图像（对应于三个不同的人）。 该代码定义三个 **PersonGroup Person** 对象，并将其关联到以 `woman`、`man` 和 `child` 开头的图像文件。

### <a name="create-a-persongroup"></a>创建人员组

下载图像后，请将以下代码添加到 **main** 方法的底部。 此代码对 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** 对象进行身份验证，然后使用它来定义新的 **PersonGroup**。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>创建 PersonGroup Person

下一个代码块对 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** 进行身份验证，并使用它来定义三个新的 **PersonGroup Person** 对象。 其中的每个对象表示图像集中的一个人。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>将人脸添加到 Person

以下代码按图像前缀排序图像，检测人脸，并根据图像文件名将人脸分配到每个相关的 **PersonGroup Person** 对象。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> 还可以从 URL 引用的远程图像创建 PersonGroup。 请参阅 [ PersonGroupPersonClient ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) 方法，例如 AddFaceFromURL。

### <a name="train-the-persongroup"></a>训练 PersonGroup

分配人脸后，请训练 **PersonGroup**，使其能够识别与其每个 **Person** 对象关联的视觉特征。 以下代码调用异步 **train** 方法并轮询结果，然后将状态输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> 人脸 API 在一组预构建的模型呢上运行，这些模型在本质上是静态的（模型的性能不会因为运行服务而提高或降低）。 如果 Microsoft 更新模型的后端，但不迁移整个新模型版本，那么模型生成的结果可能会变化。 若要使用更新的模型版本，可重新训练 PersonGroup，将更新的模型指定为具有相同注册映像的参数。

### <a name="get-a-test-image"></a>获取测试图像

以下代码在项目根目录中查找图像 _test-image-person-group.jpg_，并将其载入程序内存。 可以在用于创建 PersonGroup 的图像所在的同一个存储库中找到此图像： https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>检测测试图像中的源人脸

下一个代码块针对测试图像执行普通的人脸检测，以检索所有人脸并将其保存到数组中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces-from-source-image"></a>识别源图像中的人脸

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** 方法采用检测到的人脸的数组，并将其与给定的 **PersonGroup**（已在前一部分定义并训练）进行比较。 如果检测到的某个人脸与组中的某个人相匹配，则它会保存结果。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

然后，此代码将详细的匹配结果输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


### <a name="verify-faces"></a>验证人脸

验证操作采用某个人脸 ID 和其他人脸 ID 或 Person 对象，并确定它们是否属于同一个人。 验证可用于复查识别操作返回的人脸匹配。

以下代码检测两个源图像中的人脸，然后根据目标图像中检测到的人脸来验证源图像中的每个人脸。

### <a name="get-test-images"></a>获取测试图像

以下代码块声明将指向验证操作的目标和源图像的变量。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>检测人脸进行验证

以下代码检测源和目标图像中的人脸并将其保存到变量中。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>获取验证结果

以下代码将每个源图像与目标图像进行比较并打印出一条消息，指示它们是否属于同一个人。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项（按图像进行人脸搜索）。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先，保存对在[检测和分析](#detect-and-analyze-faces)部分中检测到的人脸的引用。 此人脸将是源。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

然后输入以下代码，以检测不同图像中的一组人脸。 这些人脸将是目标。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>查找匹配项

以下代码使用 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** 方法来查找与源人脸匹配的所有目标人脸。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>输出匹配项

以下代码将匹配详细信息输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="run-the-application"></a>运行应用程序

使用 `go run <app-name>` 命令从应用程序目录运行人脸识别应用。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

如果你在本快速入门中创建了一个 **PersonGroup** 但想要删除它，请调用 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** 方法。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用适用于 Go 的人脸客户端库来执行基本人脸识别任务。 接下来，了解不同的人脸检测模型，学习如何为你的用例指定适当的模型。

> [!div class="nextstepaction"]
> [指定人脸检测模型版本](../../Face-API-How-to-Topics/specify-detection-model.md)

* [什么是人脸服务？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go) 上找到此示例的源代码。
