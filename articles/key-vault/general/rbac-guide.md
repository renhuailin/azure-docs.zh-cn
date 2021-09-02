---
title: 使用 Azure RBAC 向应用程序授予访问 Azure Key Vault 的权限 | Microsoft Docs
description: 了解如何使用 Azure 基于角色的访问控制提供对密钥、机密和证书的访问权限。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8b53f906eed0df4c6dddbaa64f460cb7a8898a5e
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492642"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限

> [!NOTE]
> 密钥保管库资源提供程序支持两种资源类型：保管库和托管 HSM。 本文中所述的访问控制仅适用于保管库。 若要详细了解托管 HSM 的访问控制，请参阅[托管 HSM 访问控制](../managed-hsm/access-control.md)。

Azure 基于角色的访问控制 (Azure RBAC) 是在 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)基础上构建的授权系统，针对 Azure 资源提供精细的访问权限管理。

Azure RBAC 允许用户管理密钥、机密和证书权限。 它提供了一个可跨所有密钥保管库管理所有权限的位置。 

Azure RBAC 模型提供了在不同范围级别设置权限的功能：管理组、订阅、资源组或单个资源。  用于密钥保管库的 Azure RBAC 还提供了对单个密钥、机密和证书设定单独权限的功能

有关详细信息，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>用于 Key Vault 数据平面操作的 Azure 内置角色

> [!NOTE]
> `Key Vault Contributor` 角色适用于管理平面操作，用于管理密钥保管库。 它不允许访问密钥、机密和证书。

| 内置角色 | 说明 | ID |
| --- | --- | --- |
| Key Vault 管理员| 对密钥保管库以及其中的所有对象（包括证书、密钥和机密）执行所有数据平面操作。 无法管理密钥保管库资源或管理角色分配。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault 证书管理人员 | 对密钥保管库的证书执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault 加密管理人员 | 对密钥保管库的密钥执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| 密钥保管库加密服务加密用户 | 读取密钥的元数据并执行包装/展开操作。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault 加密用户  | 使用密钥执行加密操作。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault 读取者 | 读取密钥保管库及其证书、密钥和机密的元数据。 无法读取机密内容或密钥材料等敏感值。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault 机密管理人员| 对密钥保管库的机密执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault 机密用户 | 读取机密内容。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 4633458b-17de-408a-b874-0445c86b69e6 |

有关 Azure 内置角色定义的详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>对密钥保管库使用 Azure RBAC 机密、密钥和证书权限

用于密钥保管库的新 Azure RBAC 权限模型提供了保管库访问策略权限模型的替代方案。 

### <a name="prerequisites"></a>先决条件

必须拥有 Azure 订阅。 如果尚无 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要添加角色分配，必须拥有 `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../../role-based-access-control/built-in-roles.md#owner)。

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>对密钥保管库启用 Azure RBAC 权限

> [!NOTE]
> 更改权限模型需要具有“Microsoft.Authorization/roleAssignments/write”权限，该权限是[所有者](../../role-based-access-control/built-in-roles.md#owner)和[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色的一部分。 不支持经典订阅管理员角色（如“服务管理员”和“共同管理员”）。

1.  对新密钥保管库启用 Azure RBAC 权限：

    ![启用 Azure RBAC 权限 - 新建保管库](../media/rbac/image-1.png)

2.  对现有密钥保管库启用 Azure RBAC 权限：

    ![启用 Azure RBAC 权限 - 现有保管库](../media/rbac/image-2.png)

> [!IMPORTANT]
> 设置 Azure RBAC 权限模型会使所有访问策略权限失效。 若未分配对等的 Azure 角色，它可能会导致故障。

### <a name="assign-role"></a>分配角色

> [!Note]
> 建议在脚本中使用唯一的角色 ID，而不是角色名称。 这样一来，即使角色重命名，脚本仍可以继续使用。 此文档中的角色名称仅用于提高可读性。

运行以下命令创建角色分配：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

有关详细信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```

