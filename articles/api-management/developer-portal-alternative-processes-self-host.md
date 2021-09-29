---
title: 自承载开发人员门户的替代方法
titleSuffix: Azure API Management
description: 了解在 Azure API 管理中自承载开发人员门户时可以使用的替代方法。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: fb8514423bf4efd42373ad2f111aa3a96c5c0969
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582890"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>自承载开发人员门户的替代方法

[自承载开发人员门户](developer-portal-self-host.md)时，有几种替代方法可以进行探索：

* 使用设计器和发布服务器的生产版本。

* 使用 Azure 函数应用发布门户。

* 使用内容分发网络 (CDN) 将门户的文件置于前面，以减少页面加载时间。

本文提供了有关上述每种方法的信息。 

如果尚未这样做，请为最新版本的开发人员门户设置[本地环境](developer-portal-self-host.md#step-1-set-up-local-environment)。

## <a name="build-for-production"></a>用于生产的版本

如果要联机承载门户的开发环境以实现协作，请使用设计器和发布服务器的生产版本。 生产版本会捆绑文件、排除源映射等。

通过运行以下命令在 `./dist/designer` 目录中创建捆绑包：

```sh
npm run build-designer
```

结果是获得一个单页面应用程序，因此仍可将它部署到静态 Web 主机，例如 Azure Blob 存储静态网站。

同样，将经过编译和优化的发布服务器置于 `./dist/publisher` 文件夹中：

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>使用函数应用发布门户

在云中运行发布步骤，以作为在本地执行的替代方法。

若要使用 Azure 函数应用实现发布，需要满足以下先决条件：

- [创建 Azure 函数](../azure-functions/functions-get-started.md)。 该函数需要是 JavaScript 语言函数。
- 安装 Azure Functions Core Tools：
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>步骤 1：配置输出存储

将内容直接上传到承载网站（输出存储的“$web”容器），而不是本地文件夹。 在 `./src/config.publish.json` 文件中配置此更改：

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>步骤 2：生成并部署函数应用

`./examples` 文件夹中有一个示例 HTTP 触发器函数。 若要生成它并置于 `./dist/function` 中，请运行以下命令：

```sh
npm run build-function
```

然后，登录 Azure CLI 并部署它：

```azurecli
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

部署后，可以使用 HTTP 调用来调用它：

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>承载和 CDN

在[自承载开发人员门户](developer-portal-self-host.md)中，建议使用 Azure 存储帐户来承载网站。 但是，可以通过任何解决方案发布文件（包括承载提供程序的服务）。

还可以使用内容分发网络 (CDN) 将文件置于前面，以减少页面加载时间。 建议使用 [Azure CDN](https://azure.microsoft.com/services/cdn/)。

## <a name="next-steps"></a>后续步骤

详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)
