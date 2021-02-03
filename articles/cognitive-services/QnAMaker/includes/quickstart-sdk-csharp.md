---
title: 快速入门：适用于 .NET 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 .NET 的 QnA Maker 客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: e75fdbe49910f9d6fe4fe25a7996fccf415fdf35
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947363"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以使用适用于 .NET 的 QnA Maker 客户端库执行以下操作：

 * 创建知识库
 * 更新知识库
 * 发布知识库
 * 获取预测运行时终结点密钥
 * 等待长时间运行的任务
 * 下载知识库
 * 从知识库获取答案
 * 删除知识库

[参考文档](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [C# 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以使用适用于 .NET 的 QnA Maker 客户端库执行以下操作：

 * 创建知识库
 * 更新知识库
 * 发布知识库
 * 等待长时间运行的任务
 * 下载知识库
 * 从知识库获取答案
 * 删除知识库

[参考文档](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [C# 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 拥有 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)来获取创作密钥和资源名称。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和资源名称，以便将应用程序连接到 QnA Maker API。 你稍后会在本快速入门中将密钥和资源名称粘贴到下文的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。
    * 注意：请务必选择“托管”复选框。
    * 部署 QnA Maker 资源后，选择“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

---

## <a name="setting-up"></a>设置

### <a name="visual-studio-ide"></a>Visual Studio IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

使用 Visual Studio 创建一个 .NET Core 应用程序并安装客户端库，方法是右键单击“解决方案资源管理器”中的解决方案，然后选择“管理 NuGet 包” 。 在打开的包管理器中，选择“浏览”，然后搜索 `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`。 选择版本 `2.0.1`，然后选择“安装”。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

使用 Visual Studio 创建一个 .NET Core 应用程序并安装客户端库，方法是右键单击“解决方案资源管理器”中的解决方案，然后选择“管理 NuGet 包” 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`。 选择版本 `3.0.0-preview.1`，然后选择“安装”。

---

### <a name="cli"></a>CLI

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `qna-maker-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。

```console
dotnet new console -n qna-maker-quickstart
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

在应用程序目录中，使用以下命令安装适用于 .NET 的 QnA Maker 客户端库：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) 上找到它，其中包含此快速入门中的代码示例。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs) 上找到它，其中包含此快速入门中的代码示例。

---

### <a name="using-directives"></a>using 指令

从项目目录中，打开 Program.cs 文件，并添加以下 `using` 指令：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>订阅密钥和资源终结点

在应用程序的 `Main` 方法中，添加变量和代码（如以下部分所示），以使用本快速入门中的常见任务。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT 和 QNA_MAKER_RUNTIME_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- QNA_MAKER_RUNTIME_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.azurewebsites.net` 格式。
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY 和 QNA_MAKER_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---

## <a name="object-models"></a>对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为 [主动学习](../concepts/active-learning-suggestions.md)的一部分）。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[QnA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) 使用以下对象模型：
* [QnAMakerClient](#qnamakerclient-object-model) 是用于创建、管理、发布、下载和查询知识库的对象。

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端以后，使用[知识库](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)属性创建、管理和发布知识库。

通过发送 JSON 对象来管理知识库。 对于即时操作，方法通常返回一个指示状态的 JSON 对象。 对于长时间运行的操作，响应是操作 ID。 使用操作 ID 调用 [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) 方法，确定[请求状态](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

预测 QnA Maker 客户端是一个 [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) 对象，它使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证；Microsoft.Rest.ServiceClientCredentials 包含预测运行时密钥，发布知识库后进行创作客户端调用 `client.EndpointKeys.GetKeys` 可返回该密钥。

使用 [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) 方法从查询运行时获取答案。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

QnA Maker 托管资源不要求使用 QnAMakerRuntimeClient 对象。 请改为调用 [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) 方法。

---

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 .NET 的 QnA Maker 客户端库执行以下操作：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

* [对创作客户端进行身份验证](#authenticate-the-client-for-authoring-the-knowledge-base)
* [创建知识库](#create-a-knowledge-base)
* [更新知识库](#update-a-knowledge-base)
* [下载知识库](#download-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [获取查询运行时密钥](#get-query-runtime-key)
* [获取操作的状态](#get-status-of-an-operation)
* [对查询运行时客户端进行身份验证](#authenticate-the-runtime-for-generating-an-answer)
* [从知识库生成答案](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

* [对创作客户端进行身份验证](#authenticate-the-client-for-authoring-the-knowledge-base)
* [创建知识库](#create-a-knowledge-base)
* [更新知识库](#update-a-knowledge-base)
* [下载知识库](#download-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [获取操作的状态](#get-status-of-an-operation)
* [从知识库生成答案](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>对用于创作知识库的客户端进行身份验证

使用密钥将客户端对象实例化，并将它与资源一起使用来构建终结点，从而使用你的终结点和密钥创建一个 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient)。 创建一个 [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) 对象。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) 对象存储问答对：

* 对于 **编辑内容**，请使用 [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于 **文件**，请使用 [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

创建步骤还包括知识库的属性：
* `defaultAnswerUsedForExtraction` - 找不到任何答案时返回的内容
* `enableHierarchicalExtraction` - 在提取的 QnA 对之间自动创建提示关系
* `language` - 创建资源的第一个知识库时，设置要在 Azure 搜索索引中使用的语言。

调用 [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) 方法，然后将返回的操作 ID 传递给 [MonitorOperation](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行从来自 MonitorOperation 的响应返回知识库 ID。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

请确保包括上述代码中引用的 [`MonitorOperation`](#get-status-of-an-operation) 函数，以便成功创建知识库。

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd)、[update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) 和 [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO 对象的 [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) 传递给 [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) 方法。 使用 [MonitorOperation](#get-status-of-an-operation) 方法来确定更新是否成功。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

请确保包括上述代码中引用的 [`MonitorOperation`](#get-status-of-an-operation) 函数，以便成功更新知识库。

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) 方法，将知识库作为 [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) 列表下载。 这不等同于“设置”页面中 QnA Maker 门户的导出，因为此方法的结果不是一个文件。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。 必须执行此步骤才能查询知识库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

## <a name="get-query-runtime-key"></a>获取查询运行时密钥

发布知识库后，需要查询运行时密钥来查询运行时。 此密钥并非用于创建原始客户端对象的密钥。

使用 [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) 方法获取 [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) 类。

使用对象中返回的其中一个密钥属性来查询知识库。

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

需要运行时密钥才能查询知识库。

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>对用于生成答案的运行时进行身份验证

创建 [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) 来查询知识库，以通过主动学习生成答案或训练。

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient：
* 从知识库获取答案
* 将新建议的问题发送到知识库中进行[主动学习](../concepts/active-learning-suggestions.md)。

## <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) 方法从已发布的知识库中生成答案。 此方法接受知识库 ID 和 [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) 和 [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context)。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) 方法从已发布的知识库中生成答案。 此方法接受知识库 ID 和 [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top)、[Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) 和 [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest)。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

这是查询知识库的一个简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

结合使用 [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) 方法和知识库 ID 参数来删除知识库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation)。 使用操作中的[操作 ID](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) 进行轮询（使用重试逻辑），确定原始方法的状态。

以下代码块中的 _loop_ 和 _Task.Delay_ 用于模拟重试逻辑。 应将其替换为你自己的重试逻辑。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) 上找到此示例的源代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart) 上找到此示例的源代码。

---
