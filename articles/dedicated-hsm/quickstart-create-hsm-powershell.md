---
title: 快速入门：使用 Azure PowerShell 创建 Azure 专用 HSM
description: 使用 Azure PowerShell 创建 Azure 专用 HSM
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom: devx-track-azurepowershell - devx-track-azurepowershell - mode-api
ms.openlocfilehash: 07c3e6124bf6c2494f944bf955533df2f8528e84
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703768"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Azure 专用 HSM

本文介绍如何使用 [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) PowerShell 模块创建 Azure 专用 HSM。

## <a name="requirements"></a>要求

* 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > 尽管 Az.DedicatedHsm PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在指定位置创建具有指定名称的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>创建专用 HSM

若要创建专用 HSM，请使用 [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) cmdlet。 以下示例在指定订阅中创建专用 HSM。

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>获取专用 HSM

若要检索有关现有专用 HSM 的信息，请使用 [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) cmdlet。 以下示例获取指定的专用 HSM。

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>更新专用 HSM

若要更新专用 HSM，请使用 [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) cmdlet。 以下示例在指定订阅中更新专用 HSM。

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用本文中创建的资源，可以运行以下示例将其删除。

### <a name="remove-a-dedicated-hsm"></a>删除专用 HSM

若要删除专用 HSM，请使用 [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) cmdlet。 以下示例删除指定的专用 HSM。

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>删除资源组

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用 HSM](overview.md)。
