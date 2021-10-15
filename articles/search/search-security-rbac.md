---
title: 基于角色的授权
titleSuffix: Azure Cognitive Search
description: 对于服务管理和内容任务，使用 Azure 基于角色的访问控制 (Azure RBAC) 获得精细权限。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 1968bb34d124fa37a51b296071ee24b3eae47772
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273716"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>在 Azure 认知搜索服务中使用基于角色的授权

Azure 为平台上运行的所有服务提供全局的[基于角色的访问控制 (RBAC) 授权系统](../role-based-access-control/role-assignments-portal.md)。 在认知搜索中，可按以下方式使用角色：

+ 使用正式版角色进行服务管理。

+ 使用[按请求提供](https://aka.ms/azure-cognitive-search/rbac-preview)的新预览版角色进行内容管理（创建和管理索引及其他顶级对象）。

> [!NOTe]
> 搜索服务参与者是具有“预览版”功能的“正式版”角色。 它是唯一支持真正混合的服务和内容管理任务的角色，允许对给定的搜索服务执行所有操作。 若要获得此角色的内容管理预览版功能，请[注册预览版](https://aka.ms/azure-cognitive-search/rbac-preview)。

某些 RBAC 方案不受支持，或者未在本文中介绍：

+ [“使用托管标识设置与数据源的索引器连接”](search-howto-managed-identities-data-sources.md)中介绍了出站索引器连接。 对于分配有托管标识的搜索服务，可以创建角色分配，以便允许外部数据服务（例如 Azure Blob 存储）通过受信任的搜索服务对 Blob 进行读取访问。

+ 不支持[自定义角色](../role-based-access-control/custom-roles.md)。

+ 不支持通过搜索结果进行用户标识访问（有时称为行级安全性或文档级安全性）。 对于文档级安全性，一种解决方法是使用[安全筛选器](search-security-trimming-for-azure-search.md)按用户标识来细化结果，由此去除请求者不应有权访问的文档。

## <a name="built-in-roles-used-in-search"></a>搜索中使用的内置角色

在认知搜索中，内置角色包括正式版和预览版角色，为其分配的成员身份包括 Azure Active Directory 用户和组。

角色分配在用于创建或管理搜索服务的所有工具和客户端库中是累积且普遍的。 这些客户端包括 Azure 门户、管理 REST API、Azure PowerShell、Azure CLI 以及 Azure SDK 的管理客户端库。

角色作为一个整体应用于搜索服务，必须由所有者分配。 不能将角色分配到特定索引或其他顶级对象。

在 Azure 认知搜索中使用 RBAC 没有区域、层级或定价限制，但搜索服务必须处于 Azure 公有云中。

| 角色 | 适用于 | 说明 |
| ---- | ---------- | ----------- |
| [所有者](../role-based-access-control/built-in-roles.md#owner) | 服务操作（正式版） | 对搜索资源拥有完全访问权限，包括能够分配 Azure 角色。 默认情况下，订阅管理员是成员。 |
| [参与者](../role-based-access-control/built-in-roles.md#contributor) | 服务操作（正式版） | 访问权限级别与所有者相同，但无法分配角色或更改授权选项。 |
| [读者](../role-based-access-control/built-in-roles.md#reader) | 服务操作（正式版） | 对部分服务信息进行受限访问。 在门户中，读取者角色可以访问服务“概述”页、“概要”部分和“监视”选项卡中的信息。在所有其他选项卡和页面中的访问权限受限制。 </br></br>此角色有权访问服务信息：资源组、服务状态、位置、订阅名称和 ID、标记、URL、定价层、副本、分区和搜索单位。 </br></br>此角色还有权访问服务指标：搜索延迟、受限制请求百分比、每秒平均查询数。 </br></br>无法访问 API 密钥、角色分配、内容（索引或同义词映射）或内容指标（使用的存储、对象数）。 |
| [搜索服务参与者](../role-based-access-control/built-in-roles.md#search-service-contributor) | 服务操作（正式版），以及顶级对象（预览版） | 此角色是服务级别的参与者组合，但拥有通过 [`Microsoft.Search/searchServices/*`](/azure/role-based-access-control/resource-provider-operations#microsoftsearch) 对索引、同义词映射、索引器、数据源和技能组执行所有操作的完全访问权限。 此角色适用于需要全面管理服务的搜索服务管理员。 </br></br>与参与者一样，此角色的成员无法创建或管理角色分配，也无法更改授权选项。 |
| [搜索索引数据参与者](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | 文档集合（预览版） | 提供对搜索服务上所有索引中的内容的完全访问权限。 此角色适用于需要导入、刷新或查询索引文档集合的开发人员或索引所有者。 |
| [搜索索引数据读取者](../role-based-access-control/built-in-roles.md#search-index-data-reader) | 文档集合（预览版） | 提供对搜索服务上的搜索索引的只读访问权限。 此角色适用于运行查询的应用和用户。 |

> [!NOTE]
> Azure 资源具有[控制平面和数据平面](../azure-resource-manager/management/control-plane-and-data-plane.md)操作类别的概念。 在认知搜索中，“控制平面”是指[管理 REST API](/rest/api/searchmanagement/) 或等效客户端库中支持的任何操作。 “数据平面”是指针对搜索服务终结点的操作（例如索引编制或查询），或者[搜索 REST API](/rest/api/searchservice/) 或等效客户端库中指定的任何其他操作。 大多数角色仅适用于一个平面， 但搜索服务参与者是个例外，它支持这两个平面中的操作。

## <a name="step-1-preview-sign-up"></a>步骤 1：预览版注册

适用于：搜索索引数据参与者、搜索索引数据读取者、搜索服务参与者

如果使用正式版角色（所有者、参与者、读取者），或者只是执行搜索服务参与者的服务级操作，请跳过此步骤。

新的内置预览版角色针对搜索服务上的内容提供一组精细权限。 尽管内置角色在 Azure 门户中始终可见，但需要完成服务注册，这些角色才能起作用。

若要注册预览版计划，请执行以下操作：

+ [填写此表单](https://aka.ms/azure-cognitive-search/rbac-preview)

处理注册请求最多需要两个工作日。 当服务准备就绪时，将收到一封电子邮件。

## <a name="step-2-preview-configuration"></a>步骤 2：预览版配置

适用于：搜索索引数据参与者、搜索索引数据读取者、搜索服务参与者

如果使用正式版角色（所有者、参与者、读取者），或者只是执行搜索服务参与者的服务级操作，请跳过此步骤。

在此步骤中配置搜索服务，以识别提供 OAuth2 访问令牌的数据请求上的授权头。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/config-svc-portal)

1. 使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。

1. 导航到你的搜索服务。

1. 在左侧导航窗格中，选择“密钥”。

1. 选择“API 访问控制”机制。 

   | 选项 | 状态 | 说明 |
   |--------|--------|-------------|
   | API 密钥 | 正式发布（默认） | 需要请求标头中的[管理员或查询 API 密钥](search-security-api-keys.md)才能授权。 不使用任何角色。 |
   | 基于角色的访问控制 | 预览 | 需要角色分配中的成员身份才能完成任务，如下一步所述。 它还需要授权标头。 选择此选项会限制为支持 2021-04-30-preview REST API 的客户端。 |
   | 两者 | 预览 | 请求使用 API 密钥或授权令牌都有效。 |

如果未看到选项，请检查门户 URL。

如果无法保存所做的选择，或者收到“无法更新搜索服务 `<name>` 的 API 访问控制。 DisableLocalAuth 为预览版且未为此订阅启用”，则表示你的订阅注册尚未启动或尚未处理。

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

使用管理 REST API 版本 2021-04-01-Preview [创建或更新服务](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)来配置服务。

如果使用 Postman 或其他 Web 测试工具，请参阅下面的“提示”以获取有关设置请求的帮助。

1. 将[“AuthOptions”](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)设置为“aadOrApiKey”。

   （可选）设置[“AadAuthFailureMode”](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode)以指定在身份验证失败时是否返回 401 而不是 403。 “disableLocalAuth”的默认值为 false，因此不需要对其进行设置，但下面列出了该值，目的是强调每次设置 authOptions 时，disableLocalAuth 的值必须为 false。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": false,
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. 在服务上[分配角色](#assign-roles)，并验证这些角色是否可以对数据平面正常起作用。

> [!TIP]
> 将会通过 Azure Active Directory 对管理 REST API 调用进行身份验证。 有关设置安全主体和请求的指导，请参阅博客文章 [Azure REST APIs with Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)（通过 Postman 使用 Azure REST API（2021 年））。 已使用该博客文章中提供的说明和 Postman 集合对前面的示例进行了测试。

---

<a name="assign-roles"></a>

## <a name="step-3-assign-roles"></a>步骤 3：分配角色

可以使用 Azure 基于角色的访问控制文档中所述的任何[受支持方法](../role-based-access-control/role-assignments-steps.md)分配角色。

必须是所有者或拥有 [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) 权限才能管理角色分配。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/roles-portal)

1. 对于预览版角色，请使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 在 URL 中应会看到 `feature.enableRbac=true`。

   > [!NOTE]
   > 对于分配到预览版角色的用户和组，仅当使用功能标志打开门户时，才会显示索引和索引器等门户内容。 

1. 导航到你的搜索服务。

1. 在左侧导航窗格中，选择“访问控制(IAM)”。

1. 在右侧的“授予对此资源的访问权限”下，选择“添加角色分配” 。

1. 找到适用的角色，然后分配 Azure Active Directory 用户或组标识：

   + “所有者”
   + 参与者
   + 读取器
   + 搜索服务参与者
   + 搜索索引数据参与者（预览版）
   + 搜索索引数据读取者（预览版）

### <a name="powershell"></a>[**PowerShell**](#tab/roles-powershell)

[使用 PowerShell 分配角色](../role-based-access-control/role-assignments-powershell.md)时，请调用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)（提供 Azure 用户或组名称以及分配的范围）。

开始之前，请确保加载 Azure 和 AzureAD 模块并连接到 Azure：

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

范围限定为服务时，语法应类似于以下示例：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

范围限定为单个索引时：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

回想一下，只能将访问权限范围限定为顶级资源（如索引、同义词映射、索引器、数据源和技能组）。 无法使用 Azure 角色控制对搜索文档（索引内容）的访问。

---

## <a name="step-4-test"></a>步骤 4：测试

### <a name="azure-portal"></a>[**Azure 门户**](#tab/test-portal)

1. 对于预览版角色，请使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 

   > [!NOTE]
   > 对于分配到预览版角色的用户和组，仅当使用功能标志打开门户时，才会显示索引和索引器等门户内容。 

1. 导航到你的搜索服务。

1. 在“概述”页上，选择“索引”选项卡：

   + 搜索索引数据读取者的成员可以使用搜索资源管理器查询索引。 可以使用任何 API 版本来检查访问权限。 你应该可以发出查询和查看结果，但无法查看索引定义。

   + 搜索索引数据参与者的成员可以选择“新建索引”来创建新索引。 保存新索引时会验证对服务的写入访问权限。

### <a name="rest-api"></a>[**REST API**](#tab/test-rest)

+ 使用 Azure Active Directory 注册应用程序。

+ 修改代码以使用[搜索 REST API](/rest/api/searchservice/)（任何受支持版本），在请求上设置授权头并替换 api-key 头 。

  :::image type="content" source="media/search-security-rbac/rest-authorization-header.png" alt-text="包含授权头的 HTTP 请求的屏幕截图" border="true":::

有关如何获取特定环境的令牌的详细信息，请参阅 [Microsoft 标识平台身份验证库](/azure/active-directory/develop/reference-v2-libraries)。

### <a name="net-sdk"></a>[**.NET SDK**](#tab/test-dotnet)

用于 .NET 的 Azure SDK 支持 [NuGet 库 | Azure.Search.Documents 11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) 包中的授权头。

需要进行额外的配置才能将应用程序注册到 Azure Active Directory 以及获取和传递授权令牌。

+ 获取 OAuth 令牌时，范围为“https://search.azure.com/.default”。 SDK 要求受众为“https://search.azure.com”。 “.default”是一种 Azure AD 约定。

+ SDK 将验证用户是否具有“user_impersonation”范围，该范围必须由应用授予，但 SDK 本身只要求提供“https://search.azure.com/.default”。

[客户端机密凭据](/dotnet/api/azure.core.tokencredential)的用法示例：

```csharp
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, tokenCredential);
```

SDK 的 GitHub 存储库中提供了有关[在 Azure SDK for .NET 中使用 AAD 身份验证](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity)的更多详细信息。

> [!NOTE]
> 如果收到 403 错误，请验证搜索服务是否已在预览版计划中注册，以及是否为预览版角色分配配置了服务。

---

## <a name="disable-api-key-authentication"></a>禁用 API 密钥身份验证

无法删除 API 密钥，但可以在服务中将其禁用。 如果使用搜索服务参与者、搜索索引数据参与者和搜索索引数据读取者角色以及 Azure AD 身份验证，则可以禁用 API 密钥，使搜索服务拒绝所有在内容相关请求的头中传递 API 密钥的数据相关请求。

若要禁用[基于密钥的身份验证](search-security-api-keys.md)，请使用管理 REST API 版本 2021-04-01-Preview 并对[更新服务](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)发送两个连续请求。

需要拥有所有者或参与者权限才能禁用功能。 使用 Postman 或其他 Web 测试工具完成以下步骤（参阅下面的“提示”）：

1. 在第一个请求上，将[“AuthOptions”](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)设置为“aadOrApiKey”以启用 Azure AD 身份验证。 请注意，选项指示了以下任一方法的可用性：Azure AD 或本机 API 密钥。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. 在第二个请求上，将[“disableLocalAuth”](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#request-body)设置为 true。 此步骤将关闭“aadOrApiKey”选项的 API 密钥部分，只留下 Azure AD 身份验证。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": true
      }
    }
    ```

不能将步骤 1 和 2 结合使用。 在步骤 1 中，“disableLocalAuth”必须为 false 以符合“AuthOptions”的设置要求，而步骤 2 将该值更改为 true。

若要重新启用密钥身份验证，请重新运行最后一个请求，并将“disableLocalAuth”设置为 false。 搜索服务将自动继续接受请求上的 API 密钥（假设已指定）。

> [!TIP]
> 将会通过 Azure Active Directory 对管理 REST API 调用进行身份验证。 有关设置安全主体和请求的指导，请参阅博客文章 [Azure REST APIs with Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)（通过 Postman 使用 Azure REST API（2021 年））。 已使用该博客文章中提供的说明和 Postman 集合对前面的示例进行了测试。