有关详细信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

---

若要使用 Azure 门户分配角色，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。  在 Azure 门户中，Azure 角色分配屏幕可用于访问控制 (IAM) 选项卡上的所有资源。

### <a name="resource-group-scope-role-assignment"></a>资源组范围的角色分配

1. 转到包含你的密钥保管库的资源组。

    ![角色分配 - 资源组](../media/rbac/image-4.png)

1. 选择“访问控制 (IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | “密钥保管库读取者” |
    | 将访问权限分配到 | 当前用户 |
    | 成员 | 按电子邮件地址搜索 |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

有关详细信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
有关详细信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

---

上述角色分配提供了在密钥保管库中列出密钥保管库对象的功能。

### <a name="key-vault-scope-role-assignment"></a>Key Vault 范围角色分配

1. 转到“Key Vault”\>“访问控制(IAM)”选项卡
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | “密钥保管库机密主管” |
    | 将访问权限分配到 | 当前用户 |
    | 成员 | 按电子邮件地址搜索 |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

有关详细信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

有关详细信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

---

### <a name="secret-scope-role-assignment"></a>机密范围角色分配

1. 打开以前创建的机密。

1. 单击“访问控制 (IAM)”选项卡

    ![角色分配 - 机密](../media/rbac/image-8.png)

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | “密钥保管库机密主管” |
    | 将访问权限分配到 | 当前用户 |
    | 成员 | 按电子邮件地址搜索 |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

有关详细信息，请参阅[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

有关详细信息，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

---

### <a name="test-and-verify"></a>测试和验证

> [!NOTE]
> 浏览器使用缓存，删除角色分配后需要刷新页面。<br>
> 预留几分钟时间供角色分配刷新

1. 验证在密钥库级别上没有“Key Vault 机管理人员”角色的情况下添加新机密。

转到密钥保管库“访问控制(IAM)”选项卡，删除此资源的“Key Vault 机密管理人员”角色分配。

![删除分配 - 密钥保管库](../media/rbac/image-9.png)

导航到以前创建的机密。 你可以看到所有机密属性。

![具有访问权限的机密视图](../media/rbac/image-10.png)

若创建新机密（“机密”\>“+ 生成/导入”），应会看到以下错误：

   ![创建新机密](../media/rbac/image-11.png)

2.  验证在机密级别上没有“Key Vault 机密管理人员”角色的情况下编辑机密。

-   转到先前创建的机密“访问控制(IAM)”选项卡，删除此资源的“Key Vault 机密管理人员”角色分配。

-   导航到以前创建的机密。 你可以看到机密属性。

   ![无访问权限的机密视图](../media/rbac/image-12.png)

3. 验证在密钥库级别没有读取者角色的情况下读取机密。

-   转到密钥保管库资源组“访问控制(IAM)”选项卡，删除“Key Vault 读取者”角色分配。

-   若导航到密钥保管库的“机密”选项卡，应会看到以下错误：

   ![“机密”选项卡 - 错误](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>创建自定义角色 

[az role definition create 命令](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

有关如何创建自定义角色的详细信息，请参阅：

[Azure 自定义角色](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>已知的限制和性能

-   多租户方案（例如与 Azure Lighthouse 配合使用）不支持密钥保管库数据平面 RBAC
-   每个订阅 2000 个 Azure 角色分配
-   角色分配延迟：在当前预期的性能下，角色分配更改后最多需要 10 分钟（600 秒）才能应用角色

## <a name="learn-more"></a>了解更多
1. 在 [MANAGEMENTGROUP | SUBSCRIPTION | RESOURCEGROUP | RESOURCE] 作用域中，将 [ROLENAME] 角色分配给 [USER | GROUP | SERVICEPRINCIPAL | MANAGEDIDENTITY]。


- [Azure RBAC 概述](../../role-based-access-control/overview.md)
- [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)
- [自定义角色教程](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Azure Key Vault 最佳做法](best-practices.md)
