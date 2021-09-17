---
title: 使用 Azure CLI 创建邻近放置组
description: 了解如何针对 Azure 中的虚拟机创建和使用邻近放置组。
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: d37301d0c1bdde17f148c3aedb875d3a1c52d123
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698137"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>使用 Azure CLI 将 VM 部署到邻近放置组

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将 VM 部署到一个[邻近放置组](../co-location.md#proximity-placement-groups)中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-the-proximity-placement-group"></a>创建邻近放置组
使用 [`az ppg create`](/cli/azure/ppg#az_ppg_create) 创建邻近放置组。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

可以使用 [az ppg list](/cli/azure/ppg#az_ppg_list) 来列出所有邻近放置组。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>创建 VM

使用 [new az vm](/cli/azure/vm#az_vm_create) 在邻近放置组中创建 VM。

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

可以使用 [az ppg show](/cli/azure/ppg#az_ppg_show) 查看邻近放置组中的 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性集
还可以在邻近放置组中创建可用性集。 将同一 `--ppg` 参数与 [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) 一起使用来创建一个可用性集，并且也将在同一邻近放置组中创建该可用性集中的所有 VM。

## <a name="scale-sets"></a>规模集

还可以在邻近放置组中创建规模集。 将同一 `--ppg` 参数与 [az vmss create](/cli/azure/vmss#az_vmss_create) 一起使用来创建规模集，并且将在同一邻近放置组中创建所有实例。

## <a name="next-steps"></a>后续步骤

详细了解用于邻近放置组的 [Azure CLI](/cli/azure/ppg) 命令。