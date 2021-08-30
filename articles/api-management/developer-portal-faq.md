---
title: 开发人员门户 - 常见问题
titleSuffix: Azure API Management
description: 有关 API 管理中的开发人员门户的常见问题。 开发人员门户是一个可自定义的网站，API 使用者可以在其中浏览你的 API。
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: troubleshooting
ms.date: 07/30/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b7b7efda7ca28382ca1dfbdead64db9976d67bd4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747791"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API 管理开发人员门户 - 常见问题

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>如果需要门户中不支持的功能，该怎么办？

可以在 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)中打开功能请求或[自行实现缺失的功能](developer-portal-implement-widgets.md)。 详细了解开发人员门户[扩展性](api-management-howto-developer-portal.md#managed-vs-self-hosted)。


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>能否在一个 API 管理服务中有多个开发人员门户？

可以有一个托管门户和多个自承载门户。 所有门户的内容都存储在同一个 API 管理服务中，因此它们是相同的。 如果希望门户的外观和功能不同，可以使用自己的自定义小组件对其进行自承载，以便在运行时上动态自定义页面（例如基于 URL）。

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>门户是否支持 Azure 资源管理器模板，和/或是否与 API 管理 DevOps 资源工具包兼容？

否。

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>门户的内容是否随 API 管理中的备份/还原功能一起保存？

否。

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>是否需要为托管门户依赖项启用额外的 VNet 连接？

大多数情况下是不需要的。

如果 API 管理服务位于内部 VNet 中，则只能从网络内部访问开发人员门户。 管理终结点的主机名必须解析成用于访问门户管理界面的计算机中的服务的内部 VIP。 请确保管理终结点已在 DNS 中注册。 如果配置不当，将会出现以下错误：`Unable to start the portal. See if settings are specified correctly in the configuration (...)`。

如果 API 管理服务位于内部 VNet 中，并且是通过 Internet 上的应用程序网关访问它，请确保启用与开发人员门户和“API 管理”的管理终结点的连接。 可能需要禁用 Web 应用程序防火墙规则。 有关更多详细信息，请参阅[此文档文章](api-management-howto-integrate-internal-vnet-appgateway.md)。

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>我分配了自定义 API 管理域，但发布的门户不起作用

更新域后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>我添加了一个标识提供者，但门户中未显示它

配置标识提供者（例如 Azure AD、AAD B2C）之后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。 请确保开发人员门户页包含 OAuth 按钮小组件。

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>我已设置委派，但门户不使用它

设置委托后，需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使更改生效。

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>我的其他 API 管理配置更改未传播到开发人员门户中

大多数配置更改（例如 VNet、登录和产品条款）都需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)。

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>使用交互式控制台时出现 CORS 错误

