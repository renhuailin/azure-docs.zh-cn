---
title: 使用 CLI 部署 Azure 现成虚拟机
description: 了解如何使用 CLI 部署 Azure 现成虚拟机以节省成本。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 81753681776ab4fd8ae42081ac3d5b0998d4f219
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692062"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>使用 Azure CLI 部署 Azure 现成虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

使用 [Azure 现成虚拟机](../spot-vms.md)，可以利用未使用的容量，大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Azure 现成虚拟机。 因此，Azure 现成虚拟机非常适用于那些处理服务中断的工作负荷（例如批处理作业）、开发/测试环境、大型计算工作负荷等。

Azure 现成虚拟机的定价因地区和 SKU 而异。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的 VM 定价。 

可以选择对 VM 设置你愿意支付的每小时最高价格。 Azure 现成虚拟机的最高价格可以美元 (USD) 形式设置，最多保留 5 位小数。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出 VM。 VM 价格将是 Azure 现成虚拟机的当前价格或标准 VM 的价格（以较低者为准，但一定要有容量和配额可用）。 有关设置最高价格的详细信息，请参阅 [Azure 现成虚拟机 - 定价](../spot-vms.md#pricing)。

使用 Azure CLI 创建 Azure 现成虚拟机的过程与此相同，详见[快速入门文章](./quick-create-cli.md)。 只需添加“--priority Spot”参数，将 `--eviction-policy` 设置为“解除分配”（这是默认值）或 `Delete`，并提供最高价格或 `-1`。 


## <a name="install-azure-cli"></a>安装 Azure CLI

要创建 Azure 现成虚拟机，需要运行 Azure CLI 版本 2.0.74 或更高版本。 若要查找版本，请运行 **az --version**。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>创建 Azure 现成虚拟机

本示例演示如何部署不会因为价格而被逐出的 Linux Azure 现成虚拟机。 逐出策略设置为解除分配 VM，以便以后可以重新启动该虚拟机。 如果要在 VM 被逐出时删除该 VM 和基础磁盘，请将 `--eviction-policy` 设置为 `Delete`。

```azurecli-interactive
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



创建 VM 后，可以通过查询来查看资源组中所有 VM 的最高计费价格。

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>模拟逐出

可以使用 REST、PowerShell 或 CLI 模拟 Azure 现成虚拟机逐出，以测试应用程序对突然逐出的响应。

大多数情况下，需要借助 REST API [虚拟机 - 模拟逐出](/rest/api/compute/virtualmachines/simulateeviction)来自动测试应用程序。 对于 REST，`Response Code: 204` 意味着模拟逐出成功。 可以将模拟逐出与[计划事件服务](scheduled-events.md)相结合，自动测试应用在 VM 被逐出时的响应。

若要了解操作中的计划事件，请观看 [Azure Friday - 使用 Azure Scheduled Events 准备 VM 维护](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)。


### <a name="quick-test"></a>快速测试

若要快速测试以了解模拟逐出的运作情况，我们可通过查询计划事件服务来查看使用 Azure CLI 模拟逐出时的具体情况。

首次对事件发出请求时，要针对服务启用计划事件服务。 

远程登录到 VM，然后打开命令提示符。 

在 VM 的命令提示符处键入：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

第一次响应可能需要长达 2 分钟的时间。 自此之后，几乎会立即显示输出。

在已安装 Azure CLI 的计算机（例如本地计算机）中，使用 [az vm simulate-eviction](/cli/azure/vm#az_vm_simulate_eviction) 模拟逐出。 将资源组名称和 VM 名称替换成自己的名称。 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

如果请求成功完成，则响应输出将为“`Status: Succeeded`”。

快速返回到现成虚拟机的远程连接，再次查询 Scheduled Events 终结点。 重复以下命令，直到获得包含详细信息的输出：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

当计划事件服务获得逐出通知后，响应将如下所示：

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

可以看到 `"EventType":"Preempt"`，资源为 VM 资源 `"Resources":["myspotvm"]`。 

此外，还可以通过查看 `"NotBefore"` 了解 VM 将被逐出的时间 - VM 在该指定时间之前不会被逐出，所以那就是应用程序可以正常关闭的时间窗口。


## <a name="next-steps"></a>后续步骤

此外，还可以通过 [Azure PowerShell](../windows/spot-powershell.md)、[门户](../spot-portal.md)或[模板](spot-template.md)创建 Azure 现成虚拟机。

使用 [Azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices)查询当前的定价信息，了解有关 Azure 现成虚拟机的信息。 `meterName` 和 `skuName` 都包含 `Spot`。

如果遇到错误，请参阅[错误代码](../error-codes-spot.md)。