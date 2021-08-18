---
title: 通过 PowerShell 迁移到资源管理器
description: 本文介绍如何在支持的平台上使用 Azure PowerShell 命令将 IaaS 资源（例如虚拟机 (VM)、虚拟网络和存储帐户）从经典部署模型迁移到 Azure 资源管理器部署模型
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e2d63dd0b192d82a3c84306623e58fe7f5b0de3e
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320921"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](classic-vm-deprecation.md#how-does-this-affect-me)。

以下步骤演示了如何使用 Azure PowerShell 命令将基础结构即服务 (IaaS) 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。

也可以根据需要使用 [Azure CLI](migration-classic-resource-manager-cli.md) 迁移资源。

* 如需了解受支持的迁移方案的背景信息，请参阅 [Platform-supported migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-overview.md)（平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移）。
* 如需详细的指南和迁移演练，请参阅 [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)（从技术方面深入探讨如何在支持的平台上完成从经典部署模型到 Azure Resource Manager 部署模型的迁移）。
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)。

<br>
在以下流程图中确定在迁移过程中需要执行步骤的顺序。

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>步骤 1：规划迁移
下面是在评估是否将 IaaS 资源从经典部署模型迁移到资源管理器部署模型时的一些建议最佳做法：

* 通读[受支持的和不受支持的功能和配置](migration-classic-resource-manager-overview.md)。 如果虚拟机使用不受支持的配置或功能，请等到我们宣布支持该配置或功能时再进行迁移。 也可根据需要删除该功能或移出该配置，以利迁移进行。
* 如果通过自动化脚本来部署目前的基础结构和应用程序，则可尝试使用这些脚本进行迁移，以便创建类似的测试性设置。 也可以使用 Azure 门户设置示例环境。

> [!IMPORTANT]
> 目前不支持通过应用程序网关从经典部署模型迁移到资源管理器部署模型。 若要迁移带应用程序网关的虚拟网络，请先删除该网关，然后运行“准备”操作来移动网络。 完成迁移后，在 Azure Resource Manager 中重新连接该网关。
>
> 无法自动迁移其他订阅中连接到 ExpressRoute 线路的 Azure ExpressRoute 网关。 此类情况下，请删除 ExpressRoute 网关、迁移虚拟网络并重新创建网关。 有关详细信息，请参阅[将 ExpressRoute 线路和关联的虚拟网络从经典部署模型迁移到资源管理器部署模型](../expressroute/expressroute-migration-classic-resource-manager.md)。

