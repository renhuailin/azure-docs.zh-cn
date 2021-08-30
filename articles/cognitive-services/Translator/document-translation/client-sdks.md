---
title: 文档翻译 C#/.NET 或 Python 客户端库
titleSuffix: Azure Cognitive Services
description: 使用适用于基于云的批量文档翻译服务和过程的翻译器 C#/.NET 或 Python 客户端库 (SDK)
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724504"
---
# <a name="document-translation-client-library-sdks"></a>文档翻译客户端库 SDK
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
[文档翻译](overview.md)是 [Azure 翻译器](../translator-overview.md)服务的一个基于云的功能。 可以采用各种文件格式翻译整个文档或处理批量文档翻译，同时保留原始文档结构和格式。 本文将介绍如何使用文档翻译服务 C#/.NET 和 Python 客户端库。 对于 REST API，请参阅我们的[快速入门](get-started-with-document-translation.md)指南。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。

* [单服务的 Translator 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)（并非多服务的认知服务资源） 。

* 一个 [**Azure Blob 存储帐户**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你将在自己的 Azure Blob 存储帐户中为源和目标文件[创建容器](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)：

  * **源容器**。 将在此容器中上传要翻译的文件（必需）。
  * **目标容器**。 将在此容器中存储已翻译的文件（必需）。

* 还需要为源容器和目标容器创建共享访问签名 (SAS) 令牌。 `sourceUrl` 和 `targetUrl` 必须包含作为查询字符串追加的共享访问签名 (SAS) 令牌。 可将该令牌分配到容器或特定的 Blob。 参阅 [**为文档翻译处理创建 SAS 令牌**](create-sas-tokens.md)。

  * **源** 容器或 Blob 必须已指定 **读取** 和 **列出** 访问权限。
  * **目标** 容器或 Blob 必须已指定 **写入** 和 **列出** 访问权限。

有关详细信息，请参阅[创建 SAS 令牌](create-sas-tokens.md)。

## <a name="client-libraries"></a>客户端库

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [包 (NuGet)][documenttranslation_nuget_package] | [客户端库][documenttranslation_client_library_docs] |  [REST API][documenttranslation_rest_api] | [产品文档][documenttranslation_docs] | [示例][documenttranslation_samples] |

> [!IMPORTANT]
> 这是文档翻译 SDK 的预发行版。 这是一个简介性的版本，供客户提前访问并提供反馈。 预发行版仍在开发中，随时可能会更改，某些功能可能不受支持或者受限制 - 请勿在生产应用程序中使用。

### <a name="set-up-your-project"></a>设置项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `batch-document-translation` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。

```console
dotnet new console -n batch-document-translation
```

将目录更改为新创建的应用文件夹。 使用以下命令生成应用程序：

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

在应用程序目录中，使用以下方法之一安装适用于 .NET 的文档翻译客户端库：

#### <a name="net-cli"></a>**.NET CLI**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**NuGet 程序包管理器**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>NuGet PackageReference

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

在首选的编辑器或 IDE 中，从项目目录打开 Program.cs 文件。 添加以下 using 指令：

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

在应用程序的 Program 类中，为订阅密钥和自定义终结点创建变量。 有关详细信息，请参阅[自定义域名和订阅密钥](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>翻译文档或批处理文件

* 若要针对单个 Blob 容器中的一个或多个文档启动翻译操作，请调用 `StartTranslationAsync` 方法。

* 若要调用 `StartTranslationAsync`，需要初始化包含以下参数的 `DocumentTranslationInput` 对象：

* sourceUri。 包含要翻译的文档的源容器的 SAS URI。
* targetUri。翻译后的文档要写入到的目标容器的 SAS URI。
* targetLanguageCode。 翻译后的文档的语言代码。 可以在我们的[语言支持](../language-support.md)页上找到语言代码。

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

就这么简单！ 现已创建一个可以使用 .NET 客户端库翻译 Blob 容器中的文档的程序。

### <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
 > [浏览更多 C# 代码示例](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [包 (PyPI)][python-dt-pypi] |  [客户端库][python-dt-client-library] |  [REST API][python-rest-api] | [产品文档][python-dt-product-docs] | [示例][python-dt-samples] |

> [!IMPORTANT]
> 这是文档翻译 SDK 的预发行版。 这是一个简介性的版本，供客户提前访问并提供反馈。 预发行版仍在开发中，随时可能会更改，某些功能可能不受支持或者受限制 - 请勿在生产应用程序中使用。

### <a name="set-up-your-project"></a>设置项目

### <a name="install-the-client-library"></a>安装客户端库

安装 [Python](https://www.python.org/downloads/)（如果尚未安装），然后安装最新版本的翻译器客户端库：

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>创建应用程序

在首选编辑器或 IDE 中创建新的 Python 应用程序。 然后导入以下库。

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

为资源订阅密钥、自定义终结点、sourceUrl 和 targetUrl 创建变量。 有关详细信息，请参阅[自定义域名和订阅密钥](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>翻译文档或批处理文件

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

就这么简单！ 现已创建一个可以使用 Python 客户端库翻译 Blob 容器中的文档的程序。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览更多 Python 代码示例](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---