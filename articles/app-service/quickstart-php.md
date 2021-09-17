---
title: 快速入门：创建 PHP Web 应用
description: 在数分钟内将第一个 PHP Hello World 部署到 Azure 应用服务。 我们使用 Git 进行部署，这是部署到应用服务时使用的多种方法中的一种。
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 05/02/2021
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94d863dfcc3b6fd8b1d316994541ea61fd32c080
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783018"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>在 Azure 应用服务中创建 PHP Web 应用

::: zone pivot="platform-windows"  
[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门教程演示如何将 PHP 应用部署到 Windows 上的 Azure 应用服务。
::: zone-end  

::: zone pivot="platform-linux"
[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门教程演示如何将 PHP 应用部署到 Linux 上的 Azure 应用服务。
::: zone-end  

在 Cloud Shell 中使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 Git 将 PHP 代码示例部署到该 Web 应用。

![在 Azure 中运行应用的示例](media/quickstart-php/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行此处的步骤。 安装先决条件后，大约需要五分钟完成这些步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">安装 PHP</a>

## <a name="download-the-sample-locally"></a>将示例下载到本地

1. 在终端窗口中运行以下命令。 这样会将示例应用程序克隆到本地计算机，并导航到包含示例代码的目录。 

    ```bash
    git clone https://github.com/Azure-Samples/php-docs-hello-world
    cd php-docs-hello-world
    ```
    
1. 确保默认分支为 `main`。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > 应用服务不需要更改分支名称。 但是，由于许多存储库将其默认分支更改为 `main`，因此本快速入门还介绍如何从 `main` 部署存储库。
    
## <a name="run-the-app-locally"></a>在本地运行应用

1. 在本地运行应用程序，以便你能了解将它部署到 Azure 时它的外观应该是什么样的。 打开一个终端窗口并使用 `php` 命令启动内置的 PHP Web 服务器。

    ```bash
    php -S localhost:8080
    ```
    
1. 打开 Web 浏览器并导航到 `http://localhost:8080` 处的示例应用。

    此时会看到“Hello World!” 消息（来自页面中显示的示例应用）。
    
    ![在本地运行的示例应用](media/quickstart-php/localhost-hello-world-in-browser.png)
    
1. 在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

1. 在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp#az_webapp_create) 命令在应用服务计划 `myAppServicePlan` 中创建一个 Web 应用。 

    在以下示例中，将 `<app-name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `PHP|7.4`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes`](/cli/azure/webapp#az_webapp_list_runtimes)。 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'PHP|7.4' --deployment-local-git
    ```
    
    创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    现在你已经创建了一个新的空 Web 应用并启用了 Git 部署。

    > [!NOTE]
    > Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 保存此 URL，后续将会用到。
    >

1. 浏览到新建的 Web 应用。 将 _&lt;app-name>_ 替换为在上一步中创建的唯一应用名称。

    ```bash
    http://<app-name>.azurewebsites.net
    ```

    新 Web 应用应该如下所示：

    ![空 Web 应用页](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

  <pre>
  Counting objects: 2, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (2/2), done.
  Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
  Total 2 (delta 1), reused 0 (delta 0)
  remote: Updating branch 'main'.
  remote: Updating submodules.
  remote: Preparing deployment for commit id '25f18051e9'.
  remote: Generating deployment script.
  remote: Running deployment command...
  remote: Handling Basic Web Site deployment.
  remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
  remote: Copying file: '.gitignore'
  remote: Copying file: 'LICENSE'
  remote: Copying file: 'README.md'
  remote: Copying file: 'index.php'
  remote: Ignoring: .git
  remote: Finished successfully.
  remote: Running post deployment command(s)...
  remote: Deployment successful.
  To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      cc39b1e..25f1805  main -> main
  </pre>

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```
http://<app-name>.azurewebsites.net
```

PHP 示例代码正在 Azure 应用服务 Web 应用中运行。

![在 Azure 中运行应用的示例](media/quickstart-php/hello-world-in-browser.png)

祝贺你！ 现已将第一个 PHP 应用部署到应用服务。

## <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

1. 使用本地文本编辑器打开 PHP 应用中的 `index.php` 文件，并对 `echo` 旁边的字符串中的文本稍微进行更改：

    ```php
    echo "Hello Azure!";
    ```

1. 在本地终端窗口中，提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

    ```bash
    git commit -am "updated output"
    git push azure main
    ```

1. 完成部署后，返回到“浏览到应用”步骤期间打开的浏览器窗口，然后刷新页面。

    ![已更新的在 Azure 中运行应用的示例](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

1. 转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。 搜索并选择“应用服务”。

    ![搜索应用服务, Azure 门户, 创建 PHP web 应用](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. 选择 Azure 应用名称。

    ![在门户中导航到 Azure 应用](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    此时会显示 Web 应用的“概览”页。 可在此处执行基本的管理任务，例如浏览、停止、重启和删除。   

    ![Azure 门户中的“应用服务”页](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Web 应用菜单提供用于配置应用的不同选项。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [带 MySQL 的 PHP](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [配置 PHP 应用](configure-language-php.md)
