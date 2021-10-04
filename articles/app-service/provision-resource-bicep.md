---
title: 使用 Azure Bicep 创建应用服务应用
description: 使用 Azure Bicep 仅花费数秒便在 Azure 应用服务中创建你的第一个应用，这是将应用部署到应用服务的许多方式之一。
author: seligj95
ms.author: msangapu
ms.topic: article
ms.date: 8/26/2021
ms.openlocfilehash: ff9136889a475e8e98406476ea503982025d298d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061922"
---
# <a name="create-app-service-app-using-bicep"></a>使用 Bicep 创建应用服务应用

通过使用 [Bicep](/azure/azure-resource-manager/bicep/) 文件和 Cloud Shell 中的 [Azure CLI](/cli/azure/get-started-with-azure-cli) 将应用部署到云，开始使用 [Azure 应用服务](overview.md)。 由于使用的是免费应用服务层，因此完成本快速入门不会产生费用。

Bicep 是一种特定于域的语言 (DSL)，使用声明性语法来部署 Azure 资源。 它提供简明的语法、可靠的类型安全性以及对代码重用的支持。 可以使用 Bicep 而非 JSON 来开发 Azure 资源管理器模板（[ARM 模板](/azure/azure-resource-manager/templates/overview)）。 用于创建 ARM 模板的 JSON 语法可能比较繁琐，需要使用复杂的表达式。 Bicep 语法降低了这种复杂性，改进了开发体验。 Bicep 是基于 ARM 模板 JSON 的透明抽象，不会丢失任何 JSON 模板功能。 在部署过程中，Bicep CLI 将 Bicep 文件转译为 ARM 模板 JSON。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

若要使用 Bicep 有效创建资源，需要设置 Bicep [开发环境](/azure/azure-resource-manager/bicep/install)。 [Visual Studio Code](https://code.visualstudio.com/) 的 Bicep 扩展提供语言支持和资源自动完成功能。 此扩展可帮助你创建和验证 Bicep 文件，建议那些要在完成本快速入门后使用 Bicep 创建资源的开发人员使用它。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板如下所示。 它在 Linux 上部署应用服务计划和应用服务应用，并部署来自 [Azure 示例](https://github.com/Azure-Samples)存储库的示例 Node.js“Hello World”应用。

```bicep
param webAppName string = uniqueString(resourceGroup().id) // Generate unique String for web app name
param sku string = 'F1' // The SKU of App Service Plan
param linuxFxVersion string = 'node|14-lts' // The runtime stack of web app
param location string = resourceGroup().location // Location for all resources
param repositoryUrl string = 'https://github.com/Azure-Samples/nodejs-docs-hello-world'
param branch string = 'master'
var appServicePlanName = toLower('AppServicePlan-${webAppName}')
var webSiteName = toLower('wapp-${webAppName}')
resource appServicePlan 'Microsoft.Web/serverfarms@2020-06-01' = {
  name: appServicePlanName
  location: location
  properties: {
    reserved: true
  }
  sku: {
    name: sku
  }
  kind: 'linux'
}
resource appService 'Microsoft.Web/sites@2020-06-01' = {
  name: webSiteName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: linuxFxVersion
    }
  }
}
resource srcControls 'Microsoft.Web/sites/sourcecontrols@2021-01-01' = {
  name: '${appService.name}/web'
  properties: {
    repoUrl: repositoryUrl
    branch: branch
    isManualIntegration: true
  }
}
```

该模板中定义了三个 Azure 资源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。
* [Microsoft.Web/sites/sourcecontrols](/azure/templates/microsoft.web/sites/sourcecontrols)：创建外部 git 部署配置。

为了便于你使用，此模板包含多个预定义的参数。 有关参数默认值及其说明，请参阅下表：

| 参数 | 类型    | 默认值                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | 字符串  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 应用程序名称 |
| location   | 字符串  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 应用区域 |
| sku        | 字符串  | "F1"                         | 实例大小  |
| linuxFxVersion   | 字符串  | "NODE&#124;14-LTS"       | "编程语言堆栈 &#124; 版本" |
| repositoryUrl    | string  | "https://github.com/Azure-Samples/nodejs-docs-hello-world"    | 外部 Git 存储库（可选） |
| branch    | string  | “master”    | 代码示例的默认分支 |

---

## <a name="deploy-the-template"></a>部署模板

将模板复制并粘贴到首选编辑器/IDE，并将文件保存到本地工作目录。

此处使用 Azure CLI 来部署模板。 还可以使用 Azure 门户、Azure PowerShell 和 REST API。 若要了解其他部署方法，请参阅 [Bicep 部署命令](/azure/azure-resource-manager/bicep/deploy-cli)。

以下代码将创建一个资源组、一个应用服务计划和一个 Web 应用。 已为你设置了默认资源组、应用服务计划和位置。 请将 `<app-name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。

打开一个在其中安装了 Azure CLI 的终端，运行以下代码，以在 Linux 上创建 Node.js 应用。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --template-file <path-to-template>
```

若要部署其他语言堆栈，请使用相应的值更新 `linuxFxVersion`。 下面显示了示例。 若要显示当前版本，请在 Cloud Shell 中运行以下命令：`az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| 语言    | 示例：                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

## <a name="validate-the-deployment"></a>验证部署

浏览到 `http://<app_name>.azurewebsites.net/` 并验证它是否已创建。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，[可将其删除](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Bicep 文档](/azure/azure-resource-manager/bicep/)
> [!div class="nextstepaction"]
> [针对 Azure 应用服务的 Bicep 示例](/azure/app-service/samples-bicep)