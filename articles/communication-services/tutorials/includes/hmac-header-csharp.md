---
title: 使用 C# 对 HTTP 请求进行签名
description: 本教程介绍使用 Azure 通信服务的 HMAC 签名对 HTTP 请求进行签名的 C# 版本。
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 06/30/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 19a031c58219f7369a2969599b13337de238e4c6
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107392"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

- 创建活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Visual Studio](https://visualstudio.microsoft.com/downloads/)。
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信服务资源](../../quickstarts/create-communication-resource.md)。 对于本教程，你需要记录 resourceEndpoint 和 resourceAccessKey 。

## <a name="sign-an-http-request-with-c"></a>使用 C# 对 HTTP 请求进行签名

访问密钥身份验证会使用共享密钥为每个 HTTP 请求生成一个 HMAC 签名。 此签名是使用 SHA256 算法生成的，通过 `HMAC-SHA256` 方案在 `Authorization` 标头中发送。 例如：

```
Authorization: "HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature` 包括：

- HTTP 谓词（例如 `GET` 或 `PUT`）
- HTTP 请求路径
- x-ms-date
- 主机
- x-ms-content-sha256

## <a name="setup"></a>设置

以下步骤介绍如何构造授权标头。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `SignHmacTutorial` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o SignHmacTutorial
```

将目录更改为新创建的应用文件夹。 使用 `dotnet build` 命令来编译应用程序。

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>安装包

安装用于正文序列化的 `Newtonsoft.Json` 包。

```console
dotnet add package Newtonsoft.Json
```

更新 `Main` 方法声明以支持异步代码。 使用以下代码开始操作。

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>创建请求消息

在此示例中，我们将使用通信服务身份验证 API（版本 `2021-03-07`）对请求进行签名，以创建新的标识。

将以下代码添加到 `Main` 方法中。

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

将 `resourceEndpoint` 替换为实际的资源终结点值。

## <a name="create-a-content-hash"></a>创建内容哈希

内容哈希是 HMAC 签名的一部分。 使用以下代码来计算内容哈希。 可将此方法添加到 `Main` 方法下的 `Progam.cs`。

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>计算签名

使用以下代码来创建方法，以便计算 HMAC 签名。

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

将 `resourceAccessKey` 替换为实际的通信服务资源的访问密钥。

## <a name="create-an-authorization-header-string"></a>创建授权标头字符串

现在，我们将构造要添加到授权标头的字符串。

1. 计算内容哈希。
1. 指定协调世界时 (UTC) 时间戳。
1. 准备要签名的字符串。
1. 计算该签名。
1. 连接将要在授权标头中使用的该字符串。
 
将以下代码添加到 `Main` 方法中。

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>向 requestMessage 添加标头

使用以下代码将所需标头添加到 `requestMessage`。

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("x-ms-date", date);
//Add an authorization header.
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>测试客户端

通过使用 `HttpClient` 来调用终结点，并检查响应。

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
