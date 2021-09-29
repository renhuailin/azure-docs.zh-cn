---
title: 快速入门：在 Azure 门户中使用 Azure Static Web Apps 构建你的第一个静态 Web 应用
description: 了解如何使用 Azure 门户将静态站点部署到 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 05/07/2021
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 41be62a4e7baed39cc45011ed055a1abfe097002
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804592"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>快速入门：在 Azure 门户中构建你的第一个静态站点

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此快速入门中，你会使用 Azure 门户将 Web 应用部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure 门户创建静态 Web 应用。

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”。
1. 搜索“静态 Web 应用”。
1. 选择“静态 Web 应用”。
1. 选择“创建”。

在“基本信息”部分中，首先配置新应用，并将其链接到 GitHub 存储库。

:::image type="content" source="media/getting-started-portal/quickstart-portal-basics.png" alt-text="“基本信息”部分":::

1. 选择 _Azure 订阅_。
1. 在“资源组”旁边，选择“新建”链接。
1. 在文本框中输入“static-web-apps-test”。
1. 在“静态 Web 应用详细信息”下的文本框中，输入“my-first-static-web-app”。
1. 在“Azure Functions 和过渡详细信息”下，选择离你最近的区域。
1. 在“部署详细信息”下，选择“GitHub”。
1. 选择“使用 GitHub 登录”按钮，然后向 GitHub 进行身份验证。

登录 GitHub 后，输入存储库信息。

:::image type="content" source="media/getting-started-portal/quickstart-portal-source-control.png" alt-text="存储库详细信息":::

1. 选择首选的组织名称。
1. 在“存储库”下拉列表中选择“my-first-web-static-app”。
1. 在“分支”下拉列表中选择“main”。

   > [!NOTE]
   > 如果看不到任何存储库，则可能需要在 GitHub 中授权 Azure Static Web Apps。 浏览到 GitHub 存储库，转到“设置”>“应用程序”>“授权 OAuth 应用”，选择“Azure Static Web Apps”，然后选择“授予”。 对于组织存储库，你必须是组织的所有者才能授予权限。

1. 在“生成详细信息”部分中，添加特定于首选前端框架的配置详细信息。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    1. 在“生成预设”下拉列表中选择“自定义”。
    1. 在“应用位置”框中键入 ./src。
    1. 将“API 位置”框留空。
    1. 在“应用项目位置”框中键入 ./src。

    # <a name="angular"></a>[Angular](#tab/angular)

    1. 从“生成预设”下拉列表中选择“Angular”。
    1. 保留“应用位置”框中的默认值。
    1. 将“API 位置”框留空。
    1. 在“应用项目位置”框中键入 dist/angular-basic。

    # <a name="react"></a>[React](#tab/react)

    1. 从“生成预设”下拉列表中选择“React”。
    1. 保留“应用位置”框中的默认值。
    1. 将“API 位置”框留空。
    1. 在“应用项目位置”框中键入 build。

    # <a name="vue"></a>[Vue](#tab/vue)

    1. 从“生成预设”下拉列表中选择“Vue.js”。
    1. 保留“应用位置”框中的默认值。
    1. 将“API 位置”框留空。
    1. 保留“应用项目位置”框中的默认值。

    ---

1. 选择“查看 + 创建”。

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="“查看创建”按钮":::

    > [!NOTE]
    > 若要在创建应用后更改这些值，可编辑[工作流文件](build-configuration.md)。

1. 选择“创建”。

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="“创建”按钮":::

1. 选择“转到资源”。

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="“转到资源”按钮":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在顶部搜索栏中搜索“my-first-web-static-app”。
1. 选择应用名称。
1. 选择“删除”按钮。
1. 选择“是”以确认删除操作（此操作的完成可能需要一些时间）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
