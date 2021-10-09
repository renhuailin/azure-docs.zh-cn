---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c328541ef98391daec87c1988951e8f4bcda4ccc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597298"
---
本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](../articles/key-vault/general/quick-create-cli.md)、[Azure PowerShell 快速入门](../articles/key-vault/general/quick-create-powershell.md)或 [Azure 门户快速入门](../articles/key-vault/general/quick-create-portal.md)中的步骤创建 Key Vault。 

或者，只需运行以下 Azure CLI 或 Azure PowerShell 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 在下面的示例中，将 \<your-unique-keyvault-name\> 替换为密钥保管库的名称。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---
