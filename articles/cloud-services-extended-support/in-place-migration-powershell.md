---
title: 使用 PowerShell 迁移到 Azure 云服务（外延支持）
description: 如何使用 PowerShell 从 Azure 云服务（经典）迁移到 Azure 云服务（外延支持）
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7626b2af5f5d8c12a29602e62b38ebef2abfd48
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434275"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>使用 PowerShell 迁移到 Azure 云服务（外延支持）

以下步骤说明如何使用 Azure PowerShell 命令从[云服务（经典）](../cloud-services/cloud-services-choose-me.md)迁移到[云服务（外延支持）](overview.md)。

## <a name="1-plan-for-migration"></a>1) 规划迁移
规划是确保迁移成功的最重要步骤。 在开始执行任何迁移步骤之前，请查看[云服务（外延支持）概述](overview.md)和[规划将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型](../virtual-machines/migration-classic-resource-manager-plan.md)。 

## <a name="2-install-the-latest-version-of-powershell"></a>2) 安装最新版本的 PowerShell
有两个主要选项可供安装 Azure PowerShell：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)或 [Web 平台安装程序 (WebPI)](https://aka.ms/webpi-azps)。 WebPI 接收每月的更新。 PowerShell 库会持续接收更新。 本文基于 Azure PowerShell 2.1.0 版。

如需安装说明，请参阅 [How to install and configure Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps?preserve-view=true&view=azuresmps-4.0.0)（如何安装和配置 Azure PowerShell）。

## <a name="3-ensure-admin-permissions"></a>3) 确保拥有管理员权限
若要执行此迁移，必须在 [Azure 门户](https://portal.azure.com)中将你添加为订阅的共同管理员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上中选择“订阅”。  如果看不到该选项，请选择“所有服务”。
3. 查找相应订阅项，然后查看“我的角色”字段。 对于共同管理员，该值应为“帐户管理员”。

如果无法添加协同管理员，请联系订阅的服务管理员或协同管理员，将自己添加为协同管理员。

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) 注册经典提供程序和 CloudService 功能
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

使用以下命令向迁移资源提供程序注册：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> 注册是一次性步骤，但必须在尝试迁移之前完成。 如果不注册，则会出现以下错误消息：
>
> *BadRequest:Subscription is not registered for migration.* （BadRequest：订阅尚未注册迁移。）

为订阅注册 CloudServices 功能。 注册可能需要几分钟才能完成。 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

请等五分钟让注册完成。 

使用以下命令检查经典提供程序审批状态：

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

使用以下命令检查注册状态：  
```powershell
Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

在继续操作之前，请确保上述两项的 RegistrationState 为 `Registered`。

在切换到经典部署模型之前，请确保在当前部署或虚拟网络的 Azure 区域中有足够的 Azure 资源管理器 vCPU 配额。 可以使用以下 PowerShell 命令检查 Azure 资源管理器中目前的 vCPU 数量。 若要了解有关 vCPU 配额的详细信息，请参阅[限制和 Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)。

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


## <a name="5-migrate-your-cloud-services"></a>5) 迁移云服务 
在开始迁移之前，请了解[迁移步骤](./in-place-migration-overview.md#migration-steps)的工作原理以及每个步骤的目的。 

* [迁移不在虚拟网络中的云服务](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [迁移虚拟网络中的云服务](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> 此处描述的所有操作都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，建议重试准备、中止或提交操作。 然后，平台会尝试再次操作。


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5.1) 选项 1 - 迁移不在虚拟网络中的云服务
使用以下命令获取云服务列表。 然后选取要迁移的云服务。

```powershell
Get-AzureService | ft Servicename
```

获取云服务的部署名称。 在此示例中，服务名称是 **My Service**。 使用自己的服务名称替换示例名称。

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

首先，使用以下命令验证是否可以迁移云服务。 此命令会显示任何阻止迁移的错误。 

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

如果验证成功或只有警告，可继续执行“准备”步骤。 

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

使用 Azure PowerShell 或 Azure 门户检查已准备好的云服务（外延支持）的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请中止迁移。
```powershell
Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```
如果已准备好完成迁移，请提交该迁移

```powershell
Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5.1) 选项 2 - 迁移虚拟网络中的云服务

若要迁移虚拟网络中的云服务，请迁移虚拟网络。 云服务会自动随虚拟网络一起迁移。

> [!NOTE]
> 虚拟网络名称可能和新门户中显示的名称不同。 新的 Azure 门户显示名称为 `[vnet-name]`，但实际的虚拟网络名称的类型是 `Group [resource-group-name] [vnet-name]`。 在开始迁移之前，请使用命令 `Get-AzureVnetSite | Select -Property Name` 查找实际的虚拟网络名称，或者在旧版 Azure 门户中查看它。 

此示例将虚拟网络名称设置为 **myVnet**。 使用自己的虚拟网络名称替换示例名称。

```powershell
$vnetName = "myVnet"
```

首先，使用以下命令验证用户是否可以迁移虚拟网络：

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

以下命令会显示任何阻止迁移的警告和错误。 如果验证成功，则可继续执行以下准备步骤：

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

使用 Azure PowerShell 或 Azure 门户检查已准备好的云服务（外延支持）的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令：

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>后续步骤

查看[迁移后的更改](post-migration-changes.md)部分，以查看新云服务（外延支持）部署的部署文件中的更改、自动化和其他特性。
