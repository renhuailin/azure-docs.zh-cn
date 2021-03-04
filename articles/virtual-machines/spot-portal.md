---
title: 使用门户部署 Azure 点虚拟机
description: 如何使用 Azure PowerShell 部署点虚拟机以节省成本。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098564"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>使用 Azure 门户部署 Azure 点虚拟机

使用 [Azure 点虚拟机](spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出 Azure 点虚拟机。 因此，Azure 点虚拟机非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，Azure 点虚拟机的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 有关设置最大价格的详细信息，请参阅 [Azure 点虚拟机-价格](spot-vms.md#pricing)。

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位，将 Azure 点虚拟机的最大价格设置为美元 (USD) 。 例如，该值的 `0.05701` 最大价格为 $0.05701 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。

收回 VM 后，你可以选择删除 VM 和基础磁盘，或解除分配 VM，以便以后可以重新启动 vm。


## <a name="create-the-vm"></a>创建 VM

部署 VM 时，可以选择使用 Azure 点实例。


在 " **基本** 信息" 选项卡上的 " **实例详细信息** " 部分中， **不** 是使用 Azure 点实例的默认值。

![屏幕截图选择 "否，不使用 Azure 点实例"](./media/spot-portal/no.png)

如果选择 **"是"**，该部分将展开，你可以选择 [逐出类型和逐出策略](spot-vms.md#eviction-policy)。 

![用于选择 "是" 的屏幕截图，使用 Azure 点实例](./media/spot-portal/yes.png)

您还可以通过选择 " **查看定价历史记录" 和 "比较附近地区的价格**"，将定价和逐出率与其他类似的区域进行比较。

在此示例中，加拿大中部地区的成本较低，且逐出率低于美国东部区域。

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="区域选项的屏幕截图，其中的价格和逐出率不同。":::

您可以通过选择最适合您的选项来更改区域，然后选择 **"确定"**。

## <a name="simulate-an-eviction"></a>模拟逐出

可以 [模拟逐出](/rest/api/compute/virtualmachines/simulateeviction) Azure 点虚拟机，以测试应用程序对突然逐出的响应情况。 

将以下内容替换为你的信息： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` 表示模拟逐出成功。 

## <a name="next-steps"></a>后续步骤

你还可以使用 [PowerShell](./windows/spot-powershell.md)、 [CLI](./linux/spot-cli.md)或 [模板](./linux/spot-template.md)创建 Azure 点虚拟机。
