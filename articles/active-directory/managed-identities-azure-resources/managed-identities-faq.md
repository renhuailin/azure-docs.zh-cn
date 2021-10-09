---
title: 有关 Azure 资源托管标识的常见问题解答 - Azure AD
description: 有关托管标识的常见问题
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 7d2f09d3990f9e71e55b29b1eac771266627237a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791282"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>有关 Azure 资源的托管标识的常见问题解答 - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="administration"></a>管理

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>如何查找具有托管标识的资源？

可通过使用以下 Azure CLI 命令来查找具有系统分配的托管标识的资源列表： 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="what-azure-rbac-permissions-are-required-to-work-with-managed-identities"></a>使用托管标识时需要哪些 Azure RBAC 权限？ 

- 系统分配的托管标识：需要针对资源的写入权限。 例如，对于虚拟机，你需要 `Microsoft.Compute/virtualMachines/write`。 此操作包含在特定于资源的内置角色（如[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)）中。
- 将用户分配的托管标识分配给资源：需要有资源的写入权限。 例如，对于虚拟机，你需要 `Microsoft.Compute/virtualMachines/write`。 还需要对用户分配的标识执行 `Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action` 操作。 此操作包含在[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)内置角色中。
- 管理用户分配的标识：若要创建或删除用户分配的托管标识，你需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。
- 管理托管标识的角色分配：对于你将向其授予访问权限的资源，你需要[所有者](../../role-based-access-control/built-in-roles.md#all)或[用户访问管理员](../../role-based-access-control/built-in-roles.md#all)角色分配。 对于使用系统分配的标识的资源，或者对于会被授予角色分配的用户分配的标识，你需要[读取者](../../role-based-access-control/built-in-roles.md#all)角色分配。 如果没有读取访问权限，则可以按“用户、组或服务主体”进行搜索，以便查找标识的后备服务主体，而不是在添加角色分配时按托管标识进行搜索。 [详细了解如何分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>如何阻止创建用户分配的托管标识？

可以使用 [Azure Policy](../../governance/policy/overview.md) 来阻止用户创建用户分配的托管标识

1. 导航到 [Azure 门户](https://portal.azure.com)，然后转到“策略”。
2. 选择“定义”
3. 选择“+ 策略定义”并输入必要的信息。
4. 在策略规则部分，粘贴
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

创建策略后，将其分配给要使用的资源组。

1. 导航到资源组。
2. 查找要用于测试的资源组。
3. 从左侧菜单中选择“策略”。
4. 选择“分配策略”
5. 在“基本信息”部分中，提供：
    1. 范围：用于测试的资源组
    1. 策略定义：之前创建的策略。
6. 将所有其他设置保留为默认设置，然后选择“查看 + 创建”

此时，在资源组中创建用户分配的托管标识的任何尝试都会失败。

  ![策略冲突](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>概念

### <a name="do-managed-identities-have-a-backing-app-object"></a>托管标识是否具有后备应用对象？

不能。 托管标识和 Azure AD 应用注册在目录中是不同的。

应用注册有两个组件：应用程序对象和服务主体对象。
Azure 资源的托管标识仅包含以下组件之一：一个服务主体对象。

托管标识在目录中没有通常用于授予 MS Graph 应用权限的应用程序对象。 需要直接将托管标识的 MS Graph 权限授予服务主体。

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>与托管标识关联的凭据是什么？ 它的有效时间有多长？其轮换频率有多高？

> [!NOTE]
> 托管标识的身份验证方式属于在内部具体实施的措施，可能会在不通知的情况下更改。

托管标识使用基于证书的身份验证。 每个托管标识的凭据的有效期为 90 天，在 45 天后轮换。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>如果没有在请求中指定标识，IMDS 将默认采用什么标识？

- 如果已启用系统分配的托管标识且没有在请求中指定标识，则 IMDS 将默认采用系统分配的托管标识。
- 如果未启用系统分配的托管标识且仅存在一个用户分配的托管标识，则 IMDS 将默认采用用户分配的此单一托管标识。
- 如果未启用系统分配的托管标识且存在多个用户分配的托管标识，则必须在请求中指定一个托管标识。

## <a name="limitations"></a>限制

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>是否可以跨多个区域使用同一托管标识？

简而言之，是的，可以在多个 Azure 区域中使用用户分配的托管标识。 详细而言，当用户分配的托管标识作为区域资源创建时，则可在全局范围内使用在 Azure AD 中创建的关联[服务主体](../develop/app-objects-and-service-principals.md#service-principal-object) (SP)。 可以从任何 Azure 区域使用服务主体，其可用性取决于 Azure AD 的可用性。 例如，如果在中南部区域创建了一个用户分配的托管标识，而该区域变为不可用，则此问题仅影响托管标识本身的[控制平面](../../azure-resource-manager/management/control-plane-and-data-plane.md)活动。  已经配置为使用托管标识的任何资源执行的活动都不会受到影响。

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 资源托管标识可以用于 Azure 云服务吗？

否，Azure 云服务中没有支持 Azure 资源托管标识的计划。


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>什么是 Azure 资源托管标识的安全边界？

标识的安全边界是标识所附加到的资源。 例如，启用了 Azure 资源托管标识的虚拟机的安全边界是虚拟机。 在该 VM 上运行的任何代码都可以调用 Azure 资源托管标识终结点和请求令牌。 使用支持 Azure 资源托管标识的其他资源也具有类似体验。

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>如果我将订阅移动到另一个目录中，是否会自动重新创建托管标识？

否。 如果将订阅移到另一个目录中，则必须手动重新创建托管标识并重新向它们授予 Azure 角色分配。
- 对于系统分配的托管标识：禁用并重新启用。 
- 对于用户分配的托管标识：删除、重新创建并重新将其附加到所需的资源（例如虚拟机）

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>是否可以使用托管标识来访问不同目录/租户中的资源？

不是。 托管标识当前不支持跨目录方案。 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>是否有适用于托管标识的速率限制？

托管标识限制依赖于 Azure 服务限制、Azure 实例元数据服务 (IMDS) 限制和 Azure Active Directory 服务限制。

- **Azure 服务限制** 定义可在租户和订阅级别执行的创建操作的数量。 用户分配的托管标识在命名方式上也有[限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)。
- **IMDS** 一般情况下，对 IMDS 的请求限制为每秒 5 个。 系统会拒绝超过此阈值的请求，并显示 429 响应。 对托管标识类别的请求限制为每秒 20 个请求，并发请求数限制为 5 个。 有关详细信息，请参阅 [Azure 实例元数据服务 (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) 一文。
- **Azure Active Directory 服务** 如 [Azure AD 服务限制和局限性](../enterprise-users/directory-service-limits-restrictions.md)中所述，每个托管标识都计入 Azure AD 租户中的对象配额限制。


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>是否可将用户分配的托管标识移到其他资源组/订阅？

不支持将用户分配的托管标识移动到其他资源组。

## <a name="next-steps"></a>后续步骤

- 了解[如何将托管标识用于虚拟机](how-managed-identities-work-vm.md)
