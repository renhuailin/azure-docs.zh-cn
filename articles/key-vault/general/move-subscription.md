---
title: Azure Key Vault 将保管库移动到不同的订阅 | Microsoft Docs
description: 有关将密钥保管库移动到不同订阅的指南。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724742"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>将 Azure Key Vault 移动到另一个订阅

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

> [!IMPORTANT]
> **将密钥保管库移动到另一个订阅会导致环境发生中断性变更。**
> 在决定将密钥保管库移动到新订阅之前，请确保你了解此变更的影响，并仔细遵循本文中的指导。
> 如果使用的是托管服务标识 (MSI)，请阅读此文档末尾的移动后说明。 

[Azure Key Vault](overview.md) 自动绑定到用于创建该订阅的订阅的默认 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 租户 ID。 可以按照本 [指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)来查找与订阅关联的租户 ID。 所有访问策略条目和角色分配还与此租户 ID 相关联。  如果将 Azure 订阅从租户 A 移到租户 B，租户 B 中的服务主体（用户和应用程序）将无法访问现有的密钥保管库。若要解决此问题，需执行以下操作：

* 将与订阅中所有现有密钥保管库关联的租户 ID 更改到租户 B。
* 删除所有现有的访问策略条目。
* 添加与租户 B 关联的新访问策略条目。

有关 Azure Key Vault 和 Azure Active Directory 的详细信息，请参阅
- [关于 Azure Key Vault](overview.md)
- [什么是 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [如何查找租户 ID](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>限制

> [!IMPORTANT]
> **无法移动用于磁盘加密的密钥保管库** 如果为 VM 使用带磁盘加密的密钥保管库，则在启用磁盘加密时，无法将密钥保管库移动到其他资源组或订阅。 在将密钥保管库移动到新的资源组或订阅之前，必须禁用磁盘加密。 

某些服务主体（用户和应用程序）绑定到特定的租户。 如果将密钥保管库移动到其他租户中的订阅，则可能无法还原对特定服务主体的访问权限。 请进行检查，确保在要将密钥保管库移动到其中的租户中存在所有基本的服务主体。

## <a name="prerequisites"></a>先决条件

* [参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 级别的访问权限或更高级别，用于在密钥保管库所在的当前订阅。 你可以使用 [Azure 门户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)分配角色。
* [参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 级别访问权限或更高级别，用于要移动密钥保管库的订阅。你可以使用 [Azure 门户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)分配角色。
* 新订阅中有一个资源组。 可以使用 [Azure 门户](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)、 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)或 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli)创建一个。

你可以使用 [Azure 门户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal)、 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)或 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest)检查现有角色。


## <a name="moving-a-key-vault-to-a-new-subscription"></a>将密钥保管库移动到新的订阅

1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 导航到 [密钥保管库](overview.md)
3. 单击“概览”选项卡
4. 选择“移动”按钮
5. 从下拉选项中选择“移动到另一个订阅”
6. 选择要将密钥保管库移动到其中的资源组
7. 确认有关移动资源的警告
8. 选择“确定”

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>订阅在新租户中时的其他步骤

如果已将密钥保管库移动到新租户中的订阅，则需要手动更新租户 ID 并删除旧的访问策略和角色分配。 下面是通过 PowerShell 和 Azure CLI 执行这些步骤的教程。 如果你使用的是 PowerShell，你可能需要运行下面所述的 Clear-AzContext 命令，以允许你查看当前所选范围之外的资源。 

### <a name="update-tenant-id-in-a-key-vault"></a>更新密钥保管库中的租户 ID

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>更新访问策略和角色分配

> [!NOTE]
> 如果 Key Vault 使用 [AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 权限模型。 还需要删除密钥保管库角色分配。 可以使用 [Azure 门户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)、 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)或 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)删除角色分配。 

你的保管库已与正确的租户 ID 关联，并且已删除旧的访问策略项或角色分配，则设置新的访问策略项或角色分配。

有关分配策略，请参阅：
- [使用门户分配访问策略](assign-access-policy-portal.md)
- [使用 Azure CLI 分配访问策略](assign-access-policy-cli.md)
- [使用 PowerShell 分配访问策略](assign-access-policy-powershell.md)

有关添加角色分配，请参阅：
- [使用门户添加角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [使用 Azure CLI 添加角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [使用 PowerShell 添加角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>更新托管标识

如果正在传输整个订阅，并使用 Azure 资源的托管标识，还需要将其更新为新的 Azure Active Directory 租户。 有关托管标识的详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

如果使用的是托管标识，则还必须更新标识，因为旧标识将不再位于相应的 Azure Active Directory 租户中。 参阅下述有助于解决此问题的文档。 

* [更新 MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [将订阅转移到新目录](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>后续步骤

- 详细了解[密钥、机密和证书](about-keys-secrets-certificates.md)
- 关于如何解释 Key Vault 日志等概念性信息，请参阅 [Key Vault 日志记录](logging.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
- [保护密钥保管库](secure-your-key-vault.md)
- [配置 Azure Key Vault 防火墙和虚拟网络](network-security.md)
