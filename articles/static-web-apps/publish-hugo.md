---
title: 教程：将 Hugo 站点发布到 Azure 静态 Web 应用
description: 了解如何将 Hugo 应用程序部署到 Azure 静态 Web 应用。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.openlocfilehash: cc117859c911d9b2c1df6c03fc6cd9738e5de1ec
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988295"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps"></a>教程：将 Hugo 站点发布到 Azure 静态 Web 应用

本文演示如何创建 [Hugo](https://gohugo.io/) Web 应用程序并将其部署到 [Azure 静态 Web 应用](overview.md)。 最终结果是一个全新的 Azure 静态 Web 应用，其中包含关联的 GitHub Actions，使你可以控制生成和发布应用的方式。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建 Hugo 应用
> - 设置 Azure 静态 Web 应用
> - 将 Hugo 应用部署到 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 如果没有帐户，可以[免费创建一个帐户](https://github.com/join)。

## <a name="create-a-hugo-app"></a>创建 Hugo 应用

使用 Hugo 命令行接口 (CLI) 创建 Hugo 应用：

1. 有关 OS 上的 Hugo，请参阅[安装指南](https://gohugo.io/getting-started/installing/)。

1. 打开终端

1. 运行 Hugo CLI，创建新应用。

   ```bash
   hugo new site static-app
   ```

1. 导航到新建的应用。

   ```bash
   cd static-app
   ```

1. 初始化 Git 存储库。

   ```bash
   git init
   ```

1. 请确保分支名为 `main`。

   ```bash
   git branch -M main
   ```

1. 接下来，通过将主题安装为 Git 子模块并在 Hugo 配置文件中指定主题，将主题添加到站点。

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 提交更改。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>将应用程序推送到 GitHub

你需要 GitHub 上的存储库以连接到 Azure 静态 Web 应用。 以下步骤展示了如何创建站点的存储库。

1. 从名为 hugo-static-app 的 [https://github.com/new](https://github.com/new) 创建空白 GitHub 存储库（不创建自述文件）。

1. 将 GitHub 存储库作为远程存储库添加到本地存储库。 确保添加 GitHub 用户名，来替代下面命令中的 `<YOUR_USER_NAME>` 占位符。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. 将本地存储库推送到 GitHub。

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>部署 Web 应用

下列步骤说明了如何创建新的静态站点应用并将其部署到生产环境。

### <a name="create-the-application"></a>创建应用程序

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 选择“创建资源”
1. 搜索“静态 Web 应用”
1. 选择“Static Web Apps”
1. 选择“创建”
1. 在“基本信息”选项卡上，输入下列值。

    | 属性 | 值 |
    | --- | --- |
    | _订阅_ | Azure 订阅名称。 |
    | _资源组_ | my-hugo-group  |
    | _名称_ | hugo-static-app |
    | _计划类型_ | **免费** |
    | Azure Functions API 和过渡环境的区域 | 选择离你最近的区域。 |
    | _Source_ | **GitHub** |

1. 选择“使用 GitHub 登录”，然后向 GitHub 进行身份验证。

1. 输入以下 GitHub 值。

    | 属性 | 值 |
    | --- | --- |
    | 组织 | 选择所需的 GitHub 组织。 |
    | 存储库 | 选择“hugo-static-app”。 |
    | 分支 | 选择“主要”。 |

1. 在“生成详细信息”部分，从“生成预设”下拉列表中选择“Hugo”，并保留默认值。

### <a name="review-and-create"></a>查看并创建

1. 选择“查看 + 创建”按钮，验证详细信息是否全部正确。

1. 选择“创建”，开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，单击“转到资源”。

1. 在资源屏幕中，单击 URL 链接，打开已部署的应用程序。 可能需要等待一两分钟，GitHub 操作才能完成。

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="已部署的应用程序":::

#### <a name="custom-hugo-version"></a>自定义 Hugo 版本

生成静态 Web 应用时，将生成一个[工作流文件](./github-actions-workflow.md)，其中包含应用程序的发布配置设置。 可通过在 `env` 部分中输入 `HUGO_VERSION` 的值，在工作流文件中指定特定的 Hugo 版本。 以下示例配置演示了如何将 Hugo 设置为特定版本。

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加自定义域](custom-domain.md)
