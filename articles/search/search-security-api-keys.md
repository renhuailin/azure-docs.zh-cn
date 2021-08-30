---
title: API 密钥身份验证
titleSuffix: Azure Cognitive Search
description: API 密钥用于控制对服务终结点的入站访问。 管理员密钥授予写入权限。 可为只读访问权限创建查询密钥。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/25/2021
ms.openlocfilehash: f452aa6ababd338ccc86b7c7c40854367ed46e41
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460617"
---
# <a name="use-api-keys-for-azure-cognitive-search-authentication"></a>使用 API 密钥进行 Azure 认知搜索身份验证

认知搜索使用 API 密钥作为其主要身份验证方法。 对于搜索服务的入站请求（例如创建或查询索引的请求），API 密钥是你唯一的身份验证选项。 一些出站请求方案（尤其是涉及索引器的方案）可以使用 Azure Active Directory 标识和角色。

API 密钥在创建服务时生成。 在请求中传递有效的 API 密钥被视为请求来自授权客户端的证明。 密钥有两种类型。 管理密钥传达对服务的写入权限，还授予查询系统信息的权限。 查询密钥传达读取权限，应用可以使用它来查询特定索引。 

> [!NOTE]
> 授权使用 Azure 基于角色的访问控制 (RBAC) 进行数据平面操作目前为预览版。 可以使用此预览版功能来补充 API 密钥，或者将其替换为[搜索的 Azure 角色](search-security-rbac.md)。 

## <a name="using-api-keys-in-search"></a>在搜索中使用 API 密钥

在连接到搜索服务时，所有请求都必须包含专门为你的服务生成的 API 密钥。

+ 在 [REST 解决方案](search-get-started-rest.md)中，API 密钥通常在请求标头中指定

+ 在 [.NET 解决方案](search-howto-dotnet-sdk.md)中，通常将密钥指定为配置设置，然后将其作为 [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) 进行传递。

可以在 [Azure 门户](https://portal.azure.com)中查看和管理 API 密钥，也可以通过 [PowerShell](/powershell/module/az.search)、[Azure CLI](/cli/azure/search) 或 [REST API](/rest/api/searchmanagement/) 来查看和管理。

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="门户页上的“检索设置”>“密钥”部分" border="false":::

## <a name="what-is-an-api-key"></a>什么是 API 密钥？

API 密钥是由随机生成的数字和字母组成的唯一字符串，随每个请求传递到搜索服务。 如果请求本身和密钥都是有效的，则服务将接受该请求。 

使用了两种类型的密钥来访问搜索服务：管理员（只写）和查询（只读）。

|密钥|说明|限制|  
|---------|-----------------|------------|  
|管理员|授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。<br /><br /> 创建服务时，会在门户中生成两个管理密钥（称为主密钥和辅助密钥），且可按需单独重新生成   。 由于有两个密钥，可以在滚动其中一个密钥时，使用另一个密钥来持续访问服务。<br /><br /> 只能在 HTTP 请求标头中指定管理密钥。 不能在 URL 中放置管理 API 密钥。|每个服务最多有 2 个密钥|  
|查询|授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。<br /><br /> 按需创建查询密钥。<br /><br /> 可以在 HTTP 请求标头中为搜索、建议或查找操作指定查询密钥。 或者，可以在 URL 中以参数形式传递查询密钥。 根据客户端应用程序编写请求的方式，以查询参数的形式传递密钥可能更方便：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|每个服务 50 个密钥|  

 在表面上，管理密钥与查询密钥之间没有区别。 这两个密钥都是由 32 个随机生成的字母数字字符组成的字符串。 如果无法跟踪应用程序中指定了哪种类型的密钥，可以[在门户中检查密钥值](https://portal.azure.com)。  

> [!NOTE]  
> 在请求 URI 中传递敏感数据（例如 `api-key`）的做法很不安全。 为此，Azure 认知搜索只接受查询字符串中 `api-key` 形式的查询密钥。除非必须公开索引的内容，否则应避免这样做。 作为经验法则，我们建议以请求标头的形式传递 `api-key`。  

## <a name="find-existing-keys"></a>查找现有密钥

可以在门户中获取访问密钥，也可以通过 [PowerShell](/powershell/module/az.search)、[Azure CLI](/cli/azure/search) 或 [REST API](/rest/api/searchmanagement/) 来获取。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 列出适用于订阅的[搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
1. 选择该服务，在“概述”页上，单击“设置” >“密钥”以查看管理密钥和查询密钥。

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="门户页上的“查看设置”>“密钥”部分" border="false":::

## <a name="create-query-keys"></a>创建查询密钥

查询密钥用于对面向文档集合的操作索引中的文档进行只读访问。 搜索、筛选和建议查询都是采用查询密钥的操作。 返回系统数据或对象定义（例如索引定义或索引器状态）的所有只读操作需要管理密钥。

限制客户端应用中的访问和操作对于保护服务中的搜索资产至关重要。 对于源自客户端应用的任何查询，请始终使用查询密钥而不是管理密钥。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 列出订阅的[搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 选择该服务，在“概述”页上单击“设置” >“密钥”。
4. 单击“管理查询密钥”。 
5. 使用已为服务生成的查询密钥，或创建最多 50 个新的查询密钥。 默认查询密钥未命名，但可将其他查询密钥命名以便于管理。

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="创建或使用查询密钥" border="false":::

> [!Note]
> 在 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 中可以找到一个演示查询密钥用法的代码示例。

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

系统将为每个服务生成两个管理密钥，以便在轮换主密钥时可以使用辅助密钥，从而实现业务连续性。

1. 在“设置” >“密钥”页中，复制辅助密钥。
2. 对于所有应用程序，更新 API 密钥设置以使用辅助密钥。
3. 重新生成主密钥。
4. 更新所有应用程序以使用新的主密钥。

如果无意中同时重新生成了这两个密钥，则使用这些密钥的所有客户端请求将会失败并出现“HTTP 403 禁止访问”。 但是，内容不会删除，并且不会将你永久性地锁定在系统之外。 

仍然可以通过门户或以编程方式访问该服务。 管理功能是通过订阅 ID 而不是服务 API 密钥运行的，因此，即使 API 密钥不可用，这些功能也仍可用。 

通过门户或管理层创建新的密钥后，只要在请求中提供这些新密钥，就会恢复对内容（索引、索引器、数据源、同义词映射）的访问权限。

## <a name="secure-api-keys"></a>保护 API 密钥

[角色分配](search-security-rbac.md)确定谁可以读取和管理密钥。 以下角色的成员可以查看和重新生成密钥：所有者、参与者和[搜索服务参与者](../role-based-access-control/built-in-roles.md#search-service-contributor)。 读取者角色无权访问 API 密钥。

订阅管理员可以查看和重新生成所有 API 密钥。 作为预防措施，查看角色分配以了解谁有权访问管理密钥。

1. 在 Azure 门户中导航到“搜索服务”页。
1. 在左侧导航窗格中，选择“访问控制(IAM)”，然后选择“角色分配”选项卡 。
1. 将“范围”设置为“此资源”以查看服务的角色分配 。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中的安全性](search-security-overview.md)
+ [Azure 认知搜索中 Azure 基于角色的访问控制](search-security-rbac.md)
+ [使用 PowerShell 进行管理](search-manage-powershell.md) 