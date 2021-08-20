---
title: 快速入门：在 Azure Arc 上创建 Web 应用
description: 开始使用 Azure Arc 上的应用服务部署第一个 Web 应用。
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 4002ef3d66eaae05881da0dd8d95cc82ffeb916d
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112377001"
---
# <a name="create-an-app-service-app-on-azure-arc-preview"></a>在 Azure Arc 上创建应用服务应用（预览版）

在本快速入门中，你将[为已启用 Azure Arc 的 Kubernetes 群集创建应用服务应用](overview-arc-integration.md)（预览版）。 此方案仅支持 Linux 应用，可以使用内置语言堆栈或自定义容器。

## <a name="prerequisites"></a>先决条件

- [设置已启用 Azure Arc 的 Kubernetes 以运行应用服务](manage-create-arc-environment.md)。

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="1-create-a-resource-group"></a>1. 创建资源组

运行以下命令。

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="2-get-the-custom-location"></a>2. 获取自定义位置

[!INCLUDE [app-service-arc-get-custom-location](../../includes/app-service-arc-get-custom-location.md)]


## <a name="3-create-an-app-service-plan"></a>3. 创建应用服务计划

运行以下命令，替换从上一步获得的 `$customLocationId`。

```azurecli-interactive
az appservice plan create -g myResourceGroup -n myPlan \
    --custom-location $customLocationId \
    --per-site-scaling --is-linux --sku K1
``` 

## <a name="4-create-an-app"></a>4. 创建应用

以下示例创建一个 Node.js 应用。 将 `<app-name>` 替换为在群集中唯一的名称（有效字符为 `a-z`、`0-9` 和 `-`）。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes --linux`](/cli/azure/webapp)。

```azurecli-interactive
 az webapp create \
    --plan myPlan \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --runtime 'NODE|12-lts'
```

## <a name="5-deploy-some-code"></a>5. 部署一些代码

> [!NOTE]
> 公共预览版期间不支持 `az webapp up`。

使用 Git 获取示例 Node.js 应用，然后使用 [ZIP 部署](deploy-zip.md)进行部署。 将 `<app-name>` 替换为你的 Web 应用名称。

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
cd nodejs-docs-hello-world
zip -r package.zip .
az webapp deployment source config-zip --resource-group myResourceGroup --name <app-name> --src package.zip
```

## <a name="6-get-diagnostic-logs-using-log-analytics"></a>6. 使用 Log Analytics 获取诊断日志

> [!NOTE]
> 若要使用 Log Analytics，应在之前[安装应用服务扩展](manage-create-arc-environment.md#install-the-app-service-extension)时启用它。 如果安装的扩展没有 Log Analytics，请跳过此步骤。

导航到[使用应用服务扩展配置的 Log Analytics 工作区](manage-create-arc-environment.md#install-the-app-service-extension)，然后单击左侧导航栏中的“日志”。 运行以下示例查询以显示过去 72 小时的日志。 将 `<app-name>` 替换为你的 Web 应用名称。 如果运行查询时出错，请在 10-15 分钟内重试（Log Analytics 开始从应用程序接收日志前可能会有延迟）。 

```kusto
let StartTime = ago(72h);
let EndTime = now();
AppServiceConsoleLogs_CL
| where TimeGenerated between (StartTime .. EndTime)
| where AppName_s =~ "<app-name>"
```

Kubernetes 群集中托管的所有应用的应用程序日志将记录到名为 `AppServiceConsoleLogs_CL` 的自定义日志表中的 Log Analytics 工作区中。 

Log_s 包含给定应用服务的应用程序日志，AppName_s 包含应用服务应用名称 。 除了通过应用程序代码写入的日志之外，Log_s 列还包含有关容器启动、关闭和函数应用的日志。

可以在 [Kusto 入门](../azure-monitor/logs/get-started-queries.md)中详细了解日志查询。

## <a name="optional-deploy-a-custom-container"></a>（可选）部署自定义容器

若要创建自定义容器应用，请使用 `--deployment-container-image-name` 运行 [az webapp create](/cli/azure/webapp#az_webapp_create)。 对于专用存储库，请添加 `--docker-registry-server-user` 和 `--docker-registry-server-password`。

例如，尝试以下操作：

```azurecli-interactive
az webapp create \
    --plan myPlan \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --deployment-container-image-name mcr.microsoft.com/appsvc/node:12-lts
```

<!-- `TODO: currently gets an error but the app is successfully created: "Error occurred in request., RetryError: HTTPSConnectionPool(host='management.azure.com', port=443): Max retries exceeded with url: /subscriptions/62f3ac8c-ca8d-407b-abd8-04c5496b2221/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/cephalin-arctest4/config/appsettings?api-version=2020-12-01 (Caused by ResponseError('too many 500 error responses',))"` -->

若要在创建应用后更新映像，请参阅[更改自定义容器的 Docker 映像](configure-custom-container.md?pivots=container-linux#change-the-docker-image-of-a-custom-container)

## <a name="next-steps"></a>后续步骤

- [配置 ASP.NET Core 应用](configure-language-dotnetcore.md?pivots=platform-linux)
- [配置 Node.js 应用](configure-language-nodejs.md?pivots=platform-linux)
- [配置 PHP 应用](configure-language-php.md?pivots=platform-linux)
- [配置 Linux Python 应用](configure-language-python.md)
- [配置 Java 应用](configure-language-java.md?pivots=platform-linux)
- [配置 Linux Ruby 应用](configure-language-ruby.md)
- [配置自定义容器](configure-custom-container.md?pivots=container-linux)
