---
title: 基于角色的授权
titleSuffix: Azure Cognitive Search
description: 将 Azure 基于角色的访问控制 (Azure RBAC) 授予精细权限来完成服务管理和内容任务。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177828"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>在 Azure 认知搜索服务中使用基于角色的授权

Azure 为平台上运行的所有服务提供全局的[基于角色的访问控制 (RBAC) 授权系统](../role-based-access-control/role-assignments-portal.md)。 在认知搜索中，可按以下方式使用角色授权：

+ 允许访问控制平面操作，例如，通过“所有者”、“参与者”和“读取者”角色在搜索服务本身上添加容量或轮换密钥。

+ 允许访问数据平面操作，例如创建或查询索引。 此功能目前以公共预览版（[按请求](https://aka.ms/azure-cognitive-search/rbac-preview)）提供。 加入你的订阅后，请按照本文中的说明使用该功能。

+ 允许[使用托管标识](search-howto-managed-identities-data-sources.md)建立出站索引器连接。 对于分配有托管标识的搜索服务，可以创建角色分配来扩展外部数据服务（例如 Azure Blob 存储），以允许受信任的搜索服务对 Blob 进行读取访问。

本文重点介绍前两个项目：用于控制平面和数据平面操作的角色。 有关出站索引器调用的详细信息，请从[配置托管标识](search-howto-managed-identities-data-sources.md)着手了解。

有些 RBAC 方案不直接受支持，其中包括：

+ [自定义角色](../role-based-access-control/custom-roles.md)

+ 通过搜索结果进行用户标识访问（有时称为行级安全性或文档级安全性）

  > [!Tip]
  > 对于文档级安全性，一种解决方法是使用[安全筛选器](search-security-trimming-for-azure-search.md)按用户标识来细化结果，由此去除请求者不应有权访问的文档。
  >

## <a name="roles-used-in-search"></a>搜索中使用的角色

内置角色包括正式版和预览版角色，为其分配的成员身份包括 Azure Active Directory 用户和组。

在用于创建或管理搜索服务的所有工具和客户端库中，角色分配是累积性且普遍性的。 客户端包括 Azure 门户、管理 REST API、Azure PowerShell、Azure CLI 以及 Azure SDK 的管理客户端库。 

在 Azure 认知搜索中使用 RBAC 不存在区域、层或定价方面的限制，但搜索服务必须位于 Azure 公有云中。

| 角色 | 状态 | 适用于 | 说明 |
| ---- | -------| ---------- | ----------- |
| [所有者](../role-based-access-control/built-in-roles.md#owner) | Stable | 控制面板 | 对资源拥有完全访问权限，包括能够分配 Azure 角色。 默认情况下，订阅管理员是成员。 |
| [参与者](../role-based-access-control/built-in-roles.md#contributor) | Stable | 控制面板 | 访问级别与所有者相同，但无法分配角色。 |
| [读者](../role-based-access-control/built-in-roles.md#reader) | Stable | 控制面板 | 对部分服务信息进行受限访问。 在门户中，读取者角色可以访问服务“概述”页、“概要”部分和“监视”选项卡中的信息。在所有其他选项卡和页面中的访问权限受限制。 </br></br>此角色有权访问服务信息：资源组、服务状态、位置、订阅名称和 ID、标记、URL、定价层、副本、分区和搜索单位。 </br></br>此角色还有权访问服务指标：搜索延迟、受限制请求百分比、每秒平均查询数。 </br></br>无权访问 API 密钥、角色分配、内容（索引或同义词映射）或内容指标（存储消耗量、对象数）。 |
| [搜索服务参与者](../role-based-access-control/built-in-roles.md#search-service-contributor) | 预览 | 控制面板 | 提供对搜索服务和对象定义的完全访问权限，但不提供对已编制索引的数据的访问权限。 此角色适用于所需信息比读取者角色提供的信息更多，但不应有权访问索引或同义词映射内容的服务管理员。|
| [搜索索引数据参与者](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | 预览 | 数据平面 | 提供对索引数据的完全访问权限，但不提供对其他任何内容的访问权限。 此角色适用于负责创建和加载内容，但不应有权访问搜索服务信息的开发人员或索引所有者。 范围为搜索服务上的所有顶级资源（索引器、同义词映射、索引器、数据源、技能组）。 |
| [搜索索引数据读取者](../role-based-access-control/built-in-roles.md#search-index-data-reader) | 预览 | 数据平面 | 提供对索引数据的只读访问权限。 此角色适用于针对索引运行查询的用户。 范围为搜索服务上的所有顶级资源（索引器、同义词映射、索引器、数据源、技能组）。 |

## <a name="scope-control-plane-and-data-plane"></a>范围：控制平面和数据平面

Azure 资源具有[控制平面和数据平面](../azure-resource-manager/management/control-plane-and-data-plane.md)类别的操作的概念。 认知搜索的内置角色适用于其中的任一平面。

| 类别 | Operations |
|----------|------------|
| 控制面板 | 操作包括创建、更新和删除服务、管理 API 密钥、调整分区和副本，等等。 [管理 REST API](/rest/api/searchmanagement/) 和等效的客户端库定义了适用于控制平面的操作。 |
| 数据平面 | 针对搜索服务终结点的操作，涵盖服务上托管的所有对象和数据。 索引编制、查询和所有关联的操作针对可通过[搜索 REST API](/rest/api/searchservice/) 和等效的客户端库访问的数据平面。 </br></br>目前，无法使用角色分配来限制对单个索引、同义词映射、索引器、数据源或技能组的访问。 |

## <a name="configure-search-for-data-plane-authentication"></a>为数据平面身份验证配置搜索

如果使用任何预览数据平面角色（搜索索引数据参与者或搜索索引数据读取者）和 Azure AD 身份验证，则必须将搜索服务配置为识别提供 OAuth2 访问令牌的数据请求中的授权头。 本部分将介绍如何配置搜索服务。 如果使用控制平面角色（所有者、参与者、读取者），则可以跳过此步骤。

在开始之前，请[注册](https://aka.ms/azure-cognitive-search/rbac-preview) RBAC 预览版。 必须先将你的订阅注册到程序中，然后才能使用此功能。 处理注册请求最多可能需要两个工作日。 服务准备就绪后，你会收到电子邮件。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/config-svc-portal)

1. 使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。

1. 导航到你的搜索服务。

1. 在左侧导航窗格中，选择“密钥”。

1. 选择一个 API 访问控制机制。 如果未看到这些选项，请检查门户 URL。 如果无法保存所做的选择，则表示订阅注册出现了问题。 

   | 选项 | 状态 | 说明 |
   |--------|--------|-------------|
   | API 密钥 | 正式版（默认） | 要求在用于授权的请求头中提供[管理员或查询 API 密钥](search-security-api-keys.md)。 不要使用角色。 |
   | 基于角色的访问控制 | 预览 | 要求在角色分配中提供成员身份，以完成下一步骤所述的任务。 此外，要求提供授权头。 如果选择此选项，则只能使用支持 2021-04-30-preview REST API 的客户端。 |
   | 两者 | 预览 | 使用 API 密钥或授权令牌的请求有效。 |

为搜索服务启用 RBAC 后，门户将要求在 URL 中提供用于分配角色和查看内容的功能标志。 只有在使用功能标志打开内容（例如索引和索引器）时，门户中才会显示这些内容。 如果以后你想要还原默认行为，请还原对“API 密钥”所做的选择。

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

使用管理 REST API 版本 2021-04-01-Preview 来配置服务。

1. 调用“[创建或更新服务](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)”。

1. 将 [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) 设置为 `aadOrApiKey`。 有关语法，请参阅[此示例](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions)。

1. 设置 [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode)，指定在身份验证失败时是要返回 401 还是 403 错误。

---

## <a name="assign-roles"></a>分配角色

可以使用 Azure 基于角色的访问控制文档中所述的任何[受支持方法](../role-based-access-control/role-assignments-steps.md)来分配角色。

必须是所有者或拥有 [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) 权限才能管理角色分配。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/rbac-portal)

在门户 URL 中设置功能标志以使用预览版角色：搜索服务参与者、搜索索引数据参与者和搜索索引数据读取者。

1. 使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 在 URL 中应会看到 `feature.enableRbac=true`。

1. 导航到你的搜索服务。

1. 在左侧导航窗格中，选择“访问控制(IAM)”。

1. 在右侧的“授予对此资源的访问权限”下，选择“添加角色分配” 。

1. 查找适用的角色（所有者、参与者、读取者、搜索服务参与者、搜索索引数据参与者、搜索索引数据读取者），然后分配 Azure Active Directory 用户或组标识。

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

[使用 PowerShell 分配角色](../role-based-access-control/role-assignments-powershell.md)时，请调用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)，并提供 Azure 用户或组名称以及分配范围。

在开始之前，请确保加载 Azure 和 AzureAD 模块并连接到 Azure：

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

在范围限定为服务的情况下，语法应如以下示例所示：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

在范围限定为单个索引的情况下：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

如前所述，只能将访问范围限定为顶级资源，例如索引、同义词映射、索引器、数据源和技能组。 无法使用 Azure 角色来控制对搜索文档（索引内容）的访问。

---

## <a name="configure-requests-and-test"></a>配置请求并测试

若要以编程方式进行测试，请修改代码以使用搜索 REST API（任何受支持版本）并在请求中设置授权头。 如果使用某个 Azure SDK，请检查其 Beta 版本，以确定授权头是否可用。 

根据所用的应用程序，需要进行额外的配置才能将应用程序注册到 Azure Active Directory 或者获取和传递授权令牌。

或者，可以使用 Azure 门户以及分配给你自己的角色进行测试：

1. 使用以下语法打开门户：[https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 

   尽管在上一步骤中为服务启用了 RBAC，但门户要求提供功能标志来调用 RBAC 行为。 只有在使用功能标志打开内容（例如索引和索引器）时，门户中才会显示这些内容。 如果你想要还原默认行为，请还原对“API 密钥”所做的选择。

1. 导航到你的搜索服务。

1. 在“概述”页上，选择“索引”选项卡：

   + 对于充当搜索索引数据读取者的成员身份，请使用搜索资源管理器查询索引。 可以使用任何 API 版本来检查访问权限。

   + 对于搜索索引数据参与者，请选择“新建索引”创建新索引。 保存新索引时会验证对服务的写入访问权限。

## <a name="disable-api-key-authentication"></a>禁用 API 密钥身份验证

无法删除 API 密钥，但可以在服务中将其禁用。 如果使用搜索索引数据参与者和搜索索引数据读取者角色以及 Azure AD 身份验证，则可以禁用 API 密钥，使搜索服务拒绝所有通过提供密钥发出的与数据相关的请求。

对于此任务，请使用管理 REST API 预览版 2021-04-01-preview。

1. 将 [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) 设置为 `aadOrApiKey`。

1. [分配角色](#assign-roles)并验证它们是否正常工作。

1. 将 `disableLocalAuth` 设置为 True。

如果在最后一个步骤中使用相反的设置（将 `disableLocalAuth` 设置为 False），则搜索服务将自动重新接受请求中的 API 密钥（假设已指定）。