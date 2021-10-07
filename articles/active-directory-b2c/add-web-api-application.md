---
title: 添加 Web API 应用程序 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何向 Active Directory B2C 租户添加 Web API 应用程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 08/24/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b30bb973c75551fc635b5a80131190bbb36ab118
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570229"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>向 Azure Active Directory B2C 租户添加 Web API 应用程序

 在租户中注册 Web API 资源，以便他们可以接受并响应提供访问令牌的客户端应用程序的请求。 本文介绍如何在 Azure Active Directory B2C (Azure AD B2C) 中注册 Web API。

要在 Azure AD B2C 租户中注册应用程序，可以使用新的统一“应用注册”体验或旧版“应用程序(旧版)”体验 。 [详细了解此新体验](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，“webapi1”。
1. 在“重定向 URI”下选择“Web”，然后输入 Azure AD B2C 会将应用程序请求的任何令牌返回到其中的终结点。 在生产应用程序中，可以将重定向 URI 设置为 `https://localhost:5000` 之类的终结点。 开发或测试期间，你可以将它设置为 `https://jwt.ms`，这是一个 Microsoft 拥有的 Web 应用程序，用于显示已解码的令牌内容（令牌内容始终保留在浏览器中）。 可以随时在注册的应用程序中添加和修改重定向 URI。
1. 选择“注册”。
1. 记录“应用程序(客户端) ID”，以便在 Web API 的代码中使用。

如果有一个实现隐式授权流的应用程序（例如[基于 JavaScript 的单页应用程序 (SPA)](tutorial-register-spa.md)），可以通过执行以下步骤来启用此流：

1. 在“管理”下，选择“身份验证”。 
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框  。
1. 选择“保存”。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“应用程序(旧版)”，然后选择“添加” 。
1. 输入应用程序的名称。 例如，“webapi1”。
1. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。  
1. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 在生产应用程序中，可以将回复 URL 设置为 `https://localhost:44332` 之类的值。 出于测试目的，将回复 URL 设置为 `https://jwt.ms`。
1. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
1. 单击“创建”。
1. 在属性页上，记录在配置 Web 应用程序时要使用的应用程序 ID。

* * *

## <a name="configure-scopes"></a>配置范围

可通过作用域控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 在本教程中，请使用作用域为 Web API 定义读取和写入权限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授予权限

若要从应用程序调用受保护的 Web API，需授予应用程序访问该 API 的权限。 例如，在[教程：在 Azure Active Directory B2C 中注册应用程序](tutorial-register-applications.md)中，在 Azure AD B2C 中注册了一个名为 webapp1 的 Web 应用程序。 可使用此应用程序调用 Web API。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

注册应用程序以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以便使用该应用程序。 应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。