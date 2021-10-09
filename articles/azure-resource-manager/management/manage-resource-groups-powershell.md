---
title: 管理资源组 - Azure PowerShell
description: 使用 Azure PowerShell 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c3bb028186155cc3af47f8efb293b7dbe61e13c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731047"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源管理器资源组

了解如何将 Azure PowerShell 与 [Azure 资源管理器](overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure PowerShell 管理 Azure 资源](manage-resources-powershell.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 资源组](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 根据对组织最有利的原则，决定如何将资源添加到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

## <a name="create-resource-groups"></a>创建资源组

若要创建资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。

```azurepowershell-interactive
New-AzResourceGroup -Name exampleGroup -Location westus
```

## <a name="list-resource-groups"></a>列出资源组

若要列出订阅中的资源组，请使用 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)。

```azurepowershell-interactive
Get-AzResourceGroup
```

若要获取一个资源组，请提供该资源组的名称。

```azurepowershell-interactive
Get-AzResourceGroup -Name exampleGroup
```

## <a name="delete-resource-groups"></a>删除资源组

若要删除资源组，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)。

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleGroup
```

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](delete-resource-group.md)。

## <a name="deploy-resources"></a>部署资源

可以通过使用 Azure PowerShell 或者通过部署 Azure 资源管理器 (ARM) 模板或 Bicep 文件来部署 Azure 资源。

以下示例创建存储帐户。 为存储帐户提供的名称在 Azure 中必须是独一无二的。

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName exampleGroup -Name examplestore -Location westus -SkuName "Standard_LRS"
```

若要部署 ARM 模板或 Bicep 文件，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile storage.bicep
```

若要详细了解如何部署 ARM 模板，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](../templates/deploy-powershell.md)。

若要详细了解如何部署 Bicep 文件，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](../bicep/deploy-powershell.md)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改关键资源。 

若要防止删除资源组及其资源，请使用 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock)。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleGroup
```

若要获取某个资源组的锁，请使用 [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)。

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleGroup
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记组织 Azure 资源](tag-resources.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

若要协助创建 ARM 模板，可以从现有资源导出模板。 有关详细信息，请参阅[使用 Azure PowerShell 导出模板](../templates/export-template-powershell.md)。 

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 管理对 Azure 中资源的访问权限。 有关详细信息，请参阅[使用 Azure PowerShell 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/syntax.md)。
