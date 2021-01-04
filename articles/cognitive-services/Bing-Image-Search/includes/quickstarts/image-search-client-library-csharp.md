---
title: 必应图像搜索 C# 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612635"
---
在本快速入门中，你将使用必应图像搜索客户端库进行第一次图像搜索。 

客户端搜索库是 REST API 的包装器，并且包含相同的功能。 

你将创建一个 C# 应用程序，发送图像搜索查询、分析 JSON 响应，并显示所返回的第一个图像的 URL。

[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件

* 如果你正在使用 Windows，请使用任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/vs/whatsnew/)
* 如果你使用的是 macOS 或 Linux，请使用[已安装 .NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install) 的 [VS Code](https://code.visualstudio.com)
* [免费的 Azure 订阅](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-a-console-project"></a>创建控制台项目

首先，创建新的 C# 控制台应用程序。

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. 在 Visual Studio 中创建一个名为 BingImageSearch 的新控制台解决方案。
    
1. 添加[认知图像搜索 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. 在“解决方案资源管理器”中右键单击项目。
    1. 选择“管理 NuGet 包”。
    1. 搜索并选择“Microsoft.Azure.CognitiveServices.Search.ImageSearch”，然后安装该包。
    
# <a name="vs-code"></a>[VS Code](#tab/vscode)

1. 在 VS Code 中打开终端窗口。
1. 通过在终端窗口中输入以下代码，创建一个名为 BingImageSearch 的新控制台项目：
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. 在 VS Code 中打开 BingImageSearch 文件夹。
1. 通过在终端窗口中输入以下代码，添加 [认知图像搜索 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) NuGetPackage：

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>初始化应用程序


1. 用以下代码替换 Program.cs 中的所有 `using` 语句：

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. 在项目的 `Main` 方法中，创建你的有效订阅密钥的变量、必应要返回的图像结果和搜索词。 然后，使用密钥实例化图像搜索客户端。

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>使用客户端发送搜索查询
    
仍然在 `Main` 方法中，使用客户端和查询文本进行搜索：
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>分析并查看第一个图像结果

分析响应中所返回的图像结果。 

如果响应包含搜索结果，请存储第一个结果并打印其部分详细信息。

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](../../overview.md)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure 认知服务 SDK 的 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure 认知服务文档](../../../index.yml)
* [必应图像搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)