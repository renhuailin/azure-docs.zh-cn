---
title: 授予 Azure Functions 应用访问媒体服务帐户的权限
description: 假设你要为广播工作室制作一个“正在直播”标志。 可以使用媒体服务 API 确定媒体服务直播活动何时运行，但可能很难从嵌入式设备调用它。 可以改为使用 Azure Functions 为嵌入式设备公开 HTTP API。 然后，Azure Functions 便可调用媒体服务来获取直播活动的状态。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: inhenkel
ms.openlocfilehash: dc05d6488978004eebee68b901214ab71f0fffd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656638"
---
# <a name="tutorial-give-an-azure-function-app-access-to-a-media-services-account"></a>教程：向 Azure Functions 应用授予访问媒体服务帐户的权限

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

假设你想让访问网站或应用程序的人知道你在广播工作室中的状态是“正在直播”。 可以使用媒体服务 API 确定媒体服务直播活动何时运行，但可能很难从嵌入式设备调用它。 可以改为使用 Azure Functions 为嵌入式设备公开 HTTP API。 然后，Azure Functions 便可调用媒体服务来获取直播活动的状态。

:::image type="content" source="media/diagrams/managed-identities-scenario-function-app-access-media-services-account.svg" alt-text="为 Functions 应用提供媒体服务帐户访问权限的托管标识":::

本教程使用 2020-05-01 媒体服务 API。

## <a name="sign-in-to-azure"></a>登录 Azure

要使用本文中的任何命令，首先必须登录到要使用的订阅。

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>设置订阅

