---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/18/2021
ms.author: glenga
ms.openlocfilehash: 87f43ebd24cdc7b2cf4163c835e713eb39a52a85
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829694"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要创建三个资源：

- 一个[资源组](../articles/azure-resource-manager/management/overview.md)：相关资源的逻辑容器。
- 一个[存储帐户](../articles/storage/common/storage-account-create.md)：用于维护有关函数的状态和其他信息。
- 一个函数应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用以下命令创建这些项。 支持 Azure CLI 和 PowerShell。

1. 请登录到 Azure（如果尚未这样做）：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure 帐户。

    ---

1. 在所选区域中创建名为 `AzureFunctionsQuickstart-rg` 的资源组：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) 命令可创建资源组。 在上述命令中，使用从 [az account list-locations](/cli/azure/account#az_account_list_locations) 命令返回的可用区域代码，将 `<REGION>` 替换为附近的区域。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location <REGION>
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令可创建资源组。 通常，你会在从 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 返回的、离你近的某个可用区域中创建资源组和资源。

    ---

1. 在资源组和区域中创建常规用途存储帐户：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令可创建存储帐户。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 可创建存储帐户。

    ---

    在上一个示例中，将 `<STORAGE_NAME>` 替换为适合你且在 Azure 存储中唯一的名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](../articles/azure-functions/storage-considerations.md#storage-account-requirements)的常规用途帐户。
