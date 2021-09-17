---
title: 使用 Azure CLI 在可用性集中部署 VM
description: 了解如何使用 Azure CLI 在可用性集中部署高可用性虚拟机
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 52256f000cd52577e2322b236b1047abd712265b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692042"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>使用 Azure CLI 在可用性集中创建和部署虚拟机

适用于：:heavy_check_mark: Linux VM 

本教程介绍如何使用称作“可用性集”的功能提高 Azure 上虚拟机解决方案的可用性和可靠性。 可用性集可确保在 Azure 上部署的 VM 能够跨多个隔离的硬件群集分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 受到影响，整体解决方案仍可使用和操作。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

本教程在 [Azure Cloud Shell](../../cloud-shell/overview.md) 中使用 CLI，后者已不断更新到最新版本。 若要打开 Cloud Shell，请从任何代码块的顶部选择“试一试”  。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-an-availability-set"></a>创建可用性集

可使用 [az vm availability-set create](/cli/azure/vm/availability-set) 创建可用性集。 在本示例中，*myResourceGroupAvailability* 资源组中名为 *myAvailabilitySet* 的可用性集的更新域数和容错域数均设置为 *2*。

首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组，然后创建可用性集：

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

使用可用性集可跨容错域和更新域隔离资源。 **容错域** 代表服务器、网络和存储资源的隔离集合。 在前面的示例中，在部署 VM 时，可用性集至少分布在两个容错域中。 可用性集还分布在两个 **更新域** 中。 两个更新域确保当 Azure 执行软件更新时，VM 资源是隔离的，防止在 VM 上运行的所有软件同时更新。


## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

必须在可用性集中创建 VM，确保它们正确地分布在硬件中。 在创建可用性集后，无法将现有 VM 添加到可用性集中。

通过 [az vm create](/cli/azure/vm) 创建 VM 时，请使用 `--availability-set` 参数指定可用性集的名称。

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

现在，可用性集内有两台虚拟机。 由于它们在同一可用性集中，Azure 会确保 VM 及其所有资源（包括数据磁盘）分布在隔离的物理硬件上。 这种分布方式有助于确保提高整体 VM 解决方案的可用性。

可以在门户中转到“资源组”> myResourceGroupAvailability > myAvailabilitySet 来查看可用性集分布。 VM 分布在两个容错和更新域中，如以下示例所示：

![门户中的可用性集](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小

以后可以向可用性集添加更多 VM（VM 大小可在硬件上获得）。 使用 [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) 列出可用性集的硬件群集上所有可用的大小：

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

请转到下一教程，了解虚拟机规模集。

> [!div class="nextstepaction"]
> [创建虚拟机规模集](tutorial-create-vmss.md)

* 若要详细了解可用性区域，请访问[可用性区域文档](../../availability-zones/az-overview.md)。
* [Azure 虚拟机的可用性选项](../availability.md)中还提供了有关可用性集和可用性区域的更多文档。
* 若要试用可用性区域，请访问[使用 Azure CLI 在可用性区域中创建 Linux 虚拟机](./create-cli-availability-zone.md)
