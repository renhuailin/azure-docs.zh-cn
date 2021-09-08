---
title: 使用 Azure CLI 在灵活规模集中创建虚拟机
description: 了解如何使用 Azure CLI 在灵活业务流程模式下创建虚拟机规模集。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868277"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>预览：使用 Azure CLI 在灵活规模集中创建虚拟机

**适用于：** :heavy_check_mark: 灵活规模集


本文逐步介绍如何使用 Azure CLI 在灵活业务流程模式下创建虚拟机规模集。 有关灵活规模集的详细信息，请参阅[虚拟机规模集的灵活业务流程模式](flexible-virtual-machine-scale-sets.md)。 

确保已安装最新的 [Azure CLI](/cli/azure/install-az-cli2)，并且已使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。


> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


> [!CAUTION]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="register-for-flexible-orchestration-mode"></a>注册灵活业务流程模式

在以灵活业务流程模式部署虚拟机规模集之前，必须先注册订阅以获取预览版功能。 注册可能需要几分钟才能完成。

使用 [az feature register](/cli/azure/feature#az_feature_register) 为订阅启用预览版。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>开始使用灵活规模集

使用 Azure CLI 创建灵活虚拟机规模集。

### <a name="add-multiple-vms-to-a-scale-set"></a>向规模集添加多个虚拟机 (VM)

在下例中，我们指定一个虚拟机配置文件（VM 类型、网络配置等），还指定要创建的实例数（实例计数 = 2）。  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
```

### <a name="add-a-single-vm-to-a-scale-set"></a>向规模集添加单个 VM

以下示例显示如何创建未带 VM 配置文件的灵活规模集，其中容错域计数设置为 1。 这会创建一个虚拟机，然后将其添加到灵活规模集。

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何在 Azure 门户中创建灵活规模集。](flexible-virtual-machine-scale-sets-portal.md)