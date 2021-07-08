---
title: 什么是 Azure 静态 Web 应用？
description: Azure 静态 Web 应用的主要特性和功能。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 5a335525bef3509f547bbccc478ec9402ad05e28
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815047"
---
# <a name="what-is-azure-static-web-apps"></a>什么是 Azure 静态 Web 应用？

Azure Static Web Apps 是一种服务，可从代码存储库自动生成完整的堆栈 Web 应用，并将其部署到 Azure。

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps 概述图":::

Azure 静态 Web 应用的工作流适用于开发人员的日常工作流。 基于代码更改生成并部署应用。

当你创建 Azure Static Web Apps 资源时，Azure 会直接与 GitHub 或 Azure DevOps 交互以监视你选择的分支。 每次你向受监视的分支推送提交或接受拉取请求时，系统都会自动运行一次生成，并将你的应用和 API 部署到 Azure。

通常使用不需要服务器端渲染的库和框架（例如，Angular、React、Svelte、Vue 或 Blazor）来生成静态 Web 应用。 这些应用包括构成应用程序的 HTML、CSS、JavaScript 和映像资产。 对于传统 Web 服务器，这些资产与任何所需的 API 终结点一起由单个服务器提供。

使用静态 Web 应用时，静态资产与传统 Web 服务器分离，由分布在世界各地的服务器端提供。 由于文件在物理上离最终用户更近，这种分布使文件提供的速度更快。 此外，API 终结点使用[无服务器体系结构](../azure-functions/functions-overview.md)，无需将完整的后端服务器组合在一起。

## <a name="key-features"></a>主要功能

- 适用于 HTML、CSS、JavaScript 和映像等静态内容的 Web 托管。
- Azure Functions 提供的集成 API 支持，可选择使用标准帐户链接现有的 Azure Functions 应用程序。
- 一流的 GitHub 和 Azure DevOps 集成，其中的存储库更改会触发生成和部署。
- 全球分布的静态内容，使内容更接近你的用户。
- 可自动续订的免费 SSL 证书。
- 自定义域为应用提供品牌自定义。
- 调用 API 时使用反向代理的无缝安全模型，这不需要配置 CORS。
- 身份验证提供程序与 Azure Active Directory、GitHub 和 Twitter 集成。
- 可自定义的授权角色定义和分配。
- 后端路由规则，使你能够完全控制所提供的内容和路由。
- 生成的临时版本由拉取请求提供支持，在发布前提供站点的预览版本。

## <a name="what-you-can-do-with-static-web-apps"></a>可对静态 Web 应用执行的操作

- 使用 [Angular](getting-started.md?tabs=angular)、[React](getting-started.md?tabs=react)、[Svelte](/learn/modules/publish-app-service-static-web-app-api/)、[Vue](getting-started.md?tabs=vue) 等 JavaScript 框架和库构建新式 Web 应用程序，或使用带 [Azure Functions](apis.md) 后端的 [Blazor](./deploy-blazor.md) 创建 WebAssembly 应用程序。
- 使用 [Gatsby](publish-gatsby.md)、[Hugo](publish-hugo.md)、[VuePress](publish-vuepress.md) 等框架发布静态站点。
- 使用 [Next.js](deploy-nextjs.md) 和 [Nuxt.js](deploy-nuxtjs.md) 等框架部署 Web 应用程序。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建第一个静态应用](getting-started.md)
