---
title: 如何配置 Azure Key Vault 网络配置
description: 配置 Key Vault 防火墙和虚拟网络的分步说明
services: key-vault
author: sebansal
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 5/11/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: d6035436a005012cf67a46302213f79b2dce1f59
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109850186"
---
# <a name="configure-azure-key-vault-networking-settings"></a>配置 Azure Key Vault 网络设置

本文将提供有关如何配置 Azure Key Vault 网络设置以便与其他应用程序和 Azure 服务配合使用的指导。 若要详细了解不同的网络安全配置，请[阅读此处的内容](network-security.md)。

以下分步说明介绍了如何使用 Azure 门户、Azure CLI 和 Azure PowerShell 配置 Key Vault 防火墙和虚拟网络。

# <a name="portal"></a>[Portal](#tab/azure-portal)


1. 浏览要保护的 Key Vault。
2. 选择“网络”，然后选择“防火墙和虚拟网络”信息栏 。
3. 在“允许的访问来源”下，选择“所选网络”。
4. 若要将现有虚拟网络添加到防火墙和虚拟网络规则，请选择“+ 添加现有虚拟网络”。
5. 在打开的新边栏选项卡中，选择可访问此 Key Vault 的订阅、虚拟网络和子网。 如果虚拟网络和选择的子网没有启用服务终结点，确认想要启用服务终结点，并选择“启用”。 此操作最多可能需要 15 分钟才能生效。
6. 在“IP 网络”下，可通过采用 [CIDR（无类域间路由）表示法](https://tools.ietf.org/html/rfc4632)键入 IPv4 地址范围或单个 IP 地址来添加 IPv4 地址范围。
7. 如果要允许 Microsoft 信任的服务跳过 Key Vault 防火墙，请选择“是”。 有关当前 Key Vault 信任的服务的完整列表，请参阅以下链接。 [Azure Key Vault 信任的服务](./overview-vnet-service-endpoints.md#trusted-services)
7. 选择“保存”。

还可添加新的虚拟网络和子网，然后通过选择“+ 添加新的虚拟网络”，为新创建的虚拟网络和子网启用服务终结点。 然后遵照提示操作。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

下面介绍了如何使用 Azure CLI 配置 Key Vault 防火墙和虚拟网络

1. [安装 Azure CLI](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)。

2. 列出可用的虚拟网络规则。 如果尚未设置此 Key Vault 的任何规则，该列表将为空。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 为虚拟网络和子网添加网络规则。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. 添加允许通信的 IP 地址范围。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下面介绍了如何使用 PowerShell 配置 Key Vault 防火墙和虚拟网络：

1. 安装最新的 [Azure PowerShell](/powershell/azure/install-az-ps) 并[登录](/powershell/azure/authenticate-azureps)。

2. 列出可用的虚拟网络规则。 如果尚未设置此密钥保管库的任何规则，该列表将为空。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 在现有虚拟网络和子网上启用 Key Vault 的服务终结点。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 为虚拟网络和子网添加网络规则。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 添加允许通信的 IP 地址范围。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 如果所有受信服务都可以访问此 Key Vault，请将 `bypass` 设置为 `AzureServices`。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 将默认操作设置为 `Deny`，以启用网络规则。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```
---
## <a name="references"></a>参考
* ARM 模板参考：[Azure Key Vault ARM 模板参考](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlets：[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>后续步骤

* [Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
* [Azure Key Vault 安全性概述](security-features.md)
