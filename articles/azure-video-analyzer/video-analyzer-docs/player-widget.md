---
title: 使用 Azure 视频分析器播放器小组件
description: 本参考文章介绍了如何将视频分析器播放器小组件添加到应用程序。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 05/11/2021
ms.openlocfilehash: f4c2d3f7d13002d0de231859bc31a74b72b1c7fd
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555121"
---
# <a name="using-the-azure-video-analyzer-player-widget"></a>使用 Azure 视频分析器播放器小组件

本教程介绍如何在应用程序中使用 Azure 视频分析器播放器小组件。  此代码是一个易于嵌入的小组件，可使最终用户能够播放视频并浏览分段视频文件的各个部分。  为此，你需要生成一个静态 HTML 页面（其中嵌入了小组件）以及支持其运行的所有部分。

在本教程中，将：

> [!div class="checklist"]
> * 创建令牌
> * 列出视频
> * 获取播放[视频应用程序资源](./terminology.md#video)的基 URL
> * 创建包含播放器的页面
> * 将流式处理终结点和令牌传递给播放器

## <a name="suggested-pre-reading"></a>建议的读前准备

- [Web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：
* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
* [Visual Studio Code](https://code.visualstudio.com/) 或适用于 HTML 文件的其他编辑器。
一种。
* [连续视频录制和播放](./use-continuous-video-recording.md)或[在边缘设备上检测移动并录制视频](./detect-motion-record-video-clips-cloud.md)

## <a name="create-a-token"></a>创建令牌

在本部分中，我们将创建稍后要在文档中使用的 JWT 令牌。  我们将使用一个会生成 JWT 令牌的示例应用程序，它会提供创建访问策略所需的所有字段。

> [!NOTE] 
> 如果熟悉如何基于 RSA 或 ECC 证书生成 JWT 令牌，可跳过此部分。

1. 下载位于[此处](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/jwt-token-issuer/)的 JWTTokenIssuer 应用程序。

   > [!NOTE] 
   > 有关配置受众值的详细信息，请参阅此[文章](./access-policies.md)

2. 启动 Visual Studio Code 并打开包含 *.sln 文件的文件夹。
3. 在“资源管理器”窗格中，导航到程序 program.cs 文件
4. 修改第 77 行 - 将受众更改为视频分析器终结点，再加上 /videos/*，如下所示：

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```
5. 修改第 78 行 - 将颁发者更改为证书的颁发者值。  示例：https://contoso.com

   > [!NOTE] 
   > 可以在 Azure 中的视频分析器资源的概述部分找到视频分析器终结点。 需要单击链接“JSON 视图” 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/endpoint.png" alt-text="播放器小组件 - 终结点":::
6. 保存该文件。
7. 按 `F5` 运行 JWTTokenIssuer 应用程序。

这将生成并执行应用程序。  生成后，它将通过 openssl 创建证书来运行。  还可运行位于 debug 文件夹中的 JWTTokenIssuer.exe 文件。  运行应用程序的好处是可以指定输入选项，如下所示：

- JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]

JWTTokenIssuer 将创建 JWT 令牌和以下所需组件：

- `kty`; `alg`; `kid`; `n`; `e`

确保复制这些值供以后使用。

## <a name="create-an-access-policy"></a>创建访问策略

访问策略定义访问给定视频分析器视频流的权限和持续时间。  在本教程中，我们将在 Azure 门户中为视频分析器配置访问策略。  

1. 登录到 Azure 门户，然后导航到视频分析器帐户所在的资源组。
1. 选择视频分析器资源。
1. 在“视频分析器”下，选择“访问策略”

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="播放器小组件 - 门户访问策略":::    
1. 单击“新建”并输入以下内容：

   > [!NOTE] 
   > 这些值来自在上一步创建的 JWTTokenIssuer 应用程序。

   - 访问策略名称 - 任何名称

   - 颁发者 - 必须与 JWT 令牌颁发者匹配 

   - 受众 - JWT 令牌的受众 - 默认值为 ${System.Runtime.BaseResourceUrlPattern}。  若要详细了解受众和 ${System.Runtime.BaseResourceUrlPattern}，请参阅此[文章](./access-policies.md)

   - 密钥类型 - kty - RSA 

   - 算法 - 支持的值为 RS256、RS384、RS512

   - 密钥 ID - kid - 在证书中生成

   - N 值 - 对于 RSA，N 值为模数

   - E 值 - 对于 RSA，E 值为公共指数

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="播放器小组件 - 访问策略门户":::     
1. 单击 `save`。

## <a name="list-video-analyzer-video-resources"></a>列出视频分析器视频资源

接下来，需要生成视频资源的列表。  通过对上面使用的帐户终结点进行 REST 调用来完成，使用生成的令牌进行身份验证。

可以通过多种方式将 GET 请求发送到 REST API，但现在我们将使用 JavaScript 函数。  下面的代码使用 [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) 与在页面上的 `clientApiEndpointUrl` 和 `token` 字段中存储的值来发送同步 `GET` 请求。  然后，它使用视频的生成列表，并将其存储在页面上设置的 `videoList` 文本区域中。

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```

## <a name="add-the-video-analyzer-player-component"></a>添加视频分析器播放器小组件

现在我们已有客户端 API 终结点 URL、令牌和视频名称，接下来可将播放器添加到页面。

1. 通过直接向页面添加包，使其包括播放器模块本身。  可以在应用程序中包括 NPM 包方向，也可以在运行时将其动态嵌入，如下所示：
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. 将 AVA-Player 元素添加到文档中：
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. 获取指向页面中视频分析器播放器小组件的链接：
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. 若要使用已有的值配置播放器，需要将它们设置为对象，如下所示：
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. 将视频加载到播放器中以开始
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>将其放在一起

如果将以上 Web 元素组合在一起，将获得以下静态 HTML 页面，从而我们能够使用帐户终结点和令牌来观看视频。

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
</body>
</html>
```

## <a name="host-the-page"></a>托管页面

可在本地测试此页，但建议测试托管版本。  如果无法快速托管页面，下面提供了有关如何将[静态网站](../../storage/blobs/storage-blob-static-website.md)与存储一起使用来执行此操作的说明。  下面是[这些更完整的说明](../../storage/blobs/storage-blob-static-website-how-to.md)的精华版，这些说明针对在本教程中使用的文件进行了更新。

1. 创建存储帐户
1. 在左侧的 `Data management` 下面，单击 `Static website`
1. `Enable` 存储帐户上的静态网站
1. 对于 `Index document name`，放入 `index.html`
1. 对于 `Error document path`，放入 `404.html`
1. 选择顶部的 `Save`
1. 注意显示的 `Primary endpoint` - 这将是你的网站
1. 单击 `Primary endpoint` 上方的 `$web`
1. 使用顶部的 `Upload` 按钮，将静态 HTML 页面作为 `index.html` 上传

## <a name="play-a-video"></a>播放视频

托管页面后导航到该页面，应能够完成这些步骤。

1. 放入 `Client API endpoint URL` 和 `Token`
1. 按 `Get videos`
1. 在视频列表中选择视频名称，并将其填写到 `Video name` 输入字段
1. 按 `Play video`

## <a name="additional-details"></a>其他详细信息

### <a name="refreshing-the-access-token"></a>刷新访问令牌

播放器使用之前生成的访问令牌获取播放授权令牌。  令牌会定期到期，因此需要刷新。  生成新的访问令牌后，可通过两种方式来刷新播放器的访问令牌。

* 主动调用小组件方法 `setAccessToken`
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* 通过侦听 `TOKEN_EXPIRED` 事件对此事件进行操作
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` 事件将在令牌过期前 5 秒发生。  如果要设置事件侦听器，建议在播放器小组件上调用 `load` 函数之前执行此操作。

### <a name="configuration-details"></a>配置详细信息

已为上面的播放器进行了简单配置，但它支持范围更广的配置值选项。  下面是支持的字段：

| 名称   | 类型             | 说明                         |
| ------ | ---------------- | ----------------------------------- |
| 令牌  | 字符串 | 小组件的 JWT 令牌 |
| videoName | 字符串 | 视频资源的名称  |
| clientApiEndpointUrl | 字符串 | 客户端 API 的终结点 URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>将代码加载到应用程序中的替代方法

用于在应用程序中获取代码的包是[此处](https://www.npmjs.com/package/video-analyzer-widgets)的 NPM 包。  虽然在上面的示例中，最新版本是在运行时直接从存储库加载，但也可使用以下内容在本地下载和安装包：

```bash
npm install @azure/video-analyzer/widgets
```

也可在应用程序代码中使用适用于 Typescript 的以下语句导入它：

```typescript
import { Player } from '@video-analyzer/widgets';
```

或者，如果要动态创建播放器小组件，请使用适用于 Javascript 的以下语句：
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

如果使用此方法导入，在导入完成后，需要以编程方式创建播放器对象。  在上面的示例中，使用 `ava-player` HTML 标记将模块添加到了页面。  若要通过代码创建播放器对象，可在 JavaScript 中执行以下操作：

```javascript
const avaPlayer = new ava.widgets.player();
```

或在 Typescript 中执行以下操作：

```typescript
const avaPlayer = new Player();
```

然后必须将其添加到 HTML：

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>后续步骤

* 详细了解[小组件 API](https://github.com/Azure/video-analyzer/widgets)
