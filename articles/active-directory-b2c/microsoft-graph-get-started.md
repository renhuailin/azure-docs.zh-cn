---
title: 注册 Microsoft Graph 应用程序
titleSuffix: Azure AD B2C
description: 通过注册向其授予了所需 Graph API 权限的应用程序，准备使用 Microsoft Graph 管理 Azure AD B2C 资源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62b1f81f0e2a1582e78dff044a3109c3b1525939
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588953"
---
# <a name="register-a-microsoft-graph-application"></a>注册 Microsoft Graph 应用程序

利用 [Microsoft Graph][ms-graph]，你可以管理 Azure AD B2C 租户中的许多资源，包括客户用户帐户和自定义策略。 通过编写调用 [Microsoft Graph API][ms-graph-api] 的脚本或应用程序，你可以自动执行租户管理任务，例如：

* 将现有用户存储迁移到 Azure AD B2C 租户
* 使用 Azure DevOps 中的 Azure 管道部署自定义策略，以及管理自定义策略密钥
* 在你自己的页面上托管用户注册，并在后台在 Azure AD B2C 目录中创建用户帐户
* 自动执行应用程序注册
* 获取审核日志

以下各部分可帮助你为使用 Microsoft Graph API 自动管理 Azure AD B2C 目录中的资源做准备。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 交互模式

使用 Microsoft Graph API 管理 Azure AD B2C 租户中的资源时，可以使用两种通信模式：

* **交互式** - 适用于运行一次的任务，你可以使用 B2C 租户中的管理员帐户执行管理任务。 此模式要求管理员在调用 Microsoft Graph API 之前使用其凭据进行登录。

* **自动化** - 对于计划的或连续运行的任务，此方法使用你为其配置了执行管理任务所需权限的服务帐户。 通过以下方式在 Azure AD B2C 中创建“服务帐户”：注册一个应用程序，并让你的应用程序和脚本使用其应用程序（客户端）ID 和 OAuth 2.0 客户端凭据授权通过该应用程序进行身份验证。 在这种情况下，应用程序以自己的身份调用 Microsoft Graph API，而不是如前面的交互方式中所述使用管理员用户的身份。

可以通过创建以下各部分中所示的应用程序注册来启用 **自动化** 交互方案。

尽管 Azure AD B2C 身份验证服务目前不直接支持 OAuth 2.0 客户端凭据授权流，但你可以使用 Azure AD 和 Microsoft 标识平台/令牌终结点为 Azure AD B2C 租户中的应用程序设置客户端凭据流。 Azure AD B2C 租户与 Azure AD 企业租户共享某些功能。

## <a name="register-management-application"></a>注册管理应用程序

你必须先在 Azure AD B2C 租户中创建授予所需 API 权限的应用程序注册，你的脚本和应用程序才能与 [Microsoft Graph API][ms-graph-api] 进行交互来管理 Azure AD B2C 资源。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，*managementapp1*。
1. 选择“仅此组织目录中的帐户”。
1. 在“权限”下，清除“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”。
1. 记下应用“概述”页上显示的“应用程序(客户端) ID”。 在稍后的步骤中会使用此值。

## <a name="grant-api-access"></a>授予 API 访问权限

若要让应用程序访问 Microsoft Graph 中的数据，请向已注册的应用程序授予相关[应用程序权限](/graph/permissions-reference)。 应用程序的有效权限是权限默示的完整特权级别。 例如，若要创建、读取、更新和删除 Azure AD B2C 租户中的每个用户，请添加“User.ReadWrite.All”权限。 

> [!NOTE]
> “User.ReadWrite.Al”权限不包括更新用户帐户密码的功能。 如果应用程序需要更新用户帐户密码，请[授予用户管理员角色](#optional-grant-user-administrator-role)。 授予[用户管理员](../active-directory/roles/permissions-reference.md#user-administrator)角色时，不需要执行“User.ReadWrite.All”。 “用户管理员”角色包括管理用户所需的所有内容。

你可以为应用程序授予多项应用程序权限。 例如，如果应用程序还需要管理 Azure AD B2C 租户中的组，则还需添加“Group.ReadWrite.All”权限。 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[可选]授予用户管理员角色

如果应用程序或脚本需要更新用户的密码，你需要将“用户管理员”角色分配给应用程序。 [用户管理员](../active-directory/roles/permissions-reference.md#user-administrator)角色具有你向应用程序授予的一组固定权限。 

若要添加“用户管理员”角色，请遵照以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 搜索并选择“Azure AD B2C”。
1. 在“管理”下，选择“角色和管理员”。 
1. 选择“用户管理员”角色。 
1. 选择“添加分配”。
1. 在“选择”文本框中，输入之前注册的应用程序的名称或 ID，如“managementapp1”。 当它出现在搜索结果中时，选择应用程序。
1. 选择“添加”  。 可能需要几分钟才能完全传播权限。

## <a name="create-client-secret"></a>创建客户端机密

请求令牌时，应用程序需要使用客户端密码证明其身份。 若要添加客户端机密，请执行以下步骤：

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>后续步骤

现在，你已注册了管理应用程序并向其授予了所需的权限，你的应用程序和服务（例如 Azure Pipelines）现在可以使用其凭据和权限与 Microsoft Graph API 进行交互。 

* [从 Azure AD 获取访问令牌](/graph/auth-v2-service#4-get-an-access-token)
* [使用访问令牌调用 Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph 支持的 B2C 操作](microsoft-graph-operations.md)
* [使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](microsoft-graph-operations.md)
* [使用 Azure AD 报告 API 获取审核日志](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview