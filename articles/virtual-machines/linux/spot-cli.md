---
title: 使用 CLI 部署 Azure 点虚拟机
description: 了解如何使用 CLI 部署 Azure 点虚拟机以节省成本。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0a7be682f921efdfae486e8f6545758964a941ae
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098853"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>使用 Azure CLI 部署 Azure 点虚拟机

使用 [Azure 点虚拟机](../spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出 Azure 点虚拟机。 因此，Azure 点虚拟机非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，Azure 点虚拟机的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位，将 Azure 点虚拟机的最大价格设置为美元 (USD) 。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是 Azure 点虚拟机的当前价格，或者是标准 VM 的价格，只要容量和配额可用即可。 有关设置最大价格的详细信息，请参阅 [Azure 点虚拟机-价格](../spot-vms.md#pricing)。

使用 Azure CLI 创建 Azure 点虚拟机的过程与 [快速入门文章](./quick-create-cli.md)中详述的过程相同。 只需添加 "--priority 污点" 参数，将设置 `--eviction-policy` 为 "释放 (这是默认) 或 `Delete` ，并提供最大价格或 `-1` 。 


## <a name="install-azure-cli"></a>安装 Azure CLI

若要创建 Azure 点虚拟机，需要运行 Azure CLI 版本2.0.74 或更高版本。 若要查找版本，请运行 **az --version**。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>创建 Azure 点虚拟机

此示例演示如何部署将不会根据价格收回的 Linux Azure 点虚拟机。 逐出策略设置为解除分配 VM，以便以后可以重新启动它。 如果要在虚拟机被逐出时删除 VM 和基础磁盘，请将设置 `--eviction-policy` 为 `Delete` 。

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



创建 VM 后，你可以查询以查看资源组中所有 Vm 的最大计费价格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>模拟逐出

可以模拟 Azure 点虚拟机的 [逐出](/rest/api/compute/virtualmachines/simulateeviction) ，以测试应用程序将 repond 突然逐出的程度。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```
`Response Code: 204` 表示模拟逐出成功。 

后续步骤

你还可以使用 [Azure PowerShell](../windows/spot-powershell.md)、 [门户](../spot-portal.md)或 [模板](spot-template.md)创建 Azure 点虚拟机。

使用 [azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查询当前定价信息，了解有关 Azure 点虚拟机的信息。 `meterName`和 `skuName` 都包含 `Spot` 。

如果遇到错误，请参阅 [错误代码](../error-codes-spot.md)。
