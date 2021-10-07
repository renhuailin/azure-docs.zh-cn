---
title: 快速入门：使用 REST API 和 C# 获取图像见解 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视觉搜索 API 和 C# 上传图像，并获取有关该图像的见解。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e2f2b1d21e36f059b3848e6f9a1a9d6ed02c05ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669448"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>快速入门：使用必应视觉搜索 REST API 和 C# 获取图像见解

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本快速入门演示如何将图像上传到必应视觉搜索 API 以及如何查看该 API 返回的见解。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* [Json.NET 框架](https://www.newtonsoft.com/json)，以 NuGet 程序包的形式提供。
* 如果使用的是 Linux/MacOS，则可以使用 [Mono](https://www.mono-project.com/) 运行此应用程序。

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在 Visual Studio 中，创建名为 BingSearchApisQuickStart 的新控制台解决方案。 将以下命名空间添加到主代码文件：

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. 为订阅密钥、终结点以及要上传的图像的路径添加变量。 对于 `uriBase` 值，可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. 创建名为 `GetImageFileName()` 的方法以获取图像的路径。
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. 创建一个方法用于获取图像的二进制数据。

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>生成窗体数据

1. 若要上传本地图像，请先生成要发送到 API 的窗体数据。 窗体数据包括 `Content-Disposition` 标头，`name` 参数设置为“图像”且 `filename` 参数设置为图像的文件名。 窗体内容包含图像的二进制数据。 可以上传的最大图像大小为 1 MB。

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. 添加边界字符串以设置 POST 窗体数据的格式。 边界字符串确定数据的开头、结尾和换行字符。

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. 使用以下变量将参数添加到窗体数据：

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. 创建名为 `BuildFormDataStart()` 的函数，以使用边界字符串和图像路径创建窗体数据的开头部分。
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. 创建名为 `BuildFormDataEnd()` 的函数，以使用边界字符串创建窗体数据的结尾部分。
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>调用必应视觉搜索 API

1. 创建一个函数用于调用必应视觉搜索终结点并返回 JSON 响应。 该函数采用窗体数据的开头和结尾部分、包含图像数据的字节数组，以及一个 `contentType` 值。

2. 使用 `WebRequest` 存储 URI、contentType 值和标头。  

3. 使用 `request.GetRequestStream()` 写入窗体和图像数据，然后获取响应。 函数应类似于以下代码：
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>创建 Main 方法

1. 在应用程序的 `Main()` 方法中，获取图像的文件名和二进制数据。

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. 通过设置 POST 正文的边界格式来对其进行设置。 然后调用 `BuildFormDataStart()` 和 `BuildFormDataEnd()` 来创建窗体数据。

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. 通过设置 `CONTENT_TYPE_HEADER_PARAMS` 和窗体数据边界的格式来创建 `ContentType` 值。

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. 通过调用 `BingImageSearch()` 来获取 API 响应，然后输出响应。

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>使用 HttpClient

如果使用 `HttpClient`，则可以使用 `MultipartFormDataContent` 类来生成窗体数据。 使用以下代码节来替换上一示例中的相应方法：

1. 将 `Main()`方法替换为以下代码：

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. 将 `BingImageSearch()`方法替换为以下代码：

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建视觉搜索单页 Web 应用](../tutorial-bing-visual-search-single-page-app.md)