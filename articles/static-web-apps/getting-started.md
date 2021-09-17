---
title: 快速入门：使用 Azure Static Web Apps 生成第一个静态站点
description: 了解如何将静态站点部署到 Azure Static Web Apps。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 06/16/2021
ms.author: cshoe
ms.openlocfilehash: b543831ceead6d386de0617b02db93a99d9507a6
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609124"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>快速入门：使用 Azure Static Web Apps 生成第一个静态站点

Azure Static Web Apps 通过从代码存储库生成应用程序来发布网站。 在本快速入门中，你将使用 Visual Studio Code 扩展将应用程序部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 的 Azure Static Web Apps 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [安装 Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

接下来，打开 Visual Studio Code 并转到“文件”>“打开文件夹”以在编辑器中打开已克隆的存储库。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

1. 在 Visual Studio Code 中，选择活动栏中的 Azure 徽标以打开 Azure 扩展窗口。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 徽标":::

    > [!NOTE]
    > 需要在 Visual Studio Code 中登录到 Azure 和 GitHub，才能继续操作。 如果你还没有进行身份验证，在创建过程中，扩展将提示你登录到这两个服务。

1. 在“Static Web Apps”标签下，选择“加号”。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="应用程序名称":::
    
    > [!NOTE]
    > Azure Static Web Apps Visual Studio Code 扩展通过使用一系列默认值简化了创建过程。 如果要对创建过程进行精细控制，请打开命令面板，然后选择“Azure Static Web Apps: 创建静态 Web 应用…(高级)”。

1. 命令面板会在编辑器顶部打开，并提示你选择订阅名称。

    选择你的订阅并按 <kbd>Enter</kbd>。

    :::image type="content" source="media/getting-started/extension-subscription.png" alt-text="选择 Azure 订阅":::

1. 接下来，为你的应用程序命名。

    输入“my-first-static-web-app”，然后按 Enter 。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="创建静态 Web 应用":::

1. 选择与应用程序类型匹配的预设。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="应用程序预设：无框架":::

    输入 ./src 作为应用程序文件的位置，并按 <kbd>Enter</kbd>。

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="应用程序生成输出位置":::

    输入 ./src 作为生成输出位置，并按 <kbd>Enter</kbd>。

    # <a name="angular"></a>[Angular](#tab/angular)

    尽管预设了 Angular，但请选择“自定义”选项，以便为此应用程序提供相应的输出位置。

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="应用程序预设：Angular":::

    输入“./”作为应用程序文件的位置。

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="应用程序文件位置：Angular":::

    输入 dist/angular-basic 作为生成输出位置。

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="应用程序生成输出位置：Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="应用程序预设：React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="应用程序预设：Vue":::

    ---

1. 创建应用后，将在 Visual Studio Code 中显示确认通知。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="已创建确认":::

    在部署过程中，Visual Studio Code 扩展会向你报告生成状态。

    :::image type="content" source="media/getting-started/extension-waiting-for-deployment.png" alt-text="等待部署":::

    部署完成后，可以直接导航到网页。

1. 若要在浏览器中查看网站，请在 Static Web Apps 扩展中右键单击该项目，然后选择“浏览网站”。

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="浏览站点":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可通过该扩展删除 Azure Static Web Apps 实例。

在 Visual Studio Code 资源浏览器窗口中，返回到“Static Web Apps”部分，右键单击“my-first-static-web-app”，然后选择“删除” 。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="删除应用":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
