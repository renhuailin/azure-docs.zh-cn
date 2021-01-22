---
title: 快速入门：适用于 Python 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Python 的 QnA Maker 客户端库。
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 4ae6e732d94081db54c5fda3b6b05c642df4cb2d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256557"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以使用适用于 Python 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取预测运行时终结点密钥
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [包 (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/) | [Python 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以使用适用于 Python 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [包 (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。
    * 注意：请务必选择“托管”复选框。
    * 部署 QnA Maker 资源后，选择“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

---

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建名为 `quickstart-file.py` 的新 Python 文件并导入以下库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

为资源的 Azure 终结点和密钥创建变量。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT 和 QNA_MAKER_RUNTIME_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- QNA_MAKER_RUNTIME_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.azurewebsites.net` 格式。
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY 和 QNA_MAKER_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[QnA Maker](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为 [主动学习](../concepts/active-learning-suggestions.md)的一部分）。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[QnA Maker](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) 使用以下对象模型：
* [QnAMakerClient](#qnamakerclient-object-model) 是用于创建、管理、发布、下载和查询知识库的对象。

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端以后，使用[知识库](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python)属性创建、管理和发布知识库。

通过发送 JSON 对象来管理知识库。 对于即时操作，方法通常返回一个指示状态的 JSON 对象。 对于长时间运行的操作，响应是操作 ID。 使用操作 ID 调用 [operations.get_details](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#get-details-kb-id--custom-headers-none--raw-false----operation-config-) 方法，确定[请求状态](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

预测 QnA Maker 客户端是 `QnAMakerRuntimeClient` 对象，该对象使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。Microsoft.Rest.ServiceClientCredentials 包含预测运行时密钥，该密钥在发布知识库后从创作客户端调用 [client.EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) 返回。

使用 `generate_answer` 方法从查询运行时获取答案。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

QnA Maker 托管资源不要求使用 QnAMakerRuntimeClient 对象。 请改为直接在 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) 对象上调用 [generate_answer](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-)。

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>对用于创作知识库的客户端进行身份验证

使用终结点和密钥实例化某个客户端。 使用密钥创建 CognitiveServicesCredentials 对象，然后在终结点上使用该对象创建 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) 对象。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>创建知识库

使用客户端对象获取[知识库操作](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python)对象。

知识库为来自三个源的 [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) 对象存储问答对：

* 对于 **编辑内容**，请使用 [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于 **文件**，请使用 [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

调用 [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) 方法，然后将返回的操作 ID 传递给 [Operations.getDetails](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行返回 MonitorOperation 的响应中的知识库 ID。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

请确保包括上述代码中引用的 [`_monitor_operation`](#get-status-of-an-operation) 函数，以便成功创建知识库。

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python)、[update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) 和 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO 对象的 [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) 传递给 [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) 方法。 使用 [Operation.getDetail](#get-status-of-an-operation) 方法来确定更新是否成功。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

请确保包括上述代码中引用的 [`_monitor_operation`](#get-status-of-an-operation) 函数，以便成功更新知识库。

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) 方法，将知识库作为 [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>查询知识库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

### <a name="get-query-runtime-key"></a>获取查询运行时密钥

发布知识库后，需要查询运行时密钥来查询运行时。 此密钥与用于创建原始客户端对象的密钥不同。

使用 [EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) 方法以获取 [EndpointKeysDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python) 类。

使用对象中返回的其中一个密钥属性来查询知识库。

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>对用于生成答案的运行时进行身份验证

创建 [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) 来查询知识库，以通过主动学习生成答案或训练。

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 基于知识来获取答案，或者将新的建议问题发送到知识库以进行[主动学习](../concepts/active-learning-suggestions.md)。

### <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 QnAMakerRuntimeClient.runtime.generate_answer 方法从已发布的知识库中生成答案。 此方法接受知识库 ID 和 [QueryDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 Top 和 Context。

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 [generate_answer](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) 方法从已发布的知识库中生成答案。 此方法接受知识库 ID 和 [QueryDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 Top 和 Context。

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

这是一个简单的查询知识库的示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) 方法与知识库 ID 参数删除知识库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python)。 使用操作中的操作 ID 进行轮询（使用重试逻辑），确定原始方法的状态。

以下代码块中的 _setTimeout_ 调用用于模拟异步代码。 将其替换为重试逻辑。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 Python 命令运行应用程序。

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py) 上找到此示例的源代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py) 上找到此示例的源代码。

---
