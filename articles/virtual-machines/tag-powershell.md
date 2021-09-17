---
title: 如何使用 PowerShell 标记 VM
description: 了解如何使用 PowerShell 标记虚拟机
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78edd5c62c4f7abe78e70ae90e1b1e7ce3a5dc65
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695332"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>如何使用 PowerShell 标记 Azure 中的虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文介绍如何使用 PowerShell 在 Azure 中标记 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 如果要使用 Azure CLI 标记虚拟机，请参阅[如何使用 Azure CLI 标记 Azure 中的虚拟机](tag-cli.md)。

请使用 `Get-AzVM` cmdlet 来查看 VM 的当前标记列表。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

如果虚拟机已包含标记，你将会看到呈现为列表格式的所有标记。

若要添加标记，请使用 `Set-AzResource` 命令。 在通过 PowerShell 更新标记时，标记会作为整体进行更新。 如果要为已经具有标记的资源添加一个标记，则需要包括想要放置在资源中的所有标记。 下面是如何通过 PowerShell Cmdlet 将其他标记添加到资源的示例。

使用 `Get-AzResource` 和 `Tags` 属性，将 VM 的所有当前标记分配到 `$tags` 变量。

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

若要查看当前标记，请键入该变量。

```azurepowershell-interactive
$tags
```

输出可能如下所示：

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

在下面的示例中，我们添加一个名为 `Location` 且值为 `myLocation` 的标记。 使用 `+=` 将新的键/值对追加到 `$tags` 列表。

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

使用 `Set-AzResource` 来设置 VM 上 $tags 变量中定义的所有标记。

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

使用 `Get-AzResource` 来显示该资源上的所有标记。

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

输出应如下所示，其中现在包括了新标记：

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅[了解 Azure 帐单](../cost-management-billing/understand/review-individual-bill.md)。
