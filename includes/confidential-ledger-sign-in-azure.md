---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 985c50f39476c3b1c52670f06d51fde5b3156978
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385165"
---
使用 Azure CLI [az login](/cli/azure/reference-index#az_login) 命令或 Azure PowerShell [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzAccount
```
---

如果 CLI 或 PowerShell 可以打开默认浏览器，它将这样做并加载 Azure 登录页。 否则，请访问 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入终端中显示的授权代码。

如果出现提示，则在浏览器中使用帐户凭据登录。
