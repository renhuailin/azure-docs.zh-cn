---
title: 使用 Azure 视频分析器播放器小组件
description: 本参考文章介绍了如何将视频分析器播放器小组件添加到应用程序。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: b70bfc9a10e357c6f1e64c1737fdb4c049b505f5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037438"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>使用 Azure 视频分析器播放器小组件

本教程介绍如何在应用程序中使用播放器小组件。 此代码是一个易于嵌入的小组件，使用户能够播放视频并浏览分段视频文件的各个部分。 为此，需要生成一个嵌入该小组件的静态 HTML 页面以及支持其运行的所有部分。

在本教程中，将：

> [!div class="checklist"]
> * 创建令牌
> * 列出视频
> * 获取播放[视频应用程序资源](./terminology.md#video)的基 URL
> * 创建包含播放器的页面
> * 将流式处理终结点和令牌传递给播放器

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Visual Studio Code](https://code.visualstudio.com/) 或适用于 HTML 文件的其他编辑器。
* [连续视频录制和播放](./use-continuous-video-recording.md)或[在边缘设备上检测移动并录制视频](./detect-motion-record-video-clips-cloud.md)。

另外，建议熟悉以下资源：

- [Web 组件](https://developer.mozilla.org/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="create-a-token"></a>创建令牌

在本节中，你将创建一个 JSON Web 令牌 (JWT)，稍后将在本文中使用该令牌。 你将使用一个会生成 JWT 令牌的示例应用程序，它会提供创建访问策略所需的所有字段。

> [!NOTE] 
> 如果你知道如何基于 RSA 或 ECC 证书生成 JWT 令牌，可跳过此部分。

1. 克隆 [AVA C# 示例存储库](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)。 然后，前往 src/jwt-token-issuer 文件夹，并找到 JWTTokenIssuer 应用程序。

    > [!NOTE] 
    > 有关配置受众值的详细信息，请参阅[访问策略](./access-policies.md)。

2. 打开 Visual Studio Code，然后转到你下载了 JWTTokenIssuer 应用程序的文件夹。 此文件夹应包含 \*.csproj 文件。
3. 在“资源管理器”窗格中，转到 program.cs 文件。
4. 在第 77 行，将受众更改为 Azure 视频分析器终结点，后跟 /videos/\*。 该消息应类似于：

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > 可以在 Azure 门户的视频分析器资源的概述部分找到视频分析器终结点。 本文后面的[列出视频资源](#list-video-resources)中将该值引用为 `clientApiEndpointUrl`。

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="显示播放器小组件终结点的屏幕截图。":::
    
5. 在第 78 行，将颁发者更改为证书的颁发者值（例如，`https://contoso.com`）。
6. 保存文件。 系统可能会提示以下消息：“jwt 令牌颁发者”中缺少用于生成和调试的必要资产。是否添加它们? 选择 **“是”** 。
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="显示 Visual Studio Code 中所需资产提示的屏幕截图。":::
   
7. 打开命令提示符窗口，浏览到包含 JWTTokenIssuer 文件的文件夹。 运行以下两个命令：`dotnet build`，接着是 `dotnet run`。 如果你的 Visual Studio Code 上有 C# 扩展，还可以选择 F5 以运行 JWTTokenIssuer 应用程序。

这将生成并运行应用程序。 生成应用程序后，它会创建一个自签名证书，并从该证书生成 JWT 令牌信息。 你还可以运行 JWTTokenIssuer.exe 文件，此文件位于生成了 JWTTokenIssuer 的目录的调试文件夹中。 运行应用程序的好处是可以指定输入选项，如下所示：

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer 将创建 JWT 和以下必需的组件：

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

请务必复制并保存这些值供以后使用。

## <a name="create-an-access-policy"></a>创建访问策略

访问策略定义了访问特定视频流的权限和持续时间。 在本教程中，你将在 Azure 门户中为视频分析器配置访问策略。  

1. 登录到 Azure 门户，然后转到视频分析器帐户所在的资源组。
1. 选择视频分析器资源。
1. 在“视频分析器”下，选择“访问策略” 。

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="显示“访问策略”选项的屏幕截图。":::
   
1. 选择“新建”，然后输入以下信息：

   - 访问策略名称：可以选择任何名称。

   - 颁发者：此值必须与 JWT 颁发者一致。 

   - 受众：JWT 的受众。 `${System.Runtime.BaseResourceUrlPattern}` 为默认值。 若要详细了解受众和 `${System.Runtime.BaseResourceUrlPattern}`，请参阅[访问策略](./access-policies.md)。

   - **密钥类型**：RSA 

   - 算法：支持的值为 RS256、RS384 和 RS512。

   - 密钥 ID - 此 ID 从证书生成。 有关详细信息，请参阅[创建令牌](#create-a-token)。

   - RSA 密钥模数：此值从证书生成。 有关详细信息，请参阅[创建令牌](#create-a-token)。

   - RSA 密钥指数：此值从证书生成。 有关详细信息，请参阅[创建令牌](#create-a-token)。

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="显示访问策略门户的屏幕截图。"::: 
   
   > [!NOTE] 
   > 这些值来自上一步中创建的 JWTTokenIssuer 应用程序。

1. 选择“保存”。

## <a name="list-video-resources"></a>列出视频资源

接下来，生成视频资源的列表。 需要对先前使用的帐户终结点进行 REST 调用，并使用生成的令牌进行身份验证。

可以通过多种方式将 GET 请求发送到 REST API，这里使用的是 JavaScript 函数。 下面的代码使用 [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) 与在页面上的 `clientApiEndpointUrl` 和 `token` 字段中存储的值来发送同步 `GET` 请求。 然后，它使用生成的视频列表，并将其存储在页面上已设置的 `videoList` 文本区域中。

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
   > [!NOTE]
   >`clientApiEndPoint` 和令牌是在[创建令牌](#create-a-token)这一部分中收集的。

## <a name="add-the-video-analyzer-player-component"></a>添加视频分析器播放器组件

现在你已有客户端 API 终结点 URL、令牌和视频名称，接下来可将播放器添加到页面。

1. 通过直接向页面添加包，使其包括播放器模块本身。 可以在应用程序中加入 NPM 包方向，也可以在运行时将其动态嵌入，如下所示：
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. 将 `AVA-Player` 元素添加到文档中：
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
1. 将视频加载到播放器中以开始。
   ```javascript
   avaPlayer.load();
   ```

## <a name="put-it-all-together"></a>将其放在一起

将上述 Web 元素组合在一起可以获得以下静态 HTML 页面。 有了此页面，就可以使用帐户终结点和令牌来查看视频。

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

可以在本地测试此页面，但建议测试托管版本。 如果无法快速托管页面，请参考下面的说明，了解如何将[静态网站](../../storage/blobs/storage-blob-static-website.md)与 Azure 存储一起使用来执行此操作。 以下步骤是[这些更完整的说明](../../storage/blobs/storage-blob-static-website-how-to.md)的精简版本。 这些步骤针对你在本教程中使用的文件进行了更新。

1. 创建存储帐户。
1. 在“数据管理”下，选择“静态网站”。
1. 在存储帐户上启用静态网站。
1. 在“索引文档名称”中，输入 index.html。
1. 在“错误文档路径”中，输入 404.html。
1. 选择“保存”。
1. 记下显示的“主终结点”。 这将是你的网站。
1. 在“主终结点”上方，选择“$web”。
1. 使用顶部的“上传”按钮，上传静态 HTML 页面作为 index.html。

## <a name="play-a-video"></a>播放视频

现在，你已经托管了页面，接下来转到该页面，并完成以下步骤来播放视频。

1. 输入“客户端 API 终结点 URL”和“令牌”值。
1. 选择“获取视频”。
1. 在视频列表中选择视频名称，并将其输入“视频名称”字段。
1. 选择“播放视频”。

## <a name="additional-details"></a>其他详细信息

以下各节包含一些需要注意的重要附加细节。

### <a name="refresh-the-access-token"></a>刷新访问令牌

播放器使用之前生成的访问令牌来获取播放授权令牌。 令牌会定期到期，需要进行刷新。 当你生成新的访问令牌后，可通过两种方式来刷新播放器的访问令牌：

* 主动调用小组件方法 `setAccessToken`。
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* 通过侦听 `TOKEN_EXPIRED` 事件对此事件进行操作。
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` 事件将在令牌过期前 5 秒发生。 如果你要设置事件侦听器，应在对播放器小组件调用 `load` 函数之前进行设置。

### <a name="configuration-details"></a>配置详细信息

前面的播放器的配置较为简单，你也可以使用更多配置值的选项。 以下是受支持的字段：

| 名称   | 类型             | 说明                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | 字符串 | 小组件的 JWT 令牌 |
| `videoName` | 字符串 | 视频资源的名称  |
| `clientApiEndpointUrl` | 字符串 | 客户端 API 的终结点 URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>将代码加载到应用程序中的替代方法

用于将代码放入应用程序的包是 [NPM 包](https://www.npmjs.com/package/@azure/video-analyzer-widgets)。 在前面的示例中，最新版本是在运行时直接从存储库加载的。 不过，你也可以使用以下代码在本地下载和安装包：

```bash
npm install @azure/video-analyzer/widgets
```

或者，可以在应用程序代码中使用适用于 TypeScript 的以下语句导入它：

```typescript
import { Player } from '@video-analyzer/widgets';
```

如果要动态创建播放器小组件，可以使用适用于 Javascript 的以下语句：
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

如果要使用此方法来导入，在导入完成后，需要以编程方式创建播放器对象。 在前面的示例中，你使用 `ava-player` HTML 标记将模块添加到了页面。 若要通过代码创建播放器对象，可在 JavaScript 中运行以下代码：

```javascript
const avaPlayer = new ava.widgets.player();
```

或在 TypeScript 中运行以下代码：

```typescript
const avaPlayer = new Player();
```

然后必须将其添加到 HTML：

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>后续步骤

* 详细了解[小组件 API](https://github.com/Azure/video-analyzer/tree/main/widgets)。
