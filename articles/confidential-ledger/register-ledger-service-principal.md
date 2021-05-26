---
title: 向 Microsoft Azure 机密账本注册账本服务主体
description: 向 Microsoft Azure 机密账本注册账本服务主体
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: a4f751bcb262011b073207751ac590edeb41d391
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385158"
---
# <a name="register-a-confidential-ledger-service-principal"></a>注册机密账本服务主体

若要将存储帐户与机密账本关联，必须先注册机密账本服务主体。

## <a name="create-a-service-principal"></a>创建服务主体

若要创建服务主体，请运行 Azure CLI [az ad sp create](/cli/azure/ad/sp#az_ad_sp_create) 命令或 [Azure PowerShell Connect-AzureAD](/powershell/module/azuread/connect-azuread) 和 [New-AzureADServicePrincipal](/powershell/module/azuread/new-azureadserviceprincipal) cmdlet。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az ad sp create --id 4353526e-1c33-4fcf-9e82-9683edf52848
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzureAD -TenantId "<tenant-id-of-customer>"
New-AzureADServicePrincipal -AppId 4353526e-1c33-4fcf-9e82-9683edf52848 -DisplayName ConfidentialLedger
```
---

## <a name="assign-roles"></a>分配角色

为存储帐户的机密账本服务主体设置 IAM“[存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)”。 可以使用 Azure CLI [az role assignment create](/cli/azure/role/assignment) 命令或 Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) cmdlet 执行此操作。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az role assignment create --role "Storage Blob Data Contributor" --assignee "4353526e-1c33-4fcf-9e82-9683edf52848" --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId 4353526e-1c33-4fcf-9e82-9683edf52848 -RoleDefinitionName "Storage Blob Data Contributor" -Scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
---

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)
