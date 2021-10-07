---
title: 快速入门：创建静态 HTML Web 应用
description: 在数分钟内将第一个 HTML Hello World 部署到 Azure 应用服务。 我们使用 Git 进行部署，这是部署到应用服务时使用的多种方法中的一种。
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c342a898a7ad9f0918313b2292a0105c75f6c8a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535567"
---
# <a name="create-a-static-html-web-app-in-azure"></a>在 Azure 中创建静态 HTML Web 应用

本快速入门演示如何将基本 HTML+CSS 站点部署到 <abbr title="一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端应用程序。">Azure 应用服务</abbr>. 你将在 [Cloud Shell](../cloud-shell/overview.md) 中完成本快速入门，但是也可以使用 [Azure CLI](/cli/azure/install-azure-cli) 在本地运行这些命令。

## <a name="1-prepare-your-environment"></a>1.准备环境

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

在 [Cloud Shell](../cloud-shell/overview.md) 中，创建一个快速入门目录，然后切换到该目录。

```bash
mkdir quickstart

cd $HOME/quickstart
```

接下来请运行以下命令，将示例应用存储库克隆到快速入门目录。

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2.创建 Web 应用

切换到包含示例代码的目录并运行 `az webapp up` 命令。 将 `<app-name>` 替换为全局唯一名称。

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>疑难解答</summary>
<ul>
<li>如果无法识别 <code>az</code> 命令，请确保已按照<a href="#1-prepare-your-environment">准备环境</a>中所述安装了 Azure CLI。</li>
<li>将 <code>&lt;app-name&gt;</code> 替换为在整个 Azure 中均唯一的名称（有效字符为 <code>a-z</code>、<code>0-9</code> 和 <code>-</code>）<em></em>。 良好的模式是结合使用公司名称和应用标识符。</li>
<li><code>--sku F1</code> 参数在“免费”定价层上创建 Web 应用。 省略此参数可使用更快的高级层，这会按小时计费。</li>
<li><code>--html</code> 参数表示将所有文件夹内容视为静态内容并禁用生成自动化。</li>
<li>可以选择包含参数 <code>--location &lt;location-name&gt;</code>，其中 <code>&lt;location-name&gt;</code> 是可用的 Azure 区域。 可以运行 <a href="/cli/azure/appservice#az_appservice_list_locations"><code>az account list-locations</code></a> 命令来检索 Azure 帐户的允许区域列表。</li>
</ul>
</details>

此命令可能需要花费几分钟时间完成。 

<details>
<summary><code>az webapp up</code> 有何作用？</summary>
<p><code>az webapp up</code> 命令执行以下操作：</p>
<ul>
<li>创建一个默认的资源组。</li>
<li>创建一个默认的应用服务计划。</li>
<li>使用指定名称<a href="/cli/azure/webapp#az_webapp_create">创建应用服务应用</a>。</li>
<li><a href="/azure/app-service/deploy-zip">使用 Zip</a> 将文件从当前工作目录部署到应用。</li>
<li>运行时，它会提供有关资源创建、日志记录和 ZIP 部署的消息。</li>
</ul>

运行完成后，它会显示如下例所示的信息：

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

稍后你将需要使用 `resourceGroup` 值来[清理资源](#6-clean-up-resources)。

<hr/>

## <a name="3-browse-to-the-app"></a>3.浏览到应用

在浏览器中转到应用 URL：`http://<app_name>.azurewebsites.net`。

该页作为 Azure 应用服务 Web 应用运行。

![示例应用主页](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4.更新并重新部署应用

在 Cloud Shell 中，键入 `nano index.html` 以打开 nano 文本编辑器。 

在 `<h1>` 标题标记中，将“Azure 应用服务 - 示例静态 HTML 站点”更改为“Azure 应用服务”。

![Nano index.html](media/quickstart-html/nano-index-html.png)

使用命令 `^O` 保存所做的更改。

使用命令 `^X` 退出 nano。

使用 `az webapp up` 命令重新部署应用。

```bash
az webapp up --html
```

切换回在“浏览到应用”步骤中打开的浏览器窗口。

刷新页面。

![已更新的示例应用主页](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5.管理新的 Azure 应用

导航到 [Azure 门户](https://portal.azure.com)， 

搜索“应用服务”并将其选中  。

![在 Azure 门户中选择应用服务](./media/quickstart-html/portal0.png)

选择 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/quickstart-html/portal1.png)

这里我们可以看到 Web 应用的概述页。 并可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。

![Azure 门户中的“应用服务”边栏选项卡](./media/quickstart-html/portal2.png)

左侧菜单提供了用于配置应用的不同页面。

<hr/>

## <a name="6-clean-up-resources"></a>6.清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请在 Cloud Shell 中运行以下命令，以便删除资源组。 请记住，资源组名称已在[创建 Web 应用](#2-create-a-web-app)步骤中自动为你生成。

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

此命令可能需要花费一点时间运行。

<hr/>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 映射自定义域
