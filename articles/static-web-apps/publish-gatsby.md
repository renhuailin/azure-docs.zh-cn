---
title: 教程：将 Gatsby 站点发布到 Azure 静态 Web 应用
description: 本教程介绍如何将 Gatsby 应用程序部署到 Azure 静态 Web 应用。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4c6a68b8db40aa07c251cabab28217143105aab1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814507"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps"></a>教程：将 Gatsby 站点发布到 Azure 静态 Web 应用

本文演示如何创建 [Gatsby](https://gatsbyjs.org) Web 应用程序并将其部署到 [Azure 静态 Web 应用](overview.md)。 最终结果是一个新的静态 Web 应用站点（其中包含关联的 GitHub 操作），使你可以控制如何生成和发布应用。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建 Gatsby 应用
> - 设置 Azure 静态 Web 应用站点
> - 将 Gatsby 应用部署到 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 如果没有帐户，可以[免费创建一个帐户](https://github.com/join)。
- 安装 [Node.js](https://nodejs.org)。

## <a name="create-a-gatsby-app"></a>创建 Gatsby 应用

使用 Gatsby 命令行接口 (CLI) 创建 Gatsby 应用：

1. 打开终端
1. 使用 [npx](https://www.npmjs.com/package/npx) 工具通过 Gatsby CLI 创建新应用。 这可能需要几分钟的时间。

   ```bash
   npx gatsby new static-web-app
   ```

1. 导航到新建的应用

   ```bash
   cd static-web-app
   ```

1. 初始化 Git 存储库

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>将应用程序推送到 GitHub

你需要具有 GitHub 上的存储库来创建新的 Azure 静态 Web 应用资源。

1. 从名为 gatsby-static-web-app 的 [https://github.com/new](https://github.com/new) 创建空白 GitHub 存储库（不创建自述文件）。

1. 接下来，将刚创建的 GitHub 存储库作为远程存储库添加到本地存储库。 确保添加 GitHub 用户名，来替代下面命令中的 `<YOUR_USER_NAME>` 占位符。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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
    | _资源组_ | **my-gatsby-group**  |
    | _名称_ | **my-gatsby-app** |
    | _计划类型_ | **免费** |
    | Azure Functions API 和过渡环境的区域 | 选择离你最近的区域。 |
    | _Source_ | **GitHub** |

1. 选择“使用 GitHub 登录”，然后向 GitHub 进行身份验证。

1. 输入以下 GitHub 值。

    | 属性 | 值 |
    | --- | --- |
    | 组织 | 选择所需的 GitHub 组织。 |
    | 存储库 | 选择“gatsby-static-web-app”。 |
    | 分支 | 选择“主要”。 |

1. 在“生成详细信息”部分，从“生成预设”下拉列表中选择“Gatsby”，并保留默认值 。

### <a name="review-and-create"></a>查看并创建

1. 选择“查看 + 创建”按钮，验证详细信息是否全部正确。

1. 选择“创建”，开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，单击“转到资源”。

1. 在资源屏幕中，单击 URL 链接，打开已部署的应用程序。 可能需要等待一两分钟，GitHub 操作才能完成。

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="已部署的应用程序":::

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加自定义域](custom-domain.md)
