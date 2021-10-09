---
title: 使用 Azure 视频分析器播放器小组件
description: 本参考文章介绍了如何将视频分析器播放器小组件添加到应用程序。
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: ffc17e756a303723fe1d21c6ba221fed31147eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620566"
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
* [连续视频录制和播放](./use-continuous-video-recording.md)或[在边缘设备上检测移动并录制视频](./detect-motion-record-video-clips-cloud.md)
* 创建[令牌](./access-policies.md#creating-a-token)
* 创建[访问策略](./access-policies.md#creating-an-access-policy)

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
   >`clientApiEndPoint` 和令牌通过[创建令牌](./access-policies.md#creating-a-token)进行收集

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
   
## <a name="add-the-zone-drawer-component"></a>添加区域抽屉组件

1. 将 AVA-Zone-Drawer 元素添加到文档中：
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer"></ava-zone-drawer>
   ```
1. 获取指向页面中视频分析器区域抽屉的链接：
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. 将区域抽屉加载到播放器：
   ```javascript
   zoneDrawer.load();
   ```
1. 若要创建和保存区域，必须在此处添加事件侦听器：
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
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
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="avaPlayer"></ava-player>
</ava-zone-drawer>
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
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

如果要动态创建播放器小组件，可以使用适用于 Javascript 的以下语句：
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


如果要使用此方法来导入，在导入完成后，需要以编程方式创建区域抽屉和播放器对象。  在前面的示例中，你使用 `ava-player` HTML 标记将模块添加到了页面。 若要通过代码创建区域抽屉和播放器对象，可在 JavaScript 中运行以下代码：


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

或在 TypeScript 中运行以下代码：

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

然后必须将其添加到 HTML：

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>后续步骤

* 详细了解[小组件 API](https://github.com/Azure/video-analyzer/tree/main/widgets)。
