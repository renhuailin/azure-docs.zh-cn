---
title: 使用 Azure CLI 向托管标识分配对资源的访问权限 - Azure AD
description: 分步说明如何使用 Azure CLI 将托管标识分配给一个资源，将访问权限分配给另一个资源。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783280"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 向托管标识分配对资源的访问权限

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置托管标识后，便可以授予该托管标识对其他资源的访问权限，这一点与安全主体一样。 此示例展示了如何使用 Azure CLI 授予 Azure 虚拟机或虚拟机规模集的托管标识对 Azure 存储帐户的访问权限。

如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

- 如果你不熟悉 Azure 资源托管标识，请参阅[什么是 Azure 资源托管标识？](overview.md)。 若要了解系统分配的托管标识和用户分配的托管标识类型，请参阅[托管标识类型](overview.md#managed-identity-types)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>使用 Azure RBAC 授予托管标识对另一资源的访问权限

在 Azure 资源（如 [Azure 虚拟机](qs-configure-cli-windows-vm.md)或 [Azure 虚拟机规模集](qs-configure-cli-windows-vmss.md)）上启用托管标识后： 

1. 此示例要授予 Azure 虚拟机对存储帐户的访问权限。 首先，我们使用 [az resource list](/cli/azure/resource/#az_resource_list) 获取名为 myVM 的虚拟机的服务主体：

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   对于 Azure 虚拟机规模集，使用的命令相同，但获取名为“DevTestVMSS”的虚拟机规模集的服务主体：
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. 获得服务主体 ID 后，立即使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)授予虚拟机或虚拟机规模集对“myStorageAcct”存储帐户的“读者”访问权限：

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的托管标识概述](overview.md)
- 若要启用 Azure 虚拟机上的托管标识，请参阅[使用 Azure CLI 在 VM 上配置 Azure 资源的托管标识](qs-configure-cli-windows-vm.md)。
- 若要启用 Azure 虚拟机规模集上的托管标识，请参阅[使用 Azure CLI 在虚拟机规模集上配置 Azure 资源的托管标识](qs-configure-cli-windows-vmss.md)。
