---
title: 如何使用 PowerShell 标记 VM
description: 了解如何使用 PowerShell 标记虚拟机
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594913"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>如何使用 PowerShell 标记 Azure 中的虚拟机

本文介绍如何使用 PowerShell 标记 Azure 中的 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 如果要使用 Azure CLI 标记虚拟机，请参阅[如何使用 Azure CLI 标记 Azure 中的虚拟机](tag-cli.md)。


使用 `Get-AzVM` cmdlet 查看 VM 的标记的当前列表。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

如果虚拟机已包含标记，则会看到列表格式的所有标记。

若要添加标记，请使用 `Set-AzResource` 命令。 通过 PowerShell 更新标记时，标记将作为一个整体进行更新。 如果要将一个标记添加到已包含标记的资源，则需要包含要放置在资源上的所有标记。 下面是如何通过 PowerShell Cmdlet 将其他标记添加到资源的示例。

使用和属性将 VM 的所有当前标记分配给 `$tags` 变量 `Get-AzResource` `Tags` 。

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

若要查看当前标记，请键入变量。

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

在下面的示例中，我们添加了一个名为的标记，其 `Location` 值为 `myLocation` 。 使用将 `+=` 新的键/值对追加到 `$tags` 列表。

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

使用 `Set-AzResource` 设置在 VM 的 *$tags* 变量中定义的所有标记。

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

用于 `Get-AzResource` 显示资源上的所有标记。

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

输出应如下所示，现在包括新标记：

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill](../cost-management-billing/understand/review-individual-bill.md)（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption](../cost-management-billing/manage/usage-rate-card-overview.md)（深入了解 Microsoft Azure 资源消耗）。
