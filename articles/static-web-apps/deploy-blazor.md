---
title: 教程：在 Azure Static Web Apps 中使用 Blazor 生成静态 Web 应用
description: 了解如何使用 Blazor 生成 Azure Static Web Apps 网站。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 90c044593ac02f2c906fb2347d731168b25af5af
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109813859"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>教程：在 Azure Static Web Apps 中使用 Blazor 生成静态 Web 应用

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此教程中，使用 Azure 门户将 Web 应用程序部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户

## <a name="application-overview"></a>应用概览

通过 Azure Static Web Apps，你可以创建无服务器后端支持的静态 Web 应用程序。 以下教程演示了如何部署返回天气数据的 C# Blazor Web 应用程序。

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="完成 Blazor 应用":::

本教程中介绍的应用由三个不同的 Visual Studio 项目组成：

- Api：C# Azure Functions 应用程序，该应用程序实现向静态应用提供天气信息的 API 终结点。 WeatherForecastFunction 返回 `WeatherForecast` 对象的数组。

- **客户端**：前端 Blazor Web 程序集项目。 已实现[回退路由](#fallback-route)，以确保为所有路由提供 index.html 文件。

- 共享：保存 Api 和客户端项目引用的公共类，这是数据可从 API 终结点流向前端 Web 应用。 [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) 类在两个应用之间共享。

这些项目共同构成了创建 Blazor Web 程序集应用程序的所需部件，该应用程序在 API 后端支持的浏览器中运行。

## <a name="fallback-route"></a>回退路由

应用程序公开诸如 /counter 和 /fetchdata 这样的 URL，它们映射到应用程序的特定路由 。 由于此应用是作为单页应用程序实现的，因此为所有路由提供了 index.html 文件。 为了确保对于任何路径的请求均返回 index.html，可在客户端项目的“wwwroot”文件夹中找到 staticwebapp.config.json 文件，在该文件中实现[回退路由](./configuration.md#fallback-routes)  。

```json
{
  "navigationFallback": {
    "rewrite": "/index.html"
  }
}
```

上述配置确保对应用中任何路由的请求都将返回 index.html 页。

## <a name="create-a-repository"></a>创建存储库

本文使用 GitHub 模板存储库，使你能够轻松入门。 该模板具有一个部署到 Azure Static Web Apps 的入门应用。

1. 请确保已登录到 GitHub，然后导航到以下位置以创建新存储库：
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. 将存储库命名为 my-first-static-blazor-app。

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure 门户创建静态 Web 应用。

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 选择“创建资源”。
1. 搜索“静态 Web 应用”。
1. 选择“静态 Web 应用”。
1. 选择“创建”。
1. 在“基本信息”选项卡上，输入下列值。

    | 属性 | 值 |
    | --- | --- |
    | _订阅_ | Azure 订阅名称。 |
    | _资源组_ | **my-blazor-group**  |
    | _名称_ | **my-first-static-blazor-app** |
    | _计划类型_ | **免费** |
    | _Azure Functions API 和过渡环境的区域_ | 选择离你最近的区域。 |
    | _Source_ | **GitHub** |

1. 选择“使用 GitHub 登录”，然后向 GitHub 进行身份验证。

1. 输入以下 GitHub 值。

    | 属性 | 值 |
    | --- | --- |
    | 组织 | 选择所需的 GitHub 组织。 |
    | 存储库 | 选择“my-first-static-blazor-app”。 |
    | 分支 | 选择“主要”。 |

1. 在“生成详细信息”部分，从“生成预设”下拉列表中选择“Blazor”，并保留默认值 。

### <a name="review-and-create"></a>查看并创建

1. 选择“查看 + 创建”按钮以验证详细信息是否都正确。

1. 选择“创建”以开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，单击“转到资源”。

1. 选择“转到资源”。

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="“转到资源”按钮":::

## <a name="view-the-website"></a>查看网站

通过两个方面来部署静态应用。 第一个预配构成应用的基础 Azure 资源。 第二个是生成和发布应用程序的 GitHub Actions 工作流。

在导航到新静态站点之前，必须先完成部署生成的运行。

静态 Web 应用“概述”窗口显示了一系列链接，可帮助你与 Web 应用进行交互。

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="“概述”窗口":::

1. 单击“单击此处以查看 GitHub Actions 运行的状态”的标题，即可转到针对存储库运行的 GitHub Actions。 确认部署作业完成后，即可通过生成的 URL 导航到网站。

2. GitHub Actions 工作流完成后，可以选择该 URL 链接以在新选项卡中打开网站。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在顶部搜索栏中搜索“my-blazor-group”。
1. 选择组名称。
1. 选择“删除”按钮。
1. 选择“是”以确认删除操作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [身份验证和授权](./authentication-authorization.md)
