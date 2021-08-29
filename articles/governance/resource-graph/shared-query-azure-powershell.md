---
title: 快速入门：使用 Azure PowerShell 创建共享查询
description: 本快速入门介绍如何使用 Azure PowerShell 创建 Resource Graph 共享查询。
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 95057d0c1016bf030f21c9cab9281e9fe51dd897
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325082"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Resource Graph 共享查询

本文介绍如何使用 [Az.ResourceGraph](/powershell/module/az.resourcegraph) PowerShell 模块创建 Azure Resource Graph 共享查询。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > 尽管 Az.ResourceGraph PowerShell 模块为预览版，但必须使用 `Install-Module` cmdlet 单独安装它。

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>创建 Resource Graph 共享查询

将 Az.ResourceGraph PowerShell 模块添加到所选环境中后，即可创建一个 Resource Graph 共享查询。 共享查询是一个 Azure 资源管理器对象，你可授予该对象权限或在 Azure Resource Graph Explorer 中运行该对象。 该查询汇总了按“位置”分组的所有资源。

1. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组，以存储 Azure Resource Graph 共享查询。 此资源组名为 `resource-graph-queries`，并位于 `westus2`。

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. 使用 Az.ResourceGraph PowerShell 模块和 [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) cmdlet 创建 Azure Resource Graph 共享查询：

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. 列出新资源组中的共享查询。 [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) cmdlet 返回一组值。

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. 若要仅获取单个共享查询结果，请使用 `Get-AzResourceGraphQuery` 及其 `Name` 参数。

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>清理资源

如果要从 Azure 环境中删除 Resource Graph 共享查询和资源组，可以使用以下命令执行此操作：

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure PowerShell 创建了一个 Resource Graph 共享查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
