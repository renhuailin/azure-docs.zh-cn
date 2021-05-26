---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 5a94cdae7cc332b8e0df5b2ebdc6ae57b67c8a68
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385161"
---
资源提供程序是提供 Azure 资源的服务。 使用 Azure CLI [az provider register](/cli/azure/provider#az_provider_register) 命令或 Azure PowerShell [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) cmdlet 注册机密账本资源提供程序“microsoft.ConfidentialLedger”。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

可以使用 Azure CLI [az provider register](/cli/azure/provider#az_provider_show) 命令或 Azure PowerShell [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) cmdlet 验证注册是否完成。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
