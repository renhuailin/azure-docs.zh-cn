---
title: 快速入门：使用 Azure Static Web Apps 生成第一个静态站点
description: 了解如何将静态站点部署到 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449079"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>快速入门：使用 Azure Static Web Apps 生成第一个静态站点

Azure Static Web Apps 通过从代码存储库生成应用程序发布网站。 在本快速入门中，你可以使用 Visual Studio Code 扩展将应用程序部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 的 Azure Static Web Apps 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [安装 Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下来，打开 Visual Studio Code 并转到“文件”>“打开文件夹”以在编辑器中打开刚刚克隆到计算机上的存储库。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

1. 在 Visual Studio Code 中，选择活动栏中的 Azure 徽标以打开 Azure 扩展窗口。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 徽标":::

    > [!NOTE]
    > 需要登录 Azure 和 GitHub。 如果尚未从 Visual Studio Code 登录到 Azure 和 GitHub，该扩展将在创建过程中提示你登录到这两个站点。

1. 在“Static Web Apps”标签下，选择“加号”。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="应用程序名称":::

1. 命令面板将在编辑器顶部打开，并提示你命名应用程序。

    输入“my-first-static-web-app”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="创建静态 Web 应用":::

1. 选择与应用程序类型匹配的预设。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="应用程序预设：无框架":::

    输入“./”作为应用程序文件的位置

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="应用程序文件位置":::

    选择“立即跳过”作为 Azure Functions API 的位置

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API 位置":::

    输入“./”作为生成输出位置

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="应用程序生成输出位置":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="应用程序预设：Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="应用程序预设：React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="应用程序预设：Vue":::

    ---

1. 选择离你最近的位置，然后按 Enter。

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="资源位置":::

1. 创建应用后，将在 Visual Studio Code 中显示确认通知。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="已创建确认":::

    接下来，单击 GitHub 中的“打开操作”按钮。 此页面将显示应用程序的生成状态。

    GitHub 操作完成后，即可浏览到已发布的网站。

1. 若要在浏览器中查看网站，请在 Static Web Apps 扩展中右键单击该项目，然后选择“浏览网站”。

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="浏览站点":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可通过该扩展删除 Azure Static Web Apps 实例。

在 Visual Studio Code 资源浏览器窗口中，返回到“Static Web Apps”部分，右键单击“my-first-static-web-app”，然后选择“删除” 。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="删除应用":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
