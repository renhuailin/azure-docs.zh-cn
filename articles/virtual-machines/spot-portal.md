---
title: 使用门户部署 Azure 现成虚拟机
description: 如何使用门户部署现成虚拟机
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: a80356cf7045fc079e71d429f89f3b4b0fff31ba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694373"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>使用 Azure 门户部署 Azure 现成虚拟机

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

使用 [Azure 现成虚拟机](spot-vms.md)，可以利用未使用的容量，大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Azure 现成虚拟机。 因此，Azure 现成虚拟机非常适用于那些处理服务中断的工作负荷（例如批处理作业）、开发/测试环境、大型计算工作负荷等。

Azure 现成虚拟机的定价因地区和 SKU 而异。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的 VM 定价。 若要详细了解如何设置最高价格，请参阅 [Azure 现成虚拟机 - 定价](spot-vms.md#pricing)。

可以选择对 VM 设置你愿意支付的每小时最高价格。 Azure 现成虚拟机的最高价格可以美元 (USD) 形式设置，最多保留 5 位小数。 例如，值 `0.05701` 表示最高价格为 0.05701 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出 VM。 VM 的价格将是 Spot 的当前价格或是标准 VM 的价格（两者中的较低者，前提是有可用的容量和配额）。

逐出 VM 后，可以选择删除 VM 和基础磁盘，也可以解除分配 VM，以便之后可重启它。


## <a name="create-the-vm"></a>创建 VM

部署 VM 时，可以选择使用 Azure 现成实例。


在“基本信息”选项卡的“实例详细信息”部分，对于是否表示使用 Azure 现成实例，“否”是默认值  。

![选择“否”表示不使用 Azure 现成实例的屏幕截图](./media/spot-portal/no.png)

如果选择“是”，则会展开此部分，然后可选择[逐出类型和逐出策略](spot-vms.md#eviction-policy)。 

![选择“是”表示使用 Azure 现成实例的屏幕截图](./media/spot-portal/yes.png)

还可以通过选择“查看定价历史记录并比较附近区域的价格”，将定价和逐出速率与其他类似地区进行比较。

在此示例中，与美国东部地区相比，加拿大中部地区的定价更低，且逐出速率也更低。

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="地区选项的屏幕截图，其中显示了不同的定价和逐出速率。":::

可通过选择最适合你的选项，然后选择“确定”来更改地区。

## <a name="simulate-an-eviction"></a>模拟逐出

可以对 Azure 现成虚拟机[模拟逐出](/rest/api/compute/virtualmachines/simulateeviction)，以测试应用程序对突然逐出的响应情况。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` 意味着模拟逐出成功。 

## <a name="next-steps"></a>后续步骤

还可以使用 [PowerShell](./windows/spot-powershell.md)、[CLI](./linux/spot-cli.md) 或[模板](./linux/spot-template.md)创建 Azure 现成虚拟机。
