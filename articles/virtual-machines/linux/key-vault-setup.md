---
title: 使用 CLI 设置 Azure Key Vault
description: 如何使用 Azure CLI 为虚拟机设置 Key Vault。
author: mimckitt
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: af975db3c0737d9fb075b50b6087cc987198d7fd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691710"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>如何使用 Azure CLI 为虚拟机设置 Key Vault

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

在 Azure Resource Manager 堆栈中，密码/证书被建模为 Key Vault 所提供的资源。 若要了解有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md) 为了让 Key Vault 能与 Azure Resource Manager VM 搭配使用，必须将 Key Vault 上的 *EnabledForDeployment* 属性设置为 true。 本文说明如何通过 Azure CLI 设置用于 Azure 虚拟机 (VM) 的 Key Vault。 

若要执行这些步骤，需要安装最新的 [Azure CLI](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

## <a name="create-a-key-vault"></a>创建密钥保管库
使用 [az keyvault create](/cli/azure/keyvault) 创建密钥保管库并分配部署策略。 以下示例在 `myResourceGroup` 资源组中创建名为 `myKeyVault` 的密钥保管库：

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>更新用于 VM 的 Key Vault
使用 [az keyvault update](/cli/azure/keyvault) 在现有的密钥保管库上设置部署策略。 以下命令在 `myResourceGroup` 资源组中更新名为 `myKeyVault` 的密钥保管库：

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>使用模板设置密钥保管库
使用模板时，必须将 Key Vault 资源的 `enabledForDeployment` 属性设置为 `true`，如下所示：

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>后续步骤
有关使用模板创建 Key Vault 时可以配置的其他选项，请参阅[创建密钥保管库](https://azure.microsoft.com/resources/templates/key-vault-create/)。
