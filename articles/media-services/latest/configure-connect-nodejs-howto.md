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
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096053"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>连接到媒体服务 v3 API - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 node.js SDK。

## <a name="prerequisites"></a>必备条件

- 安装 [Node.js](https://nodejs.org/en/download/)。
- 安装 [Typescript](https://www.typescriptlang.org/download)。
- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。

> [!IMPORTANT]
> 查看 Azure 媒体服务 [命名约定](media-services-apis-overview.md#naming-conventions) ，了解实体的重要命名限制。 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>参考文档 @Azure/arm-mediaservices
- [适用于 Node.js的 Azure 媒体服务模块的参考文档 ](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure 上的 Node.js 的更多开发人员文档
- [适用于 JavaScript & Node.js 开发人员的 Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Git 中心存储库中的媒体服务源代码 @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [面向 Node.js 开发人员的 Azure 包文档](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>安装包

1. 使用偏好的编辑器创建一个 package.json 文件。
1. 打开该文件并粘贴以下代码：

   确保获取[适用于 JavaScript 的 AzureMediaServices SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)。

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

应指定以下包：

|程序包|说明|
|---|---|
|`@azure/arm-mediaservices`|Azure 媒体服务 SDK。 <br/>若要确保使用的是最新的 Azure 媒体服务包，请选中 " [npm 安装 @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices)"。|
|`@azure/ms-rest-nodeauth` | 使用服务主体或托管标识进行 AAD 身份验证所必需的|
|`@azure/storage-blob`|存储 SDK。 将文件上传到资产时使用。|
|`@azure/ms-rest-js`| 用于登录。|
|`@azure/storage-blob` | 用于将文件上传和下载到 Azure 媒体服务中的资产进行编码。|
|`@azure/abort-controller`| 与存储客户端一起用于超时长时间运行的下载操作|


可以运行以下命令以确保使用的是最新的包：

### <a name="install-azurearm-mediaservices"></a>安装 @azure/arm-mediaservices
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>安装 @azure/ms-rest-nodeauth

请安装 " @azure/ms-rest-nodeauth "： "^ 3.0.0" 的最低版本。

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>使用 TypeScript 连接到 Node.js 客户端

1. 使用偏好的编辑器创建 TypeScript 文件。
1. 打开该文件并粘贴以下代码。
1. 创建一个 env 文件，并从 Azure 门户中填写详细信息。 请参阅 [访问 api](./access-api-howto.md)。

### <a name="sample-env-file"></a>示例 env 文件
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
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

## <a name="typescript---hello-world---list-assets"></a>Typescript Hello World 列出资产
此示例演示如何使用帐户中的服务主体和列表资产连接到媒体服务客户端。 如果你使用的是新帐户，则该列表将返回空。 可以在门户中上传几个资产来查看结果。

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

## <a name="run-the-sample-application-helloworld-listassets"></a>运行示例应用程序 HelloWorld-ListAssets

克隆 Node.js 示例的存储库

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

将目录更改到 AMSv3Samples 文件夹
```bash
cd AMSv3Samples
```

安装 packages.js中使用的包
```
npm install 
```

将目录更改到 HelloWorld-ListAssets 文件夹中
```bash
cd HelloWorld-ListAssets
```

从 AMSv3Samples 文件夹启动 Visual Studio Code。 这是从 "vscode" 文件夹和文件上 tsconfig.js所在的文件夹启动所必需的
```dotnetcli
cd ..
code .
```

打开 HelloWorld-ListAssets 的文件夹，然后在 Visual Studio Code 编辑器中打开文件。
在索引 ts 文件中，按 F5 启动调试器。 如果你的资产已在帐户中，你应看到显示的资产列表。 如果帐户为空，您将看到一个空列表。  在门户中上传几个资产，查看结果。

## <a name="more-samples"></a>更多示例

[存储库](https://github.com/Azure-Samples/media-services-v3-node-tutorials)中提供了以下示例

|项目名称|用例|
|---|---|
|Live/index。 ts| 基本实时流式处理示例。 **警告**，请确保在使用 live 时，检查是否所有资源都已清理，并且不再在门户中计费|
|StreamFilesSample/index。 ts| 上载本地文件或从源 URL 编码的基本示例。 示例演示如何使用存储 SDK 下载内容，并演示如何流式传输到播放机 |
|StreamFilesWithDRMSample/index。 ts| 演示如何使用 Widevine 和 PlayReady DRM 进行编码和流式处理 |
|VideoIndexerSample/index。 ts| 使用视频和音频分析器预设生成视频或音频文件中的元数据和见解的示例 |

## <a name="see-also"></a>另请参阅

- [媒体服务概念](concepts-overview.md)
- [npm 安装 @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [适用于 JavaScript & Node.js 开发人员的 Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [存储库中的媒体服务源代码 @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>后续步骤

浏览媒体服务 [Node.js 参考](/javascript/api/overview/azure/mediaservices/management)文档并查看[示例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)，了解如何将媒体服务 API 与 Node.js 配合使用。
