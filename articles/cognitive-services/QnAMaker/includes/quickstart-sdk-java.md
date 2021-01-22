---
title: 快速入门：适用于 Java 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Java 的 QnA Maker 客户端库。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 783cc6dbfbc916383d71ffa17b9d789e227303a0
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256523"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以使用适用于 Java 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取预测运行时终结点密钥
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [包](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以使用适用于 Java 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 等待长时间运行的任务
* 下载知识库
* 从知识库获取答案
* 删除知识库

[库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [包](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。
    * 注意：请务必选择“托管”复选框。
    * 部署 QnA Maker 资源后，选择“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

---

## <a name="setting-up"></a>设置

### <a name="install-the-client-libraries"></a>安装客户端库

安装 Java 后，可以使用 [MVN 存储库](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker)中的 [Maven](https://maven.apache.org/) 来安装客户端库。

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

创建一个名为 `quickstart.java` 的新文件，并导入以下库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

为资源的 Azure 终结点和密钥创建变量。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY、QNA_MAKER_ENDPOINT 和 QNA_MAKER_RUNTIME_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- QNA_MAKER_RUNTIME_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.azurewebsites.net` 格式。
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。

- 创建名为 QNA_MAKER_SUBSCRIPTION_KEY 和 QNA_MAKER_ENDPOINT 的环境变量来存储这些值。
- QNA_MAKER_ENDPOINT 的值采用 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 格式。 
- 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](../../../key-vault/general/overview.md)可提供安全的密钥存储。

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

QnA Maker 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为 [主动学习](../concepts/active-learning-suggestions.md)的一部分）。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

QnA Maker 使用以下对象模型：
* [QnAMakerClient](#qnamakerclient-object-model) 是用于创建、管理、发布、下载和查询知识库的对象。

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是一个 [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) 对象，使用包含密钥的 MsRest::ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端后，可使用客户端的 [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) 属性方法创建、管理和发布知识库。

对于即时操作，方法通常返回结果（如果有）。 对于长时间运行的操作，响应是 [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) 对象。 使用 `operation.operationId` 值调用 [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) 方法，确定[请求状态](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

运行时 QnA Maker 客户端是一个 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) 对象。

使用创作客户端发布知识库后，可使用运行时客户端的 [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) 方法从知识库获取答案。

通过调用 [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) 并传递运行时终结点密钥来创建运行时客户端。 要获取运行时终结点密钥，请使用创作客户端调用 [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30)。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

QnA Maker 托管资源不要求使用 QnAMakerRuntimeClient 对象。 请改为直接在 [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) 对象上调用 [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308)。

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>对用于创作知识库的客户端进行身份验证

使用创作终结点和订阅密钥实例化客户端。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) 对象存储问答对：

* 对于 **编辑内容**，请使用 [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于 **文件**，请使用 [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

调用 [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) 方法，然后将返回的操作的 `operationId` 属性传递给 [getDetails](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行返回知识库 ID。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是调用 [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) 并传入知识库 ID 和 [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java) 对象。 而该对象可以包含：
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

将返回的操作的 `operationId` 属性传递给 [getDetails](#get-status-of-an-operation) 方法以轮询状态。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) 方法，将知识库作为 [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

发布知识库后，需要运行时终结点密钥来查询知识库。 这与用于创建创作客户端的订阅密钥不同。

使用 [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) 方法获取 [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java) 对象。

通过调用 [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) 并从 EndpointKeysDTO 对象传递运行时终结点密钥来创建运行时客户端。

使用 [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) 方法从已发布的知识库生成答案。 此方法接受知识库 ID 和 [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) 对象。

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

使用 [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) 方法从已发布的知识库生成答案。 此方法接受知识库 ID 和 [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) 对象。

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

这是查询知识库的简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) 方法与知识库 ID 参数删除知识库。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java)。 使用操作中的操作 ID 进行轮询（使用重试逻辑），确定原始方法的状态。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>运行此应用程序

下面是应用程序的 main 方法。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

按如下所示运行应用程序。 这会假设类名为 `Quickstart`，并且依赖项位于当前文件夹下名为 `lib` 的子文件夹中。

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/version-1)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java) 上找到此示例的源代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/version-2)

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java) 上找到此示例的源代码。

---
