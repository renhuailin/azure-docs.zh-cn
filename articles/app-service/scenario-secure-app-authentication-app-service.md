---
title: 教程 - 向 Azure 应用服务上的 Web 应用添加身份验证 | Azure
description: 本教程介绍如何为 Azure 应用服务上运行的 Web 应用启用身份验证和授权。 仅限组织中的用户访问 Web 应用。
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 06/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: fdf94512de6c9cde5078f3c1d64154d62c301ef7
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004495"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>教程：向 Azure 应用服务上运行的 Web 应用添加身份验证

了解如何为 Azure 应用服务上运行的 Web 应用启用身份验证，并仅限组织中的用户访问应用。

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="显示用户登录的示意图。" border="false":::

应用服务提供内置的身份验证和授权支持。只需在 Web 应用中编写少量的代码或根本无需编写代码，就能让用户登录和访问数据。 不要求使用应用服务身份验证/授权模块，但使用该模块有助于简化应用的身份验证和授权。 本文介绍如何将 Azure Active Directory (Azure AD) 用作标识提供者，使用应用服务身份验证/授权模块保护 Web 应用。

身份验证/授权模块通过 Azure 门户和应用设置进行启用和配置。 不需要任何 SDK、特定语言，或者对应用程序代码进行更改。 支持多种标识提供者，包括 Azure AD、Microsoft 帐户、Facebook、Google 和 Twitter。 启用身份验证/授权模块后，每个传入的 HTTP 请求将通过此模块，然后由应用代码处理。 若要了解详细信息，请参阅 [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 为 Web 应用配置身份验证。
> * 仅限组织中的用户访问 Web 应用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>在应用服务上创建并发布一个 Web 应用

对于本教程，你需要一个部署到应用服务的 Web 应用。 可使用现有的 Web 应用，也可按照 [ASP.NET Core 快速入门](quickstart-dotnetcore.md)创建新的 Web 应用并将其发布到应用服务。

无论是使用现有的 Web 应用还是创建新的 Web 应用，请记下 Web 应用名称以及 Web 应用部署到的资源组的名称。 在本教程中，需要用到这些名称。 

## <a name="configure-authentication-and-authorization"></a>配置身份验证和授权

现在，你有一个在应用服务上运行的 Web 应用。 接下来将为 Web 应用启用身份验证和授权。 请将 Azure AD 用作标识提供者。 有关详细信息，请参阅[为应用服务应用程序配置 Azure AD 身份验证](configure-authentication-provider-aad.md)。

在 [Azure 门户](https://portal.azure.com)菜单上，选择“资源组”，或在任意页面中搜索并选择“资源组” 。

在“资源组”中，查找并选择资源组。 在“概述”中，选择应用的管理页。

:::image type="content" alt-text="显示如何选择应用的管理页面的屏幕截图。" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

在应用程序的左侧菜单中，选择“身份验证”，然后单击“添加标识提供者”。

在“添加标识提供者”页上，选择“Microsoft”作为“标识提供者”以登录 Microsoft 和 Azure AD 标识。

对于“应用程序注册” > “应用程序注册类型”，请选择“新建应用程序注册”。

对于“应用程序注册” > “支持的帐户类型”，请选择“当前租户-单租户”。

在“应用服务身份验证设置”部分中，将“身份验证”设置为“要求身份验证”，并将“未验证请求”设置为“HTTP 302 Found 重定向：建议网站选用”。

在“添加标识提供者”页的底部，单击“添加”为 Web 应用启用身份验证。

:::image type="content" alt-text="显示配置身份验证的屏幕截图。" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

现在，你有一个受应用服务身份验证和授权保护的应用。

> [!NOTE]
> 若要允许来自其他租户的帐户，请从“身份验证”边栏选项卡编辑“标识提供者”，将“颁发者 URL”更改为“https://login.microsoftonline.com/common/v2.0”。
>

## <a name="verify-limited-access-to-the-web-app"></a>验证对 Web 应用的受限访问

启用应用服务身份验证/授权模块后，系统会在 Azure AD 租户中创建应用注册。 应用注册的显示名称与 Web 应用的相同。 若要检查设置，请从门户菜单中选择“Azure Active Directory”，然后选择“应用注册” 。 选择已创建的应用注册。 在“概述”中，验证“支持的帐户类型”是否设置为“仅限我的组织” 。

:::image type="content" alt-text="显示如何验证访问权限的屏幕截图。" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

若要验证是否仅限组织中的用户访问应用，请在隐匿或私密模式下启动浏览器，然后转到 `https://<app-name>.azurewebsites.net`。 系统应该会将你定向到安全的登录页面，以验证未经身份验证的用户是否无法访问该站点。 以组织中用户的身份登录，获取对站点的访问权限。 你还可以启动新的浏览器并尝试使用个人帐户登录，以验证组织外部的用户是否没有访问权限。

## <a name="clean-up-resources"></a>清理资源

如果已完成本教程，并且不再需要 Web 应用或相关资源，请[清理创建的资源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 为 Web 应用配置身份验证。
> * 仅限组织中的用户访问 Web 应用。

> [!div class="nextstepaction"]
> [应用服务访问存储](scenario-secure-app-access-storage.md)
