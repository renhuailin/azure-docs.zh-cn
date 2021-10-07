---
title: 使用 Azure 资源管理器模板创建应用服务应用
description: 使用 Azure 资源管理器模板（ARM 模板）在几秒钟时间内在 Azure 应用服务中创建你的第一个应用，这是将应用部署到应用服务的许多方式之一。
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2940e407e88239aa1afec84137e72073b7a5028a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533230"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>快速入门：使用 ARM 模板创建应用服务应用

通过使用以下工具将应用部署到云中，开始使用 [Azure 应用服务](overview.md) <abbr title="一个 JSON 文件，它以声明方式定义一个或多个 Azure 资源以及所部署资源之间的依赖关系。 使用模板能够以一致方式反复部署资源。">ARM 模板</abbr> 和 Cloud Shell 中的 [Azure CLI](/cli/azure/get-started-with-azure-cli)。 由于使用的是免费应用服务层，因此完成本快速入门不会产生费用。 <abbr title="在声明性语法中，你可以在不编写创建部署的编程命令序列的情况下，描述预期部署。">模板使用声明性语法。</abbr>

 如果你的环境满足先决条件，而且你熟悉如何使用 [ARM 模板](../azure-resource-manager/templates/overview.md)，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

::: zone pivot="platform-windows"
[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1.准备环境

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2.查看模板

::: zone pivot="platform-windows"
本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/app-service-docs-windows)。 它在 Windows 上部署应用服务计划和应用服务应用。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json":::

<details>
<summary>模板中定义了哪些资源和参数？</summary>

该模板中定义了两个 Azure 资源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。

下表详细说明了默认参数及其说明：

| 参数 | 类型    | 默认值                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | 字符串  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 应用程序名称 |
| location   | 字符串  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 应用区域 |
| sku        | 字符串  | "F1"                         | 实例大小（F1 = 免费层） |
| 语言   | 字符串  | ".net"                       | 编程语言堆栈（.net、php、node、html） |
| helloWorld | boolean | False                        | True = 部署“Hello World”应用 |
| repoUrl    | 字符串  | " "                          | 外部 Git 存储库（可选） |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/app-service-docs-linux)。 它在 Windows 上部署应用服务计划和应用服务应用。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json":::

为了方便起见，此模板包括已定义的 Azure 资源和参数。

<details>
<summary>模板中定义了哪些资源和参数？</summary>

该模板中定义了两个 Azure 资源：

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：创建应用服务计划。
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：创建应用服务应用。

下表详细说明了默认参数及其说明：

| 参数 | 类型    | 默认值                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | 字符串  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | 应用程序名称 |
| location   | 字符串  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | 应用区域 |
| sku        | 字符串  | "F1"                         | 实例大小（F1 = 免费层） |
| linuxFxVersion   | 字符串  | "DOTNETCORE&#124;3.0        | "编程语言堆栈 &#124; 版本" |
| repoUrl    | 字符串  | " "                          | 外部 Git 存储库（可选） |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3.部署模板

::: zone pivot="platform-windows"
运行以下代码，使用 Azure CLI 在 Windows 上部署 .NET Framework 应用。 

Replace <abbr title="有效的字符是 `a-z`、`0-9` 和 `-`。"> \<app-name> </abbr> 为全局唯一的应用名称。 若要了解其他 <abbr title="还可以使用 Azure 门户、Azure PowerShell 和 REST API。">部署方法</abbr>，请参阅[部署模板](../azure-resource-manager/templates/deploy-powershell.md)。 可以[在此处找到更多 Azure 应用服务模板示例](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
运行以下代码，在 Linux 上创建 Python 应用。 

将 \<app-name\> 替换为全局唯一的应用名称。 有效的字符是 `a-z`、`0-9` 和 `-`。

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>代码的作用是什么？</summary>
<p>命令会执行以下操作：</p>
<ul>
<li>创建默认 <abbr title="可作为一个单元进行管理的相关 Azure 资源的逻辑容器。">资源组</abbr>.</li>
<li>创建默认 <abbr title="指定托管应用的 Web 服务器场的位置、大小和功能的计划。">应用服务计划</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">创建具有指定名称的 <abbr title="Web 应用的表示形式，其中包含应用代码、DNS 主机名、证书和相关资源。">应用服务应用</abbr></a>。</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>如何部署其他语言堆栈？</summary>
若要部署其他语言堆栈，请更新 <abbr title="该模板与 .NET Core、.NET Framework、PHP、Node.js 和静态 HTML 应用兼容。">语言参数</abbr> 使用相应的值。 对于 Java，请参阅<a href="/azure/app-service/quickstart-java-uiex">创建 Java 应用</a>。

| 参数 | 类型    | 默认值                | 说明 |
|------------|---------|------------------------------|-------------|
| 语言   | 字符串  | ".net"                       | 编程语言堆栈（.net、php、node、html） |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>如何部署其他语言堆栈？</summary>
 
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4.验证部署

浏览到 `http://<app_name>.azurewebsites.net/` 并验证它是否已创建。

<hr/>

## <a name="5-clean-up-resources"></a>5.清理资源

不再需要资源组时，[可将其删除](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)。

<hr/>

## <a name="next-steps"></a>后续步骤

- 从本地 Git 进行部署
- [将 ASP.NET Core 与 SQL 数据库配合使用](tutorial-dotnetcore-sqldb-app.md)
- 将 Python 与 Postgres 配合使用
- [带 MySQL 的 PHP](tutorial-php-mysql-app.md)
- [使用 Java 连接到 Azure SQL 数据库](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
