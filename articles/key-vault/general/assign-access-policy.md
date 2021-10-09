---
title: 分配 Azure Key Vault 访问策略 (CLI)
description: 如何使用 Azure CLI 将 Key Vault 访问策略分配到安全主体或应用程序标识。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837628"
---
# <a name="assign-a-key-vault-access-policy"></a>分配 Key Vault 访问策略

Key Vault 访问策略确定给定的安全主体（即用户、应用程序或用户组）是否可以对 Key Vault [机密](../secrets/index.yml)、[密钥](../keys/index.yml)和[证书](../certificates/index.yml)执行不同的操作。 可以使用 [Azure 门户](assign-access-policy-portal.md)、Azure CLI 或 [Azure PowerShell](assign-access-policy-powershell.md) 来分配访问策略。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>分配访问策略

1.  在 [Azure 门户](https://portal.azure.com)中，导航到 Key Vault 资源。 

1.  在“设置”下，选择“访问策略”，然后选择“添加访问策略”  ：

    ![选择“访问策略”，选择“添加角色分配”](../media/authentication/assign-policy-portal-01.png)

1.  在“证书权限”、“密钥权限”和“机密权限”下选择所需要的权限  。 也可以选择包含常见权限组合的模板：

    ![指定访问策略权限](../media/authentication/assign-policy-portal-02.png)

1. 在“选择主体”下，选择“未选择任何项”链接，以打开“主体”选择窗格  。 在搜索字段中输入用户、应用或服务主体的名称，选择相应的结果，然后选择“选择”。

    ![为该访问策略选择安全主体](../media/authentication/assign-policy-portal-03.png)

    如果使用的是应用的托管标识，请搜索并选择该应用本身的名称。 有关安全主体的详细信息，请参阅 [Key Vault 身份验证](authentication.md)。
 
1.  返回“添加访问策略”窗格，选择“添加”以保存该访问策略 。

    ![添加分配了安全主体的访问策略](../media/authentication/assign-policy-portal-04.png)

1. 返回“访问策略”页，验证“当前访问策略”下是否已列出你的访问策略，然后选择“保存”  。 访问策略在保存之后才会应用。

    ![保存访问策略更改](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要详细了解如何使用 Azure CLI 在 Azure Active Directory 中创建组，请参阅 [az ad group create](/cli/azure/ad/group#az_ad_group_create) 和 [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add)。

## <a name="configure-the-azure-cli-and-sign-in"></a>配置 Azure CLI 并登录

1. 若要在本地运行 Azure CLI 命令，请安装 [Azure CLI](/cli/azure/install-azure-cli)。
 
    要直接在云中运行命令，请使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

1. 仅限本地 CLI：使用 `az login` 登录到 Azure：

    ```bash
    az login
    ```

    `az login` 命令会打开浏览器窗口来收集凭据（如果需要）。

## <a name="acquire-the-object-id"></a>获取对象 ID

确定要为其分配访问策略的应用程序、组或用户的对象 ID：

- 应用程序和其他服务主体：请使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 命令来检索服务主体。 请检查命令的输出，以确定要为其分配访问策略的安全主体的对象 ID。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 组：请使用 [az ad group list](/cli/azure/ad/group#az_ad_group_list) 命令，并通过 `--display-name` 参数筛选结果：

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 用户：请使用 [az ad user show](/cli/azure/ad/user#az_ad_user_show) 命令，并在 `--id` 参数中传递用户的电子邮件地址：

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>分配访问策略
    
使用 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 命令来分配所需的权限：

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

将 `<object-id>` 替换为安全主体的对象 ID。

为这些特定类型分配权限时，只需包括 `--secret-permissions`、`--key-permissions` 和 `--certificate-permissions`。 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 文档中提供了 `<secret-permissions>`、`<key-permissions>` 和 `<certificate-permissions>` 的允许值。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要详细了解如何使用 Azure PowerShell 在 Azure Active Directory 中创建组，请参阅 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 和 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)。

## <a name="configure-powershell-and-sign-in"></a>配置 PowerShell 并登录

1. 若要在本地运行命令，请安装 [Azure PowerShell](/powershell/azure/)（如果尚未这样做）。

    要直接在云中运行命令，请使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

1. 仅限本地 PowerShell：

    1. 安装 [Azure Active Directory PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD)。

    1. 登录 Azure：

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>获取对象 ID

确定要为其分配访问策略的应用程序、组或用户的对象 ID：

- 应用程序和其他服务主体：使用带有 `-SearchString` 参数的 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) cmdlet 根据所需服务主体的名称筛选结果：

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 组：使用带有 `-SearchString` 参数的 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) cmdlet 根据所需组的名称筛选结果：

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    在输出中，对象 ID 将作为 `Id` 列出。

- 用户：使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet，将用户的电子邮件地址传递给 `-UserPrincipalName` 参数。

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    在输出中，对象 ID 将作为 `Id` 列出。

## <a name="assign-the-access-policy"></a>分配访问策略

使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 分配访问策略：

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

为这些特定类型分配权限时，只需提供 `-PermissionsToSecrets`、`-PermissionsToKeys` 和 `-PermissionsToCertificates`。 `<secret-permissions>`、`<key-permissions>` 和 `<certificate-permissions>` 的允许值在 [Set-AzKeyVaultAccessPolicy - 参数](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)文档中提供。

---

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性](security-features.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
