---
title: 使用 Azure PowerShell 在灵活规模集中创建虚拟机
description: 了解如何使用 PowerShell 在灵活业务流程模式下创建虚拟机规模集。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 9e01c9f4d9cdb3bdd95d77cb2cc26462a95661c6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868276"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>预览：使用 PowerShell 在灵活规模集中创建虚拟机

适用于：:heavy_check_mark: 灵活规模集


本文逐步介绍如何使用 PowerShell 在灵活业务流程模式下创建虚拟机规模集。 有关灵活规模集的详细信息，请参阅[虚拟机规模集的灵活业务流程模式](flexible-virtual-machine-scale-sets.md)。 


> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


> [!CAUTION]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。


## <a name="register-for-flexible-orchestration-mode"></a>注册灵活业务流程模式

在以灵活业务流程模式部署虚拟机规模集之前，必须先注册订阅以获取预览版功能。 注册可能需要几分钟才能完成。

使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```


## <a name="get-started-with-flexible-scale-sets"></a>开始使用灵活规模集

使用 Azure PowerShell 创建灵活虚拟机规模集。

### <a name="add-multiple-vms-to-a-scale-set"></a>向规模集添加多个 VM 

在下例中，我们指定一个虚拟机配置文件（VM 类型、网络配置、存储类型等），还指定要创建的实例数（sku 容量 = 2）。 

1. 创建 IP 地址配置：

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. 创建配置文件：

    配置对象存储用于创建规模集的核心信息。

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. 引用库中的虚拟机映像：

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. 设置用于对虚拟机进行身份验证的信息：

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. 将虚拟网络连接到 config 对象：

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. 创建具有配置对象的规模集：

    此步骤可能需要几分钟才能完成。 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>向规模集添加单个 VM

以下示例显示如何创建未带 VM 配置文件的灵活规模集，其中容错域计数设置为 1。 这会创建一个虚拟机，然后将其添加到灵活规模集。

1. 登录到 Azure PowerShell 并指定部署的订阅和变量。 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. 不要指定 VM 配置文件参数，如网络或 VM SKU。

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. 将 VM 添加到灵活规模集。

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何在 Azure 门户中创建灵活规模集。](flexible-virtual-machine-scale-sets-portal.md)
