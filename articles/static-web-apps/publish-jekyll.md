---
title: 教程：将 Jekyll 网站发布到 Azure 静态 Web 应用
description: 了解如何将 Jekyll 应用程序部署到 Azure 静态 Web 应用。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: cshoe
ms.openlocfilehash: fa97100e670e1d96bdd33c362b2e133d78d8dae7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155432"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps"></a>教程：将 Jekyll 网站发布到 Azure 静态 Web 应用

本文演示如何创建 [Jekyll](https://jekyllrb.com/) Web 应用程序并将其部署到 [Azure 静态 Web 应用](overview.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建 Jekyll 网站
> - 设置 Azure 静态 Web 应用资源
> - 将 Jekyll 应用部署到 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 安装 [Jekyll](https://jekyllrb.com/docs/installation/)
  - 如有必要，可使用适用于 Linux 的 Windows 子系统并按照 Ubuntu 说明操作。
- 具有活动订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 如果没有帐户，可以[免费创建一个帐户](https://github.com/join)。

## <a name="create-jekyll-app"></a>创建 Jekyll 应用

使用 Jekyll 命令行接口 (CLI) 创建 Jekyll 应用：

1. 从终端运行 Jekyll CLI 来创建一个新的应用。

   ```bash
   jekyll new static-app
   ```

1. 导航到新建的应用。

   ```bash
   cd static-app
   ```

1. 初始化新的 Git 存储库。

   ```bash
    git init
   ```

1. 提交更改。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>将应用程序推送到 GitHub

Azure 静态 Web 应用使用 GitHub 来发布你的网站。 以下步骤展示了如何创建 GitHub 存储库。

1. 根据名为 jekyll-azure-static 的 [https://github.com/new](https://github.com/new) 创建空白 GitHub 存储库（不创建自述文件）。

1. 将 GitHub 存储库作为远程存储库添加到本地存储库。 确保添加 GitHub 用户名，来替代下面命令中的 `<YOUR_USER_NAME>` 占位符。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-azure-static
   ```

1. 将本地存储库推送到 GitHub。

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > 你的 git 分支的命名方式可能与 `main` 不同。 用正确的值代替此命令中的 `main`。

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
    | _资源组_ | jekyll-static-app  |
    | _名称_ | jekyll-static-app |
    | _计划类型_ | **免费** |
    | Azure Functions API 和过渡环境的区域 | 选择离你最近的区域。 |
    | _Source_ | **GitHub** |

1. 选择“使用 GitHub 登录”，然后向 GitHub 进行身份验证。

1. 输入以下 GitHub 值。

    | 属性 | 值 |
    | --- | --- |
    | 组织 | 选择所需的 GitHub 组织。 |
    | 存储库 | 选择“jekyll-static-app”。 |
    | 分支 | 选择“主要”。 |

1. 在“生成详细信息”部分，从“生成预设”下拉列表中选择“自定义”，并保留默认值。

1. 在“应用位置”框中输入“./”。

1. 将“API 位置”框留空。

1. 在“输出位置”框中，输入“_site”。

### <a name="review-and-create"></a>查看并创建

1. 选择“查看 + 创建”按钮，验证详细信息是否全部正确。

1. 选择“创建”，开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，单击“转到资源”。

1. 在资源屏幕中，单击 URL 链接，打开已部署的应用程序。 可能需要等待一两分钟，GitHub 操作才能完成。

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="已部署的应用程序":::

#### <a name="custom-jekyll-settings"></a>自定义 Jekyll 设置

生成静态 Web 应用时，将生成一个[工作流文件](./build-configuration.md)，其中包含应用程序的发布配置设置。

若要配置环境变量（如 `JEKYLL_ENV`），请将 `env` 部分添加到工作流中的 Azure 静态 Web 应用 GitHub 操作。

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v1
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加自定义域](custom-domain.md)
