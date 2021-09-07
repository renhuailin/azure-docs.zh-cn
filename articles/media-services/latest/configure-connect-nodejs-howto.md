---
title: 连接到 Azure 媒体服务 v3 API - Node.js
description: 本文演示如何使用 Node.js 连接到媒体服务 v3 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7c6bba2084247d76a79de9ae9efb1b3ea356a5c4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221496"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>连接到媒体服务 v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 node.js SDK。 你将使用 media-services-v3-node-tutorials 示例存储库中的文件。 HelloWorld-ListAssets 示例包含用于连接的代码，并在帐户中列出资产。

## <a name="prerequisites"></a>先决条件

- 安装 Visual Studio Code。
- 安装 [Node.js](https://nodejs.org/en/download/)。
- 安装 [TypeScript](https://www.typescriptlang.org/download)。
- [创建媒体服务帐户](./account-create-how-to.md)。 请务必记住资源组名称和媒体服务帐户名称。
- 创建应用程序的服务主体。 请参阅[访问 API](./access-api-howto.md)。<br/>专业提示！ 使此窗口保持打开状态，或将 JSON 选项卡中的所有内容复制到记事本。 
- 确保获取[适用于 JavaScript 的 AzureMediaServices SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)。

> [!IMPORTANT]
> 查看 Azure 媒体服务[命名约定](media-services-apis-overview.md#naming-conventions)，了解实体的重要命名限制。

## <a name="clone-the-nodejs-samples-repo"></a>克隆 Node.JS 示例存储库

你将在 Azure 示例中使用一些文件。 克隆 Node.JS 示例存储库。

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-nodejs-packages"></a>安装 Node.js 包

### <a name="install-azurearm-mediaservices"></a>安装 @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>安装 @azure/ms-rest-nodeauth

请安装“@azure/ms-rest-nodeauth”的最低版本：“^3.0.0”。

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

在此示例中，你将在 `package.json` 文件中使用以下包。

|程序包|说明|
|---|---|
|`@azure/arm-mediaservices`|Azure 媒体服务 SDK。 <br/>为确保使用的是最新的 Azure 媒体服务包，请检查 [npm install@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)。|
|`@azure/ms-rest-nodeauth` | 使用服务主体或托管标识进行 AAD 身份验证时所必需的|
|`@azure/storage-blob`|存储 SDK。 将文件上传到资产时使用。|
|`@azure/ms-rest-js`| 用于登录。|
|`@azure/storage-blob` | 用于将文件上传和下载到 Azure 媒体服务中的“资产”，以进行编码。|
|`@azure/abort-controller`| 与存储客户端一起用于超时长时间运行的下载操作|

### <a name="create-the-packagejson-file"></a>创建 package.json 文件

1. 使用偏好的编辑器创建一个 `package.json` 文件。
1. 打开该文件并粘贴以下代码：

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>使用 TypeScript 连接到 Node.js 客户端

### <a name="sample-env-file"></a>示例 .env 文件

将此文件的内容复制到名为 .env 的文件中。 它应存储在工作存储库的根目录中。 这些是你在门户中媒体服务帐户的“API 访问”页中获得的值。

若要访问那些需要输入到 .env 文件中的值，建议先阅读并查看操作指南文章：[访问 API](./access-api-howto.md)。
可以使用 Azure 门户或 CLI 来获取那些需要输入到此示例的环境变量文件中的值。

一旦创建了 .env 文件，就可以开始使用这些示例。

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>运行示例应用程序 HelloWorld-ListAssets

1. 将目录更改为 AMSv3Samples 文件夹

```bash
cd AMSv3Samples
```

2. 安装在 package.json 文件中使用的包。

```
npm install 
```

3. 将目录更改为 HelloWorld-ListAssets 文件夹。

```bash
cd HelloWorld-ListAssets
```

4. 从 AMSv3Samples 文件夹中启动 Visual Studio Code。 需要从“.vscode”文件夹和 tsconfig.json 文件所在的文件夹中启动

```dotnetcli
cd ..
code .
```

打开 HelloWorld-ListAssets 的文件夹，然后在 Visual Studio Code 编辑器中打开 index.ts 文件。

在 index.ts 文件中，按 F5 启动调试器。 如果你的资产已在帐户中，应会看到显示的资产列表。 如果帐户为空，你将看到一个空列表。  

若要快速查看列出的资产，请使用门户上传几个视频文件。 将自动创建每个资产，然后再次运行此脚本以返回它们的名称。

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>详细了解 HelloWorld-ListAssets 示例

HelloWorld-ListAssets 示例说明了如何使用服务主体连接到媒体服务客户端，并列出帐户中的资产。 有关其作用的详细说明，请参阅代码中的注释。

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>更多示例

以下示例可在[存储库](https://github.com/Azure-Samples/media-services-v3-node-tutorials)中获得

|项目名称|用例|
|---|---|
|Live/index.ts| 基本实时传送视频流示例。 警告：在使用实时传送视频流时，请确保检查是否已清理所有资源，并且不再在门户中计费|
|StreamFilesSample/index.ts| 上传本地文件或从源 URL 编码的基本示例。 示例演示如何使用存储 SDK 下载内容，并演示如何流式传输到播放机 |
|StreamFilesWithDRMSample/index.ts| 演示如何使用 Widevine 和 PlayReady DRM 进行编码和流式传输 |
|VideoIndexerSample/index.ts| 使用视频和音频分析器预设生成视频或音频文件中的元数据和见解的示例 |

## <a name="see-also"></a>另请参阅

- [用于 Node.js 的 Azure 媒体服务模块的参考文档](/javascript/api/overview/azure/media-services)
- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js Git 中心存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [面向 Node.js 开发人员的 Azure 包文档](/javascript/api/overview/azure/)
- [媒体服务概念](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [面向 JavaScript 和 Node.js 开发人员的 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js 存储库中的媒体服务源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>后续步骤

浏览媒体服务 [Node.js 参考](/javascript/api/overview/azure/mediaservices/management)文档并查看[示例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)，了解如何将媒体服务 API 与 Node.js 配合使用。
