---
title: Azure Key Vault 恢复概述 | Microsoft Docs
description: Key Vault 恢复功能旨在防止意外删除或恶意删除密钥保管库以及其中存储的机密、密钥和证书。
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: 45f56812f0d6645c9ac75ce62f9f0659acc358fe
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970072"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>带软删除和清除保护功能的 Azure Key Vault 恢复管理

本文介绍了 Azure Key Vault 的两项恢复功能：软删除和清除保护。 本文档概述了这两项功能，并演示了如何通过 Azure 门户、Azure CLI 和 Azure PowerShell 对其进行管理。

有关 Key Vault 的详细信息，请参阅
- [Key Vault 概述](overview.md)
- [Azure Key Vault 密钥、机密和证书概述](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/dotnet)
* [PowerShell 模块](/powershell/azure/install-az-ps)。
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault - 可以使用 [Azure 门户](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 进行创建
* 用户需要具有以下权限（在订阅级别）才能对软删除的保管库执行操作：

  | 权限 | 说明 |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|查看软删除的密钥保管库的属性|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|清除软删除的密钥保管库|
  |Microsoft.KeyVault/locations/operationResults/read| 检查保管库的清除状态|
  |[密钥保管库参与者](../../role-based-access-control/built-in-roles.md#key-vault-contributor)|恢复软删除的保管库|


## <a name="what-are-soft-delete-and-purge-protection"></a>什么是软删除和清除保护

[软删除](soft-delete-overview.md)和清除保护是两项不同的密钥保管库恢复功能。

> [!IMPORTANT]
> 启用软删除对于确保密钥保管库和凭据不被意外删除至关重要。 但是，启用软删除被视为中断性变更，因为它可能要求你更改应用程序逻辑或为服务主体提供额外的权限。 根据以下说明启用软删除之前，请参阅[此处](soft-delete-change.md)的这个文档，确保你的应用程序与此变更兼容。

“软删除”旨在防止意外删除密钥保管库以及存储在密钥保管库中的密钥、机密以及证书。 可以将软删除视为回收站。 密钥保管库或密钥保管库对象在删除后，会在可供用户配置的保持期内或默认的 90 天内保持可恢复状态。 还可以清除处于软删除状态的密钥保管库，这意味着它们会被永久删除。 这样便可以重新创建具有相同名称的密钥保管库和密钥保管库对象。 恢复和删除密钥保管库与对象都需要提升的访问策略权限。 软删除一旦启用就无法禁用。

必须注意的是，密钥保管库名称是全局独一无二的，因此你无法创建与处于软删除状态的密钥保管库同名的密钥保管库。 类似地，密钥、机密和证书的名称在密钥保管库中是独一无二的。 你将无法使用处于软删除状态的机密、密钥或证书的名称创建另一个机密、密钥或证书。

“清除保护”旨在防止恶意内部成员删除你的密钥保管库、密钥、机密和证书。 可以将此视为一个带有基于时间的锁的回收站。 你可以在可配置的保持期内随时恢复项。 在保持期结束之前，你将无法永久删除或清除密钥保管库。 保持期结束后，系统会自动清除密钥保管库或密钥保管库对象。

> [!NOTE]
> “清除保护”旨在使管理员角色或权限不能重写、禁用或绕过清除保护。 一旦启用清除保护，任何人（包括 Microsoft）都不能禁用或重写它。 这意味着，在重复使用密钥保管库名称之前，必须恢复已删除的密钥保管库或等待保持期结束。

有关软删除的详细信息，请参阅 [Azure Key Vault 软删除概述](soft-delete-overview.md)。

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>验证是否对密钥保管库启用了软删除，并在未启用软删除的情况下将其启用

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 单击“属性”边栏选项卡。
1. 验证软删除旁边的单选按钮是否已设置为“启用恢复”。
1. 如果未对密钥保管库启用软删除，请单击该单选按钮以启用软删除，然后单击“保存”。

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="在“属性”上突出显示了“软删除”，这是用于启用它的值。":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>向服务主体授予清除和恢复已删除机密所需的访问权限

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 单击“访问策略”边栏选项卡。
1. 在表中，找到要授予其访问权限的安全主体所在的行（或添加新的安全主体）。
1. 单击密钥、证书和机密的下拉列表。
1. 滚动到下拉列表底部，然后单击“恢复”和“清除”
1. 安全主体还需要“获取”和“列出”功能才能执行大多数操作。

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="在左侧的导航窗格中，突出显示了“访问策略”。在“访问策略”上，显示了“机密位置”下拉列表，并且选择了四个项：获取、列出、恢复和清除。":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>列出、恢复或清除软删除的密钥保管库

1. 登录到 Azure 门户。
1. 单击页面顶部的搜索栏。
1. 在“最近使用的服务”下，单击“Key Vault”。 不要单击单个密钥保管库。
1. 在屏幕顶部，单击“管理已删除的保管库”选项
1. 此时会在屏幕右侧打开一个上下文窗格。
1. 选择订阅。
1. 如果你的密钥保管库已被软删除，则它会显示在右侧的上下文窗格中。
1. 如果保管库过多，可以单击上下文窗格底部的“加载更多”，也可以使用 CLI 或 PowerShell 来获取结果。
1. 找到要恢复或清除的保管库后，选中其旁边的复选框。
1. 如果要恢复密钥保管库，请选择上下文窗格底部的“恢复”选项。
1. 如果要永久删除密钥保管库，请选择“清除”选项。

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="在“密钥保管库”上，突出显示了“管理删除的保管库”选项。":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="在“管理删除的密钥保管库”上，突出显示并选中了唯一列出的密钥保管库，并突出显示了“恢复”按钮。":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>列出、恢复或清除已软删除的机密、密钥和证书

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 选择与要管理的机密类型（密钥、机密或证书）对应的边栏选项卡。
1. 在屏幕顶部，单击“管理已删除项(密钥、机密或证书)”
1. 此时会在屏幕右侧显示一个上下文窗格。
1. 如果机密、密钥或证书未显示在此列表中，则表明它没有处于软删除状态。
1. 选择要管理的机密、密钥或证书。
1. 选择上下文窗格底部用于恢复或清除的选项。

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="在“密钥”上，突出显示了“管理删除的密钥”选项。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* 验证密钥保管库是否已启用软删除

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 在密钥保管库上启用软删除

    默认情况下，所有新的密钥保管库均已启用软删除。 如果当前有未启用软删除的密钥保管库，请使用以下命令启用软删除。

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* 删除密钥保管库（在启用了软删除的情况下可以恢复）

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 列出所有已软删除的密钥保管库

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 恢复已软删除的密钥保管库

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 清除已软删除的密钥保管库（警告！此操作会永久删除你的密钥保管库）

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 对密钥保管库启用清除保护

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>证书 (CLI)

* 授予清除和恢复证书所需的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* 删除证书

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 列出已删除的证书

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复已删除的证书

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 清除已软删除的证书（警告！此操作会永久删除你的证书）

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>密钥 (CLI)

* 授予清除和恢复密钥所需的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* 删除密钥

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 列出已删除的密钥

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复已删除的密钥

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 清除已软删除的密钥（警告！此操作会永久删除你的密钥）

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>机密 (CLI)

* 授予清除和恢复机密所需的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* 删除机密

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 列出已删除的机密

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复已删除的机密

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 清除已软删除的机密（警告！此操作会永久删除你的机密）

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* 验证密钥保管库是否已启用软删除

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* 删除密钥保管库

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 列出所有已软删除的密钥保管库

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 恢复已软删除的密钥保管库

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 清除已软删除的密钥保管库（警告！此操作会永久删除你的密钥保管库）

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* 对密钥保管库启用清除保护

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>证书 (PowerShell)

* 授予恢复和清除证书所需的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 删除证书

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* 列出密钥保管库中所有已删除的证书

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 恢复处于已删除状态的证书

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 清除已软删除的证书（警告！此操作会永久删除你的证书）

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>密钥 (PowerShell)

* 授予恢复和清除密钥所需的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* 删除密钥

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* 列出密钥保管库中所有已删除的证书

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 恢复已软删除的密钥

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 清除已软删除的密钥（警告！此操作会永久删除你的密钥）

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>机密 (PowerShell)

* 授予恢复和清除机密所需的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* 删除名为 SQLPassword 的机密

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* 列出密钥保管库中所有已删除的机密

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 恢复处于已删除状态的机密

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 清除处于已删除状态的机密（警告！此操作会永久删除你的密钥）

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault PowerShell cmdlet](/powershell/module/az.keyvault)
- [Key Vault Azure CLI 命令](/cli/azure/keyvault)
- [Azure Key Vault 备份](backup.md)
- [如何启用 Key Vault 日志记录](howto-logging.md)
- [Azure Key Vault 安全功能](security-features.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)