## <a name="step-2-install-the-latest-version-of-powershell"></a>步骤 2：安装最新版本的 PowerShell
有两个主要选项可供安装 Azure PowerShell：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)或 [Web 平台安装程序 (WebPI)](https://aka.ms/webpi-azps)。 WebPI 接收每月的更新。 PowerShell 库会持续接收更新。 本文基于 Azure PowerShell 2.1.0 版。

如需安装说明，请参阅 [How to install and configure Azure PowerShell](/powershell/azure/)（如何安装和配置 Azure PowerShell）。

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>步骤 3：确保你是订阅的管理员
若要执行此迁移，必须在 [Azure 门户](https://portal.azure.com)中将你添加为订阅的共同管理员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上中选择“订阅”。  如果看不到该选项，请选择“所有服务”。
3. 查找相应订阅项，然后查看“我的角色”字段。 对于共同管理员，该值应为“帐户管理员”。

如果无法添加协同管理员，请联系订阅的服务管理员或协同管理员，将自己添加为协同管理员。

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>步骤 4：设置订阅并针对迁移进行注册
首先，请启动 PowerShell 提示符。 对于迁移，请针对经典部署模型和资源管理器部署模型设置环境。

登录到 Resource Manager 模型的帐户。

```powershell
    Connect-AzAccount
```

使用以下命令获取可用订阅：

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

设置当前会话的 Azure 订阅。 此示例将默认订阅名称设置为 **My Azure Subscription**。 将示例订阅名称替换成自己的名称。

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 注册是一次性步骤，但必须在尝试迁移之前完成。 如果不注册，则会出现以下错误消息：
>
> *BadRequest:Subscription is not registered for migration.* （BadRequest：订阅尚未注册迁移。）

使用以下命令向迁移资源提供程序注册：

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

请等五分钟让注册完成。 使用以下命令检查审批状态：

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

请确保在继续操作之前，RegistrationState 为 `Registered` 。

在切换到经典部署模型之前，请确保在当前部署或虚拟网络的 Azure 区域中有足够的 Azure 资源管理器虚拟机 vCPU。 可以使用以下 PowerShell 命令检查 Azure 资源管理器中目前的 vCPU 数量。 若要了解有关 vCPU 配额的详细信息，请参阅[限制和 Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)。

此示例检查在“美国西部”区域中的可用性。 使用自己的区域名称替换示例名称。

```powershell
    Get-AzVMUsage -Location "West US"
```

现在，请登录到经典部署模型的帐户。

```powershell
    Add-AzureAccount
```

使用以下命令获取可用订阅：

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

设置当前会话的 Azure 订阅。 此示例将默认订阅设置为 **My Azure Subscription**。 将示例订阅名称替换成自己的名称。

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>步骤 5：运行迁移 IaaS 资源的命令
* [迁移云服务中的 VM（不在虚拟网络中）](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [迁移虚拟网络中的 VM](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [迁移存储帐户](#step-52-migrate-a-storage-account)

> [!NOTE]
> 此处描述的所有操作都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，建议重试准备、中止或提交操作。 然后，平台会尝试再次操作。


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>步骤 5.1：选项 1 - 迁移云服务中的虚拟机（不在虚拟网络中）
使用以下命令获取云服务列表。 然后选取要迁移的云服务。 如果云服务中的 VM 在虚拟网络中或者具有 Web 角色或辅助角色，该命令会返回错误消息。

```powershell
    Get-AzureService | ft Servicename
```

获取云服务的部署名称。 在此示例中，服务名称是 **My Service**。 使用自己的服务名称替换示例名称。

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

准备迁移云服务中的虚拟机。 可以从两个选项中进行选择。

* **选项 1：将 VM 迁移到平台所创建的虚拟网络上。**

    首先，使用以下命令验证用户是否可以迁移云服务：

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 如果验证消息不包含错误类型的消息，则可转到“准备”步骤。

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **选项 2：迁移到资源管理器部署模型中的现有虚拟网络。**

    此示例将资源组名称设置为 **myResourceGroup**、将虚拟网络名称设置为 **myVirtualNetwork** 并将子网名称设置为 **mySubNet**。 将该示例中的名称替换成自己的资源名称。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    首先，使用以下命令验证用户是否可以迁移虚拟网络：

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 如果验证消息不包含错误，则可执行以下“准备”步骤：

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

使用前述任一选项成功完成准备操作以后，即可查询 VM 的迁移状态。 确保 VM 处于 `Prepared` 状态。

此示例将 VM 名称设置为 **myVM**。 使用自己的 VM 名称替换示例名称。

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

使用 PowerShell 或 Azure 门户查看准备就绪的资源的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令：

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>步骤 5.1：选项 2 - 迁移虚拟网络中的虚拟机

若要迁移虚拟网络中的虚拟机，可迁移虚拟网络。 虚拟机随虚拟网络自动迁移。 选取要迁移的虚拟网络。
> [!NOTE]
> 通过使用虚拟机的 VHD（OS 和数据）文件创建新的使用托管磁盘的资源管理器虚拟机，来[迁移](./windows/create-vm-specialized-portal.md)使用经典部署模型创建的单个虚拟机。
<br>

> [!NOTE]
> 虚拟网络名称可能和新门户中显示的名称不同。 新的 Azure 门户显示名称为 `[vnet-name]`，但实际的虚拟网络名称的类型是 `Group [resource-group-name] [vnet-name]`。 在开始迁移之前，请使用命令 `Get-AzureVnetSite | Select -Property Name` 查找实际的虚拟网络名称，或者在旧版 Azure 门户中查看它。 

此示例将虚拟网络名称设置为 **myVnet**。 使用自己的虚拟网络名称替换示例名称。

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 如果虚拟网络包含的 Web 角色/辅助角色或 VM 的配置不受支持，则会出现验证错误消息。

首先，使用以下命令验证用户是否可以迁移虚拟网络：

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

以下命令会显示任何阻止迁移的警告和错误。 如果验证成功，则可继续执行以下准备步骤：

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

使用 Azure PowerShell 或 Azure 门户查看已准备就绪的虚拟机的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令：

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>步骤 5.2：迁移存储帐户
完成虚拟机迁移之后，请先执行以下先决条件检查，然后再迁移存储帐户。

> [!NOTE]
> 如果存储帐户没有关联的磁盘或 VM 数据，则可以直接跳至“验证存储帐户并开始迁移”部分。 另请注意，删除经典磁盘、VM 映像或 OS 映像不会删除存储帐户中的源 VHD 文件。 但是，它会中断这些 VHD 文件的租用，以便在迁移后可以重复使用这些 VHD 文件来创建 ARM 磁盘或映像。

* 关于是否迁移了 VM 或存储帐户是否有磁盘资源的先决条件检查：
    * 迁移其磁盘存储在存储帐户中的虚拟机。

        以下命令返回存储帐户中所有 VM 磁盘的 RoleName 和 DiskName 属性。 RoleName 是磁盘附加到的虚拟机的名称。 如果此命令返回了磁盘，请确保先迁移这些磁盘附加到的虚拟机，再迁移存储帐户。
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * 删除存储帐户中存储的未附加 VM 磁盘。

        使用以下命令查找存储帐户中未附加的 VM 磁盘：

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        如果上述命令返回了磁盘，请使用以下命令删除这些磁盘：

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * 删除存储帐户中存储的 VM 映像。

        以下命令返回 OS 磁盘存储在存储帐户中的所有 VM 映像。
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         以下命令返回数据磁盘存储在存储帐户中的所有 VM 映像。
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        使用以下命令删除上述命令返回的所有 VM 映像：
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* 验证存储帐户并开始迁移。

    使用以下命令验证要迁移的每个存储帐户。 在此示例中，存储帐户名称为 **myStorageAccount**。 使用自己的存储帐户名称替换示例名称。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    下一步是准备存储帐户以进行迁移。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    使用 Azure PowerShell 或 Azure 门户检查准备就绪的存储帐户的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令：

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>后续步骤
* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-cli.md)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.yml)
