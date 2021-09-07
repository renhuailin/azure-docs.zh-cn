---
title: 人脸 JavaScript 客户端库快速入门
description: 使用适用于 JavaScript 的人脸客户端库来检测人脸、查找相似的人脸（按图像进行人脸搜索）、识别人脸（人脸识别搜索）并迁移人脸数据。
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 56f116b8c7b90f74da86a792242866d5a88ebff4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442380"
---
## <a name="quickstart-face-client-library-for-javascript"></a>快速入门：适用于 JavaScript 的人脸客户端库

开始使用适用于 JavaScript 的人脸客户端库进行人脸识别。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过人脸服务，可以访问用于检测和识别图像中的人脸的高级算法。

使用适用于 JavaScript 的人脸客户端库可以：

* [检测和分析人脸](#detect-and-analyze-faces)
* [识别人脸](#identify-a-face)
* [查找相似人脸](#find-similar-faces)

[参考文档](/javascript/api/overview/azure/cognitive-services/face-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [包 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [示例](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Node.js](https://nodejs.org/en/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* 拥有 Azure 订阅后，请在 Azure 门户中[创建人脸资源](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)，以获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

### <a name="install-the-client-library"></a>安装客户端库 

安装 `ms-rest-azure` 和 `azure-cognitiveservices-face` NPM 包:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 `index.js` 的文件，并导入以下库：

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub]() 上找到它，其中包含此快速入门中的代码示例。

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

为资源的 Azure 终结点和密钥创建变量。 

> [!IMPORTANT]
> 转到 Azure 门户。 如果你在“先决条件”部分创建的人脸资源部署成功，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到密钥和终结点 。 
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services-security.md)文章。

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>对象模型

以下类和接口将处理人脸 .NET 客户端库的某些主要功能：

|名称|说明|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | 此类代表使用人脸服务的授权，使用所有人脸功能时都需要用到它。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。 |
|[人脸](/javascript/api/@azure/cognitiveservices-face/face)|此类处理可对人脸执行的基本检测和识别任务。 |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|此类代表已从图像中的单个人脸检测到的所有数据。 可以使用它来检索有关人脸的详细信息。|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|此类管理云中存储的 **FaceList** 构造，这些构造存储各种不同的人脸。 |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| 此类管理云中存储的 **Person** 构造，这些构造存储属于单个人员的一组人脸。|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| 此类管理云中存储的 **PersonGroup** 构造，这些构造存储各种不同的 **Person** 对象。 |

## <a name="code-examples"></a>代码示例

以下代码片段演示如何使用适用于 .NET 的人脸客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [检测和分析人脸](#detect-and-analyze-faces)
* [识别人脸](#identify-a-face)
* [查找相似人脸](#find-similar-faces)

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js) 上找到它，其中包含此快速入门中的代码示例。

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥实例化某个客户端。 使用密钥创建 [ApiKeyCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials) 对象，并使用该对象在终结点中创建 [FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) 对象 。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>声明全局值和 helper 函数

你稍后将添加的一些人脸操作需要以下全局值。

该 URL 指向示例图像的文件夹。 UUID 将用作要创建的 PersonGroup 的名称和 ID。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

你将使用以下函数来等待 PersonGroup 的训练完成。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-and-analyze-faces"></a>检测和分析人脸

人脸检测是进行人脸分析和身份验证的第一步。 本部分介绍如何返回额外的人脸属性数据。 如果只想检测人脸以进行人脸识别或验证，请跳过后面的部分。


### <a name="get-detected-face-objects"></a>获取检测到的人脸对象

创建新方法以检测人脸。 `DetectFaceExtract` 方法处理给定 URL 处的三个图像，并在程序内存中创建 [DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface) 对象的列表。 **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** 值列表指定要提取的特征。 

然后，`DetectFaceExtract` 方法会分析并输出每个检测到的人脸的属性数据。 每个属性必须在原始人脸检测 API 调用中单独指定（在 **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** 列表中）。 下面的代码处理每个属性，但你可能只需要使用一个或一些属性。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> 还可以检测本地图像中的人脸。 请参阅 [Face](/javascript/api/@azure/cognitiveservices-face/face) 方法，如 [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____)。



## <a name="identify-a-face"></a>识别人脸

[识别](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____)操作获取一个人（或多个人）的图像，并查找与图像中的每个人脸相关联的存储的人员对象（人脸识别搜索）。 它将每个检测到的人脸与某个 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)（面部特征已知的不同 [Person](/javascript/api/@azure/cognitiveservices-face/person) 对象的数据库）进行比较。 为了执行“识别”操作，你首先需要创建并训练 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)。

### <a name="add-faces-to-persongroup"></a>将人脸添加到 PersonGroup

创建以下函数以将人脸添加到 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>等待 PersonGroup 训练

创建以下帮助程序函数，静待 PersonGroup完成训练。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>创建人员组

以下代码：
- 创建 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)
- 通过调用你以前定义的 `AddFacesToPersonGroup`，将人脸添加到 PersonGroup。
- 训练 PersonGroup。
- 识别该 PersonGroup 中的人脸。

现已准备好在“验证”、“识别”或“分组”操作中使用此 PersonGroup 及其关联的 Person 对象 。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> 你还可以从本地图像创建 PersonGroup。 请参阅 [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) 方法，如 [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_)。

## <a name="find-similar-faces"></a>查找相似人脸

以下代码采用检测到的单个人脸（源），并搜索其他一组人脸（目标），以找到匹配项（按图像进行人脸搜索）。 找到匹配项后，它会将匹配的人脸的 ID 输出到控制台。

### <a name="detect-faces-for-comparison"></a>检测人脸以进行比较

首先定义另一个人脸检测方法。 需要先检测图像中的人脸，然后才能对其进行比较；此检测方法已针对比较操作进行优化。 它不会提取以上部分所示的详细人脸属性，而是使用另一个识别模型。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>查找匹配项

以下方法检测一组目标图像和单个源图像中的人脸。 然后，它将比较这些人脸，并查找与源图像类似的所有目标图像。 最后，该方法会将匹配项详细信息输出到控制台。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="main"></a>Main

最后，创建并调用 `main` 函数。

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

此快速入门介绍了如何使用适用于 JavaScript 的人脸客户端库来执行基本的人脸识别任务。 接下来，了解不同的人脸检测模型，学习如何为你的用例指定适当的模型。

> [!div class="nextstepaction"]
> [指定人脸检测模型版本](../../Face-API-How-to-Topics/specify-detection-model.md)

* [什么是人脸服务？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js) 上找到此示例的源代码。
