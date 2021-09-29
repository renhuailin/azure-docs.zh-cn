---
title: 教程：注册应用程序
titleSuffix: Azure AD B2C
description: 按照此教程进行操作，了解如何使用 Azure 门户在 Azure Active Directory B2C 中注册 Web 应用程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913393b36ec91b1db576a39711deeddca0f98258
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581614"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册 Web 应用

必须在你管理的租户中注册应用程序，然后这些[应用程序](application-types.md)才能与 Azure Active Directory B2C (Azure AD B2C) 交互。 本教程介绍如何使用 Azure 门户注册 Web 应用程序。 

“Web 应用”是指在服务器上执行大多数应用程序逻辑的传统 Web 应用。 它们可能是使用 ASP.NET Core、Maven (Java)、Flask (Python) 和 Express (Node.js) 等框架生成的。

> [!IMPORTANT]
> 如果改用单页应用程序（“SPA”）（例如，使用 Angular、Vue 或 React），请了解[如何注册单页应用程序](tutorial-register-spa.md)。
> 
> 但如果你使用的是本机应用（例如 iOS、Android、移动和桌面），请参阅[如何注册本机客户端应用程序](add-native-application.md)。

## <a name="prerequisites"></a>先决条件
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有的 Azure AD B2C 租户。

## <a name="register-a-web-application"></a>注册 Web 应用程序

要在 Azure AD B2C 租户中注册 Web 应用程序，可以使用新的统一“应用注册”体验或旧版“应用程序(旧版)”体验 。 [详细了解此新体验](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，“webapp1”。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。
1. 在“重定向 URI”下，选择“Web”，然后在 URL 文本框中输入 `https://jwt.ms` 。

    “重定向 URI”是授权服务器（在本例中为 Azure AD B2C）在完成与用户的交互后将用户定向到的终结点，也是在成功授权后向其发送访问令牌或授权代码的终结点。 在生产应用程序中，它通常是运行应用的公共可访问终结点，如 `https://contoso.com/auth-response`。 出于类似本教程的测试目的，你可以将它设置为 `https://jwt.ms`，这是一个 Microsoft 拥有的 Web 应用程序，用于显示已解码的令牌内容（令牌内容始终保留在浏览器中）。 在应用开发期间，你可以添加应用程序本地侦听的终结点，如 `https://localhost:5000`。 可以随时在注册的应用程序中添加和修改重定向 URI。

    重定向 URI 存在以下限制：

    * 回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用程序(旧版)”，然后选择“添加” 。
1. 输入应用程序的名称。 例如，“webapp1”。
1. 对于“包括 Web 应用/Web API”，请选择“是”。 
1. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 例如，可将其设置为在 `http://localhost:5000` 上本地侦听。 可以随时在注册的应用程序中添加和修改重定向 URI。

    重定向 URI 存在以下限制：

    * 除非使用 `localhost`，否则回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

1. 选择“创建”，完成应用程序注册。

* * *

## <a name="create-a-client-secret"></a>创建客户端机密

对于 Web 应用程序，需要创建应用程序机密。 客户端密码也称为应用程序密码。 应用程序将使用此密码来交换访问令牌的授权代码。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 在“Azure AD B2C - 应用注册”页中，选择已创建的应用程序，例如 webapp1。
1. 在左侧菜单中“管理”下，选择“证书和机密”。 
1. 选择“新建客户端机密”。
1. 在“说明”框中输入客户端机密的说明。 例如，*clientsecret1*。
1. 在“过期时间”下，选择机密持续生效的时间，然后选择“添加”。
1. 记录密码的 **值**，以便在客户端应用程序代码中使用。 退出此页面后，此机密值永不再显示。 在应用程序的代码中将此值用作应用程序机密。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 在“Azure AD B2C - 应用程序”页中，选择已创建的应用程序，例如 webapp1。
1. 选择“密钥”，然后选择“生成密钥”。
1. 选择“保存”以查看密钥。 记下“应用密钥”值。 在应用程序的代码中将此值用作应用程序机密。

* * *

> [!NOTE]
> 出于安全考虑，您可以定期滚动更新应用程序密码，或在紧急情况下立即滚动更新。 任何与 Azure AD B2C 集成的应用程序都应准备好处理密码滚动更新事件，而不管滚动更新可能发生的频率如何。 可以设置两个应用程序密码，使应用程序能够在应用程序密码轮换事件期间继续使用旧密码。 若要添加另一个客户端密码，请重复本部分中的步骤。 

## <a name="enable-id-token-implicit-grant"></a>启用 ID 令牌隐式授权

隐式授权的特征是，令牌（例如 ID 和访问令牌）直接从 Azure AD B2C 返回到应用程序。 对于直接从授权终结点请求 ID 令牌的 Web 应用（例如 ASP.NET Core Web 应用和 [https://jwt.ms](https://jwt.ms)），请在应用注册中启用隐式授权流。

1. 在左侧菜单中的“管理”下，选择“身份验证” 。
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框 。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 注册 Web 应用程序
> * 创建客户端机密

接下来，了解如何创建用户流以使用户能够注册、登录和管理其个人资料。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中创建用户流](tutorial-create-user-flows.md)
