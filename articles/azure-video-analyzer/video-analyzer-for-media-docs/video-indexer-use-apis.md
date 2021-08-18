---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）API
titleSuffix: Azure Video Analyzer for Media
description: 本文介绍如何开始使用 Azure 视频分析器媒体版（以前称为视频索引器）API。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a5f7b2bcc861ae92016c1eece0db19026bc233c9
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118818"
---
# <a name="tutorial-use-the-video-analyzer-for-media-api"></a>教程：使用视频分析器媒体版 API

Azure 视频分析器媒体版（以前称为视频索引器）在一个集成式服务中整合了 Microsoft 提供的多种音频和视频人工智能 (AI) 技术，使开发变得更简单。 API 可让开发人员专注于使用媒体 AI 技术，而无需考虑云平台的规模、全球覆盖度、可用性和可靠性的问题。 使用 API 可以上传文件、获取详细的视频见解和获取可嵌入见解和播放器小组件的 URL 等等。

创建视频分析器媒体版帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频分析器媒体版为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以[创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频分析器媒体版帐户](connect-to-azure.md)。 你需要为索引时间付费，有关详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。

本文介绍开发人员可以如何利用[视频分析器媒体版 API](https://api-portal.videoindexer.ai/)。

## <a name="subscribe-to-the-api"></a>订阅 API

1. 登录到[视频分析器媒体版开发人员门户](https://api-portal.videoindexer.ai/)。

    查看有关[登录信息](release-notes.md#october-2020)的发行说明。
    
     ![登录到视频分析器媒体版开发人员门户](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * 必须使用注册视频分析器媒体版时所用的同一提供程序。
   > * 个人 Google 和 Microsoft (Outlook/Live) 帐户只能用作试用帐户。 连接到 Azure 的帐户需要 Azure AD。
   > * 只能为每个电子邮件地址提供一个活动帐户。 如果用户尝试使用 user@gmail.com 登录到 LinkedIn，然后又使用 user@gmail.com 登录到 Google，则后一次登录时会显示错误页，指出该用户已存在。

2. 订阅。

   选择[产品](https://api-portal.videoindexer.ai/products)选项卡。然后选择“授权”并订阅。
    
   ![视频索引器开发人员门户中的“产品”选项卡](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > 新用户已自动订阅“授权”。
    
   订阅后，可以在“产品” -> “授权”下找到订阅。 在订阅页中，可以找到主密钥和辅助密钥。 密钥应受保护。 密钥应仅供服务器代码使用。 在客户端（js、.html 等）应该不可使用密钥。

   ![视频索引器开发人员门户中的订阅和密钥](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> 视频分析器媒体版用户可以使用单个订阅密钥连接到多个视频分析器媒体版帐户。 然后，可以将这些视频分析器媒体版帐户链接到不同的媒体服务帐户。

## <a name="obtain-access-token-using-the-authorization-api"></a>使用授权 API 获取访问令牌

订阅授权 API 后，可以获取访问令牌。 这些访问令牌用于对操作 API 进行身份验证。

对操作 API 的每个调用应该与匹配调用授权范围的访问令牌相关联。

- 用户级别：使用用户级别访问令牌可以在“用户”级别执行操作。 例如，获取关联的帐户。
- 帐户级别：使用帐户级别访问令牌可以在“帐户”级别或“视频”级别执行操作 。 例如，上传视频、列出所有视频、获取视频见解，等等。
- 视频级别：使用视频级访问令牌可对特定的视频执行操作。 例如，获取视频见解、下载隐藏式字幕、获取小组件，等等。

可通过两种方式控制令牌的权限级别：

* 对于“帐户”令牌，可以使用“获取具有权限的帐户访问令牌”API，并指定权限类型（读取者/参与者/MyAccessManager/所有者）     。
* 对于所有类型的令牌（包括“帐户”令牌），可以指定 allowEdit=true/false 。 false 等效于“读取者”权限（只读），true 等效于“参与者”权限（读写）   。

对于大多数的服务器到服务器方案，也许可以使用相同的“帐户”令牌，因为它同时涵盖了帐户操作和视频操作  。 但是，若要对视频分析器媒体版发出客户端调用（例如，从 JavaScript），你将需要使用“视频”访问令牌，以防止客户端访问整个帐户。 这也是在客户端中嵌入视频分析器媒体版客户端代码（例如，使用“获取见解小组件”或“获取播放器小组件”）时，必须提供“视频”访问令牌的原因  。

为简化操作，可以使用 **授权** API > **GetAccounts** 来获取帐户，而不是先获取用户令牌。 还可以请求获取具有有效令牌的帐户，以便跳过附加的调用即可获取帐户令牌。

访问令牌在 1 小时后过期。 在使用操作 API 之前，请确保访问令牌有效。 如果令牌已过期，请再次调用授权 API 来获取新访问令牌。

现在，便可以开始与 API 集成。 查找[每个视频分析器媒体版 REST API 的详细说明](https://api-portal.videoindexer.ai/)。

## <a name="account-id"></a>帐户 ID

在所有操作 API 调用中都必须指定“帐户 ID”参数。 帐户 ID 是一个 GUID，可通过以下方式之一获取：

* 使用视频分析器媒体版网站获取帐户 ID：

    1. 浏览到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
    2. 浏览到“设置”页。
    3. 复制帐户 ID。

        ![视频分析器媒体版设置和帐户 ID](./media/video-indexer-use-apis/account-id.png)

* 使用视频分析器媒体版开发人员门户以编程方式获取帐户 ID。

    使用[获取帐户](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) API。

    > [!TIP]
    > 可以通过定义 `generateAccessTokens=true` 来生成帐户的访问令牌。

* 从帐户中的播放器页 URL 获取帐户 ID。

    观看视频时，该 ID 显示在 `accounts` 节之后、`videos` 节之前。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>建议

本部分列出了有关使用视频分析器媒体版 API 的一些建议。

- 若要上传视频，建议将文件放在某个公用网络位置（例如 Azure Blob 存储帐户）。 获取视频的链接，并提供 URL 作为上传文件参数。

    提供给视频分析器媒体版的 URL 必须指向某个媒体（音频或视频）文件。 验证 URL（或 SAS URL）的一种简单方式是将其粘贴到浏览器中，如果文件开始播放/下载，则它可能是正常的 URL。 如果浏览器中显示某种可视化效果，则该 URL 可能不是文件的链接，而是 HTML 页的链接。

- 调用获取指定视频的视频见解的 API 时，会获取详细的 JSON 输出（响应内容）。 [在此主题中查看有关返回的 JSON 的详细信息](video-indexer-output-json-v2.md)。

## <a name="code-sample"></a>代码示例

以下 C# 代码片段演示了如何将所有的视频分析器媒体版 API 结合使用。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);
```

## <a name="clean-up-resources"></a>清理资源

完成本教程后，请删除你不打算使用的资源。

## <a name="see-also"></a>另请参阅

- [视频分析器媒体版概述](video-indexer-overview.md)
- [区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>后续步骤

- [检查输出 JSON 的详细信息](video-indexer-output-json-v2.md)
- 查看[示例代码](https://github.com/Azure-Samples/media-services-video-indexer)，其中展示了上传视频和为视频编制索引的重要方面。 沿用这些代码可以让你更好地了解如何将 API 用于基本功能。 请务必阅读内联注释并注意我们的最佳做法建议。

