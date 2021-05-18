---
title: 使用 Azure PowerShell 创建和配置 Azure DDoS 防护计划
description: 了解如何使用 Azure PowerShell 创建 DDoS 防护计划
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 69f9b5a74566879ecf8f15f23e689ebb731da45a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814136"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建和配置 Azure DDoS 防护标准

借助于 Azure PowerShell 开始使用 Azure DDoS 防护标准。 

DDoS 防护计划在订阅中定义一组已启用 DDoS 防护标准的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。 

在本次快速入门中，你将创建一个 DDoS 防护计划，并将其链接到虚拟网络。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 防护计划

在 Azure 中，可将相关的资源分配到资源组。 可以使用现有资源组，也可以创建新组。

若要创建资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 在此示例中，我们将资源组命名为 _MyResourceGroup_，并使用 _美国东部_ 位置：

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

现在，创建名为 _MyDdosProtectionPlan_ 的 DDoS 防护计划：

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>为虚拟网络启用 DDoS

### <a name="enable-ddos-for-a-new-virtual-network"></a>为新的虚拟网络启用 DDoS

创建虚拟网络时，可以启用 DDoS 防护。 在此示例中，我们将为虚拟网络命名为 _MyVnet_： 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>为现有虚拟网络启用 DDoS

创建 DDoS 防护计划时，可以关联现有虚拟网络：

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>验证并测试

首先，请查看 DDoS 防护计划的详细信息：

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

验证该命令是否返回正确的 DDoS 防护计划详细信息。

## <a name="clean-up-resources"></a>清理资源

资源可保留在下一教程中使用。 如果不再需要，请删除 _MyResourceGroup_ 资源组。 删除资源组时，DDoS 防护计划及其所有相关资源也会一起删除。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

若要为虚拟网络禁用 DDoS 防护，请执行以下操作： 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

如果要删除 DDoS 防护计划，必须首先取消与之关联的所有虚拟网络。

## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)