交互式控制台从浏览器发出客户端 API 请求。 通过在 API 中添加 [CORS 策略](api-management-cross-domain-policies.md#CORS)解决 CORS 问题。

可以在 Azure 门户的 API 管理服务的“门户概述”部分中检查 CORS 策略的状态。 警告框指示缺少策略或策略配置不正确。

> [!NOTE]
> 
> 仅执行一个 CORS 策略。 如果你指定了多个 CORS 策略（例如在 API 级别和所有 API 级别），则交互式控制台可能不会按预期方式工作。

![显示可检查 CORS 策略状态的位置的屏幕截图。](media/developer-portal-faq/cors-azure-portal.png)

通过单击“启用 CORS”按钮自动应用 CORS 策略。

还可以手动启用 CORS。

1. 选择“在全局级别上手动应用”链接以查看生成的策略代码。
2. 在 Azure 门户中导航到 API 管理服务的“API”部分的“所有 API” 。
3. 在“入站处理”部分中，选择 </> 图标 。
4. 将策略插入到 XML 文件的 <inbound> 部分中。 请确保 <origin> 值与开发人员门户的域匹配。

> [!NOTE]
> 
> 如果在产品范围而不是 API 范围内应用 CORS 策略，并且 API 通过标头使用订阅密钥身份验证，则控制台无法正常工作。
>
> 浏览器会自动发出 `OPTIONS` HTTP 请求，但该请求不包含带有订阅密钥的标头。 由于缺少订阅密钥，API 管理无法将 `OPTIONS` 调用与产品相关联，因此也就无法应用 CORS 策略。
>
> 解决方法之一是在查询参数中传递订阅密钥。

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>什么是 CORS 代理功能，以及应在何时使用它？

在 API 操作详细信息小组件的配置中选择“使用 CORS 代理”选项，可通过 API 管理服务中的门户后端路由交互式控制台的 API 调用。 在此配置中，不再需要为 API 应用 CORS 策略，并且不需要从本地计算机连接到网关终结点。 如果 API 是通过自承载网关进行公开，或者服务处于虚拟网络中，则需要从 API 管理的后端服务连接到网关。 如果使用自承载门户，请在配置文件中使用 `backendUrl` 选项来指定门户的后端终结点。 否则，自承载门户不会知道后端服务的位置。

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>编辑开发人员门户需要哪些权限？

如果在管理模式下打开门户时出现 `Oops. Something went wrong. Please try again later.` 错误，则原因可能是缺少所需的权限 (Azure RBAC)。

旧门户需要服务范围 (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) 的 `Microsoft.ApiManagement/service/getssotoken/action` 权限，以允许用户管理员访问门户。 新门户需要 `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` 范围的 `Microsoft.ApiManagement/service/users/token/action` 权限。

可以使用以下 PowerShell 脚本创建拥有所需权限的角色。 请记得更改 `<subscription-id>` 参数。 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
创建角色后，可以通过 Azure 门户中的“访问控制(IAM)”部分将其授予任何用户。 将此角色分配给用户会在服务范围分配权限。 该用户可以代表服务中的任何用户生成 SAS 令牌。 最起码需要将此角色分配给服务的管理员。 以下 PowerShell 命令演示如何在最低范围将角色分配给用户 `user1`，以避免向该用户授予不必要的权限： 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

向用户授予权限后，该用户必须注销并重新登录到 Azure 门户，才能使新权限生效。

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>出现 `Unable to start the portal. See if settings are specified correctly (...)` 错误

对 `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 发出 `GET` 调用失败时会显示此错误。 该调用是在浏览器中通过门户的管理界面发出的。

如果 API 管理服务处于 VNet 中，请参阅 [VNet 连接问题](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)。

调用失败也可能是 TLS/SSL 证书引起的，该证书已分配到自定义域，且不受浏览器信任。 作为缓解措施，可以删除管理终结点自定义域，API 管理将回退到包含受信任证书的默认终结点。

## <a name="whats-the-browser-support-for-the-portal"></a>门户对浏览器的支持是什么？

| 浏览者                     | 支持       |
|-----------------------------|-----------------|
| Apple Safari                | 是<sup>1</sup> |
| Google Chrome               | 是<sup>1</sup> |
| Microsoft Edge              | 是<sup>1</sup> |
| Microsoft Internet Explorer | 否              |
| Mozilla Firefox             | 是<sup>1</sup> |

 <small><sup>1</sup> 在两个最新的生产版本中受支持。</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>自承载门户的本地开发不再正常工作

如果本地版本的开发人员门户无法保存或检索来自存储帐户或 API 管理实例的信息，则 SAS 令牌可能已过期。 可以通过生成新令牌来解决该问题。 有关说明，请参阅[自承载开发人员门户](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)的相关教程。

## <a name="how-do-i-disable-sign-up-in-the-developer-portal"></a>如何在开发人员门户中禁用注册？

如果不需要开发人员门户中默认启用的注册功能，可以通过以下步骤禁用它：

1. 在 Azure 门户，导航到 API 管理实例。
1. 在菜单的“开发人员门户”下，选择“标识” 。
1. 删除列表中显示的每个标识提供者。 选择每个提供者，选择上下文菜单（“...”），然后选择“删除” 。
 
   :::image type="content" source="media/developer-portal-faq/delete-identity-providers.png" alt-text="更新标识提供者":::
 
1. 导航到开发人员门户管理界面。
1. 删除门户内容中的“注册”链接和导航项。 有关自定义门户内容的信息，请参阅[教程：访问和自定义开发人员门户](api-management-howto-developer-portal-customize.md)。
 
   :::image type="content" source="media/developer-portal-faq/delete-navigation-item.png" alt-text="删除导航项":::
 
1. 修改“注册”页面内容以删除用于输入标识数据的字段，以防用户直接导航到该数据。
   
   可以选择性地删除“注册”页。 目前可使用 [contentItem](/rest/api/apimanagement/2021-01-01-preview/content-item) REST API 来列出和删除此页面。
 
1. 保存更改并[重新发布门户](api-management-howto-developer-portal-customize.md#publish)。

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>如何删除预配到 API 管理服务的开发人员门户内容？

在开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)中的 `scripts.v3/cleanup.bat` 脚本中提供所需参数，然后运行该脚本

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>如何对自承载开发人员门户启用单一登录 (SSO) 身份验证？

在其他身份验证方法中，开发人员门户支持单一登录 (SSO)。 若要使用此方法进行身份验证，需要通过在查询参数中使用令牌来调用 `/signin-sso`：

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>生成用户令牌
可以使用 [API 管理 REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest) 的[获取共享访问令牌](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token)操作来生成特定于用户的令牌（包括管理令牌）。

> [!NOTE]
> 令牌必须进行 URL 编码。


## <a name="next-steps"></a>后续步骤

详细了解新开发人员门户：

- [访问和自定义托管开发人员门户](api-management-howto-developer-portal-customize.md)
- [设置自承载版本的门户](developer-portal-self-host.md)
- [实现自己的小组件](developer-portal-implement-widgets.md)

浏览其他资源：

- [包含源代码的 GitHub 存储库](https://github.com/Azure/api-management-developer-portal)