使用此命令设置要使用的订阅。

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 强烈建议在学习本教程之前先完成[通过命令行在 Azure 中创建 C# 函数快速入门](../../azure-functions/create-first-function-cli-csharp.md)。  这是因为其中包含的设置步骤与此处所需的步骤相同。  在该快速入门中，还可以使用作为本教程基础的简单示例。

## <a name="resource-names"></a>资源名称

在开始之前，确定要创建的资源名称。  为这些资源命名时应使其易于识别为一组，尤其是在完成测试后不打算再使用它们。 许多资源类型的命名规则是不同的，因此最好始终采用全小写形式。 例如，“mediatest1rg”表示资源组名称，而“mediatest1stor”表示存储帐户名称。 对本文中的每个步骤使用相同的名称。

你将看到以下命令中引用了这些名称。  所需资源名称包括：

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region
- your-function-name：使用“OnAir”
- your-live-event-name：使用“live1”
- your-ip-address：使用“0.0.0./32”

> [!NOTE]
> 上述连字符仅用于分隔引导词。 由于 Azure 服务中的命名资源不一致，因此在命名资源时请勿使用连字符。<br/><br/>
> 用 00000000-0000-0000-0000000000 表示的任何内容都是资源的唯一标识符。  此值通常由 JSON 响应返回。 此外，还建议在记事本或其他文本编辑器中复制并粘贴 JSON 响应，因为这些响应将包含以后 CLI 命令所需的值。<br/><br/>
> 此外，也不用创建区域名称。  区域名称由 Azure 确定。

### <a name="list-azure-regions"></a>列出 Azure 区域

如果不确定要使用的实际区域名称，请使用此命令获取列表：

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>序列

以下每个步骤都是按特定顺序完成的，因为你在序列的下一步中使用了来自 JSON 响应的一个或多个值。

## <a name="create-a-resource-group"></a>创建资源组

你将创建的资源必须属于资源组。 首先创建资源组。 你将在媒体服务帐户创建步骤和后续步骤中使用 `your-resource-group-name`。

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>创建存储帐户

你将创建的媒体服务帐户必须具有与之关联的存储帐户。 首先为媒体服务帐户创建存储帐户。 你将在后续步骤中使用 `your-storage-account-name`。

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>创建媒体服务帐户

现在创建媒体服务帐户。 查找 `

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-cli.md)]

## <a name="set-up-the-azure-function"></a>设置 Azure Functions

在本部分中，你将设置 Azure Functions。

### <a name="get-the-code"></a>获取代码

使用 Azure Functions 创建函数项目，并从 HTTP 模板检索代码。

```azurecli-interactive
func init MediaServicesLiveMonitor –dotnet
```

### <a name="change-directory"></a>更改目录

请务必将工作目录更改为项目目录，  否则会出现错误。

```azurecli-interactive
cd .\MediaServicesLiveMonitor\
```

### <a name="name-your-function"></a>为函数命名

```azurecli-interactive
func new --name OnAir --template "HTTP trigger" --authlevel "anonymous"
```

## <a name="configure-the-functions-project"></a>配置函数项目

### <a name="install-media-services-and-other-extensions"></a>安装媒体服务和其他扩展

在终端窗口中运行 dotnet add package 命令，在项目中安装所需的扩展包。 以下命令安装媒体服务和 Azure 标识包。

```bash
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

### <a name="edit-the-onaircs-code"></a>编辑 OnAir.cs 代码

更改 `OnAir.cs` 文件。 将 `subscriptionId`、`resourceGroup` 和 `mediaServicesAccountName` 变量更改为之前确定的变量。

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Rest;
using System.Threading.Tasks;

namespace MediaServicesLiveMonitor
{
    public static class OnAir
    {
        [FunctionName("OnAir")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            if (string.IsNullOrWhiteSpace(name))
            {
                return new BadRequestObjectResult("Missing 'name' URL parameter");
            }

            var credential = new ManagedIdentityCredential();
            var accessTokenRequest = await credential.GetTokenAsync(
                new TokenRequestContext(
                    scopes: new string[] { "https://management.core.windows.net" + "/.default" }
                    )
                );
            ServiceClientCredentials credentials = new TokenCredentials(accessTokenRequest.Token, "Bearer");

            var subscriptionId = "00000000-0000-0000-000000000000";                 // Update
            var resourceGroup = "<your-resource-group-name>";                       // Update
            var mediaServicesAccountName = "<your-media-services-account-name>";    // Update

            var mediaServices = new AzureMediaServicesClient(credentials)
            {
                SubscriptionId = subscriptionId
            };

            var liveEvent = await mediaServices.LiveEvents.GetAsync(resourceGroup, mediaServicesAccountName, name);

            if (liveEvent == null)
            {
                return new NotFoundResult();
            }

            return new OkObjectResult(liveEvent.ResourceState == LiveEventResourceState.Running ? "On air" : "Off air");
        }
    }
}
```

## <a name="create-the-function-app"></a>创建 Functions 应用

创建用于托管函数的 Functions 应用。 该名称与之前下载的名称相同，即 `MediaServicesLiveMonitorApp`。

```azurecli-interactive

az functionapp create --resource-group <your-resource-group-name> --consumption-plan-location your-region --runtime dotnet --functions-version 3 --name MediaServicesLiveMonitorApp --storage-account mediatest3store --assign-identity "[system]"

```

在 JSON 响应中查找 `principalId`：

```json
{
...  
"identity": {
//Note the principalId value for the following step
    "principalId": "00000000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-000000000000",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  }
...
```

## <a name="grant-the-function-app-access-to-the-media-services-account-resource"></a>向函数应用授予媒体服务帐户资源的访问权限

对于此请求：

- `assignee` 是来自 `az functionapp create` 的 JSON 响应中的 `principalId`
- `scope` 是来自 `az ams account create` 的 JSON 响应中的 `id`。  请参阅上面的示例 JSON 响应。

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Media Services Account Administrator" --scope "/subscriptions/<the-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.Media/mediaservices/<your-media-services-account-name>"
```

## <a name="publish-the-function"></a>发布函数

```azurecli-interactive
func azure functionapp publish MediaServicesLiveMonitorApp
```

## <a name="validation"></a>验证

在浏览器中，转到函数 URL，例如：

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

这应会返回 404（未找到）错误，因为直播活动尚不存在。

### <a name="create-a-live-event"></a>创建直播活动

```azurecli-interactive
az ams live-event create --resource-group test3 --account-name mediatest3 --name live1 --streaming-protocol RTMP
```

在浏览器中，转到函数 URL，例如：

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

此时应会显示“正在直播”。

### <a name="start-the-live-event"></a>启动直播活动

如果启动直播活动，函数应会返回“正在直播”。

```azurecli-interactive
az ams live-event start live1
```

此函数可供任何人使用。 保护对 Azure Functions 的访问和接通“正在直播”灯不在本文档的讨论范围内。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用已创建的资源，请删除资源组。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
