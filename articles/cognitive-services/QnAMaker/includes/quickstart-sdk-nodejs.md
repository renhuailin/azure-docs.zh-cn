---
title: 快速入门：适用于 Node.js 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Node.js 的 QnA Maker 客户端库。
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: fba4354fb1aae19833790e166474008dc994cd79
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792286"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以使用适用于 Node.js 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取预测运行时终结点密钥
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[参考文档](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [包 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以使用适用于 Node.js 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[参考文档](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [包 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org)。
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和资源。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和资源名称，以便将应用程序连接到 QnA Maker API。 你稍后会在本快速入门中将密钥和资源名称粘贴到下文的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org)。
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。
    * 注意：请务必选择“托管”复选框。
    * 部署 QnA Maker 资源后，选择“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

---

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

运行 `npm init -y` 命令以使用 `package.json` 文件创建一个 node 应用程序。

```console
npm init -y
```

### <a name="install-the-client-library"></a>安装客户端库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

安装以下 NPM 包：

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

安装以下 NPM 包：

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 index.js 的文件，并导入以下库：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

为资源的 Azure 密钥和资源名称创建一个变量。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT 和 QNA_MAKER_RUNTIME_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- QNA_MAKER_RUNTIME_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.azurewebsites.net` 格式。
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY 和 QNA_MAKER_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为 [主动学习](../concepts/active-learning-suggestions.md)的一部分）。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) 使用以下对象模型：
* [QnAMakerClient](#qnamakerclient-object-model) 是用于创建、管理、发布、下载和查询知识库的对象。

---

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是 [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 对象，使用包含密钥的凭据向 Azure 进行身份验证。

创建该客户端以后，使用[知识库](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase)创建、管理和发布知识库。

通过发送 JSON 对象来管理知识库。 对于即时操作，方法通常返回一个指示状态的 JSON 对象。 对于长时间运行的操作，响应是操作 ID。 使用操作 ID 调用 [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) 方法，确定[请求状态](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

预测 QnA Maker 客户端是 QnAMakerRuntimeClient 对象，该对象使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。Microsoft.Rest.ServiceClientCredentials 包含预测运行时密钥，该密钥在发布知识库后从创作客户端调用 [client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) 返回。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

QnA Maker 托管资源不要求使用 QnAMakerRuntimeClient 对象。 请改为直接在 [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 对象上调用 [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_)。

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

使用终结点和密钥实例化某个客户端。 使用密钥创建 ServiceClientCredentials 对象，并将其与终结点一起使用以创建 [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 对象。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 对象存储问答对：

* 对于 **编辑内容**，请使用 [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于 **文件**，请使用 [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

创建步骤还包括知识库的属性：
* `defaultAnswerUsedForExtraction` - 找不到任何答案时返回的内容
* `enableHierarchicalExtraction` - 在提取的 QnA 对之间自动创建提示关系
* `language` - 创建资源的第一个知识库时，设置要在 Azure 搜索索引中使用的语言。

使用知识库信息调用 [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 方法。 知识库信息基本上是一个 JSON 对象。

当 create 方法返回时，请将返回的操作 ID 传递到 [wait_for_operation](#get-status-of-an-operation) 方法，以轮询状态。 操作完成时，wait_for_operation 方法会返回。 分析返回的操作的 `resourceLocation` 标头值，以获取新的知识库 ID。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

请确保包括上述代码中引用的 [`wait_for_operation`](#get-status-of-an-operation) 函数，以便成功创建知识库。

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 和 [delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 对象的 [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) 传递给 [update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 方法。 DTO 也基本上是 JSON 对象。 使用 [wait_for_operation](#get-status-of-an-operation) 方法来确定更新是否成功。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

请确保包括上述代码中引用的 [`wait_for_operation`](#get-status-of-an-operation) 函数，以便成功更新知识库。

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) 方法，将知识库作为 [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。 检查 HTTP 响应代码，验证发布是否成功。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>查询知识库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

### <a name="get-query-runtime-key"></a>获取查询运行时密钥

发布知识库后，需要查询运行时密钥来查询运行时。 此密钥与用于创建原始客户端对象的密钥不同。

使用 [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) 方法以获取 [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) 类。

使用对象中返回的其中一个密钥属性来查询知识库。

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>对用于生成答案的运行时进行身份验证

创建 QnAMakerRuntimeClient 来查询知识库，以通过主动学习生成答案或训练。

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 基于知识来获取答案，或者将新的建议问题发送到知识库以进行[主动学习](../concepts/active-learning-suggestions.md)。

### <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 RuntimeClient.runtime.generateAnswer 方法从已发布的知识库生成答案。 此方法接受知识库 ID 和 [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 Top 和 Context。

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 QnAMakerClient.knowledgebase.generateAnswer 方法从已发布的知识库中生成答案。 此方法接受知识库 ID 和 [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 Top 和 Context。

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

这是查询知识库的简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 方法与知识库 ID 参数删除知识库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)。 使用操作中的[操作 ID](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) 进行轮询（使用重试逻辑），确定原始方法的状态。

以下代码块中的 _delayTimer_ 调用用于模拟重试逻辑。 将此项替换为你自己的重试逻辑。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node index.js` 命令运行应用程序。

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) 上找到此示例的源代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js) 上找到此示例的源代码。

---