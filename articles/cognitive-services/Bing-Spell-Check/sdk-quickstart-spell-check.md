---
title: 快速入门：使用用于 C# 的必应拼写检查 SDK 进行拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f39dbd22372342786eacbccd31a10fc927c0892
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352620"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>快速入门：使用用于 C# 的必应拼写检查 SDK 进行拼写检查

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明开始使用用于 C# 的必应拼写检查 SDK 进行拼写检查。 虽然必应拼写检查具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck) 上找到此示例的源代码。

## <a name="application-dependencies"></a>应用程序依赖项

* 任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)。
* 必应拼写检查 [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

若要向项目中添加必应拼写检查 SDK，请在 Visual Studio 中的 **解决方案资源管理器** 中选择“管理 NuGet 包”选项。 添加 `Microsoft.Azure.CognitiveServices.Language.SpellCheck` 程序包。 此程序包还会安装以下依赖项：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中创建一个新的 C# 控制台解决方案。 然后添加以下 `using` 语句。
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. 创建新类。 然后创建一个名为 `SpellCheckCorrection()` 的异步函数，获取订阅密钥并发送拼写检查请求。

3. 通过创建新 `ApiKeyServiceClientCredentials` 对象来对客户端进行实例化。 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>发送请求并读取响应

1. 在上面创建的函数中，执行以下步骤。 向客户端发送拼写检查请求。 将要检查的文本添加到 `text` 参数中，并将模式设置为 `proof`。  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. 获取第一个拼写检查结果（如果有）。 打印返回的第一个拼写错误的字词（令牌）、令牌类型和建议数字。

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. 获取第一个建议的更正（如果有）。 输出建议分数和建议字词。 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>运行应用程序

生成并运行项目。 如果使用的是 Visual Studio，请按 **F5** 调试该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorials/spellcheck.md)

- [什么是必应拼写检查 API？](overview.md)
- [必应拼写检查 C# SDK 参考指南](/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)