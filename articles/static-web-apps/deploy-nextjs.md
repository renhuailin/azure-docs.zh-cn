---
title: 教程：在 Azure Static Web Apps 中部署静态呈现的 Next.js 网站
description: 使用 Azure 静态 Web 应用生成并部署 Next.js 动态站点。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455899"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>在 Azure Static Web Apps 中部署静态呈现的 Next.js 网站

此教程介绍如何将 [Next.js](https://nextjs.org) 生成的静态网站部署到 [Azure 静态 Web 应用](overview.md)。 有关 Next.js 细节的详细信息，请参阅[初学者模板的自述文件](https://github.com/staticwebdev/nextjs-starter#readme)。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 [免费创建帐户](https://github.com/join)。
- 安装 [Node.js](https://nodejs.org)。

## <a name="set-up-a-nextjs-app"></a>安装 Next.js 应用

无需使用 Next.js CLI 创建应用，可以使用初学者存储库。 初学者存储库包含支持动态路由的现有 Next.js 应用程序。

首先通过 GitHub 帐户从模板存储库创建新的存储库。

1. 导航到 [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)
1. 将存储库命名为 nextjs-starter
1. 接下来，将新存储库克隆到计算机。 确保将 `<YOUR_GITHUB_ACCOUNT_NAME>` 替换为你的帐户名称。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. 导航到新克隆的 Next.js 应用：

   ```bash
   cd nextjs-starter
   ```

1. 安装依赖项：

    ```bash
    npm install
    ```

1. 在开发环境中启动 Next.js 应用：

    ```bash
    npm run dev
    ```

导航到 `http://localhost:3000`，打开应用，应看到从你首选的浏览器中打开了下面的网站：

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="启动 Next.js 应用":::

选择框架或库时，会看到已选定项的详细信息页：

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="详细信息页":::

## <a name="deploy-your-static-website"></a>部署静态网站

以下步骤演示如何将应用链接到 Azure 静态 Web 应用。 在 Azure 中，可以将应用程序部署到生产环境。

### <a name="create-a-static-app"></a>创建静态应用

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”。
1. 搜索“静态 Web 应用”。
1. 选择“静态 Web 应用”。
1. 选择“创建”。
1. 在“基本信息”选项卡上，输入下列值。

    | 属性 | 值 |
    | --- | --- |
    | _订阅_ | Azure 订阅名称。 |
    | _资源组_ | **my-nextjs-group**  |
    | _名称_ | **my-nextjs-app** |
    | _计划类型_ | **免费** |
    | Azure Functions API 和过渡环境的区域 | 选择离你最近的区域。 |
    | _Source_ | **GitHub** |

1. 选择“使用 GitHub 登录”，然后向 GitHub 进行身份验证。

1. 输入以下 GitHub 值。

    | 属性 | 值 |
    | --- | --- |
    | 组织 | 选择相应的 GitHub 组织。 |
    | 存储库 | 选择 nextjs-starter。 |
    | 分支 | 选择“主要”。 |

1. 在“生成详细信息”部分，从“生成预设”下拉中选择“自定义”。 添加以下值用于生成配置。

    | 属性 | 值 |
    | --- | --- |
    | 应用位置 | 在框中输入 /。 |
    | API 位置 | 将此框留空。 |
    | 输出位置 | 在框中输入 out。 |

### <a name="review-and-create"></a>查看并创建

1. 选择“查看 + 创建”按钮，验证详细信息是否全部正确。

1. 选择“创建”，开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，请选择“转到资源”。

1. 在“概述”窗口中，选择 URL 链接，打开已部署的应用程序。

如果网站未立即加载，则生成仍在运行。 工作流完成后，可以刷新浏览器以查看 Web 应用。

若要检查 Actions 工作流的状态，请导航到存储库的 Actions 仪表板：

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

现在，对 `main` 分支进行的任何更改都会启动新的网站生成和部署。

### <a name="sync-changes"></a>同步更改

创建应用后，Azure 静态 Web 应用在存储库中创建了 GitHub Actions 文件。 通过将最新内容拉取到本地存储库，与服务器同步。

返回终端并运行以下命令：`git pull origin main`。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在顶部搜索栏中搜索“my-nextjs-group”。
1. 选择组名称。
1. 选择“删除”按钮。
1. 选择“是”以确认删除操作。

> [!div class="nextstepaction"]
> [设置自定义域](custom-domain.md)
