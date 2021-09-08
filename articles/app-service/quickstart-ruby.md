---
title: 快速入门：创建 Ruby 应用
description: 将第一个 Ruby 应用部署到应用服务中的 Linux 容器即可开始使用 Azure 应用服务。
keywords: azure 应用服务, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dffb2634b59c54632364d8469244edb6840d7b9d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435413"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>在应用服务中创建 Ruby on Rails 应用

[Linux 上的 Azure 应用服务](overview.md#app-service-on-linux)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门教程介绍了如何使用 [Cloud Shell](../cloud-shell/overview.md) 将 Ruby on Rails 应用部署到 Linux 上的应用服务。

> [!NOTE]
> Ruby 开发堆栈目前仅支持 Ruby on Rails。 如果想要使用不同的平台（如 Sinatra），或者想要使用不受支持的 Ruby 版本，则需要[在自定义容器中运行它](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux)。

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">安装 Ruby 2.6 或更高版本</a>
* <a href="https://git-scm.com/" target="_blank">安装 Git</a>

## <a name="download-the-sample"></a>下载示例

1. 在终端窗口中，将示例应用程序克隆到本地计算机，并导航到包含示例代码的目录。 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. 确保默认分支为 `main`。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > 应用服务不需要更改分支名称。 但是，由于许多存储库正在将默认分支更改为 `main`，因此本教程还介绍如何从 `main` 部署存储库。 有关详细信息，请参阅[更改部署分支](deploy-local-git.md#change-deployment-branch)。

## <a name="run-the-application-locally"></a>在本地运行应用程序

1. 安装所需的 gem。 示例中包含了 `Gemfile`，因此只需运行以下命令：

    ```bash
    bundle install
    ```

1. 安装 gem 后，启动应用：

    ```bash
    bundle exec rails server
    ```

1. 使用 Web 浏览器导航到 `http://localhost:3000` 以在本地测试该应用。

    ![已配置了 Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

1. 在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](overview.md#app-service-on-linux)。 

    在 Cloud Shell 中可以使用 [`az webapp create`](/cli/azure/webapp) 命令。 在以下示例中，将 `<app-name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `RUBY|2.6`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes --linux`](/cli/azure/webapp)。 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6' --deployment-local-git
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
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    现在你已经创建了一个新的空 Web 应用并启用了 Git 部署。

    > [!NOTE]
    > Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 保存此 URL，后续将会用到。
    >

1. 浏览到该应用，查看使用内置映像新建的 Web 应用。 将 &lt;app-name> 替换为你的 Web 应用名称。

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    新 Web 应用应该如下所示：

    ![启动页面](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>部署应用程序

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>浏览到应用

部署完成后，请等待大约 10 秒，然后重启 Web 应用，再导航到 Web 应用并验证结果。

```bash
http://<app-name>.azurewebsites.net
```

![更新的 Web 应用](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> 应用重启时，可能会在浏览器中看到 HTTP 状态代码 `Error 503 Server unavailable` 或在默认页面中看到 `Hey, Ruby developers!`。 可能需要花费几分钟时间才能完全重启应用。
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：Ruby on Rails 与 Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [配置 Ruby 应用](configure-language-ruby.md)
