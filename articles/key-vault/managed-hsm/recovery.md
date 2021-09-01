---
title: Azure Key Vault　托管 HSM 恢复概述 | Microsoft Docs
description: 托管 HSM 恢复功能旨在防止意外或恶意删除 HSM 资源和密钥。
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195150"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>托管　HSM　软删除和清除保护

本文介绍了托管 HSM 的两项恢复功能：软删除和清除保护。 文中概述了这两项功能，并演示了如何通过 Azure CLI 和 Azure PowerShell 对其进行管理。

如需更多详细信息，请参阅[托管 HSM 概述](overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/dotnet)。
* [PowerShell 模块](/powershell/azure/install-az-ps)。
* Azure CLI 2.25.0　或更高版本。 运行 `az --version` 以确定你使用的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 托管 HSM。 可使用 [Azure CLI](./quick-create-cli.md) 或 [Azure PowerShell](./quick-create-powershell.md)　创建一个。
* 用户需要以下权限才能对软删除的 HSM 或密钥执行操作：

  | 角色分配 | 说明 |
  |---|---|
  |[托管 HSM 参与者](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|列出、恢复和清除软删除的 HSM|
  |[托管 HSM 加密用户](./built-in-roles.md)|列出软删除的密钥|
  |[托管 HSM 加密管理人员](./built-in-roles.md)|清除和恢复软删除的密钥|



## <a name="what-are-soft-delete-and-purge-protection"></a>软删除和清除保护是什么？

[软删除](soft-delete-overview.md)和清除保护均属于恢复功能。


软删除旨在防止意外删除 HSM 和密钥。 软删除的功能与回收站类似。 删除 HSM 或密钥后，相应内容在可配置的保持期或默认的 90 天内会保持可恢复状态。 软删除状态下的 HSM 和密钥也可清除，此清除代表它们将被永久删除。 清除后，你可以重新创建与已清除项同名的 HSM 和密钥。 恢复和删除 HSM 及密钥都需要特定的角色分配。 无法禁用软删除。

> [!NOTE]
> 即使基础资源处于已删除状态，由于其仍保持已分配给 HSM 的状态，因此 HSM 资源在处于该状态时仍将按小时产生费用。

托管 HSM 名称在每种云环境中都是全局唯一的。 因此，对于以软删除状态存在的 HSM，无法创建与之同名的托管 HSM。 同样，HSM 中的密钥名称也是唯一的。 对于软删除状态中已存在的密钥，无法创建与之同名的密钥。

如需更多详细信息，请参阅 [托管 HSM 软删除概述](soft-delete-overview.md)。

清除保护旨在防止怀有恶意的内部人员删除 HSM 和密钥。 此功能像被时间锁定的回收站。 你可以在可配置的保持期内随时恢复项。 在保持期结束之前，你无法永久删除或清除　HSM　或密钥。 保持期结束时，HSM 或密钥将自动清除。

> [!NOTE]
> 不论是管理员角色还是拥有何种权限都不能替代、禁用或绕过清除保护。 若启用清除保护，任何人（包括 Microsoft）都不能禁用或替代清除保护。 因此，你必须恢复已删除的 HSM 或等待保持期结束，才能重复使用 HSM 名称。

## <a name="manage-keys-and-managed-hsms"></a>管理密钥和托管 HSM

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>托管 HSM (CLI) 

* 检查托管 HSM 的软删除和清除保护状态：

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* 删除 HSM：

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  此操作可恢复，因为软删除默认为启用状态。

* 列出所有软删除的 HSM：

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* 恢复软删除的　HSM：

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* 清除软删除的 HSM：

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > 此操作将永久删除 HSM。

* 若要在 HSM 上启用清除保护，可使用：

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>密钥 (CLI)

* 删除密钥：

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 列出已删除的密钥：

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* 恢复已删除的密钥：

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 清除软删除的密钥： 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > 此操作将永久删除密钥。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>托管 HSM (PowerShell)

* 检查托管 HSM 的软删除和清除保护状态：

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* 删除 HSM：

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  此操作可恢复，因为软删除默认为启用状态。

### <a name="keys-powershell"></a>密钥 (PowerShell)

* 删除密钥：

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* 列出所有已删除的密钥： 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* 恢复软删除的密钥：

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* 清除软删除的密钥：

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > 此操作将永久删除密钥。
    
---

## <a name="next-steps"></a>后续步骤

- [托管 HSM PowerShell cmdlet](/powershell/module/az.keyvault)
- [Key Vault Azure CLI 命令](/cli/azure/keyvault)
- [托管 HSM 完整备份和还原](backup-restore.md)
- [如何启用托管 HSM 日志记录](logging.md)
