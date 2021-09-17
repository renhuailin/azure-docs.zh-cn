---
title: 使用 Azure 现成虚拟机
description: 了解如何使用 Azure 现成虚拟机节省成本。
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: ce20b7815c89a45a2677029a28fd205499751e30
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695397"
---
# <a name="use-azure-spot-virtual-machines"></a>使用 Azure 现成虚拟机 

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

使用 Azure 现成虚拟机，可以利用未使用的容量，大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Azure 现成虚拟机。 因此，Azure 现成虚拟机非常适用于那些处理服务中断的工作负荷（例如批处理作业）、开发/测试环境、大型计算工作负荷等。

可用容量可能因大小、区域、一天内的时间等因素而异。 部署 Azure 现成虚拟机时，如果有可用的容量，则 Azure 会分配 VM，但这些 VM 没有 SLA 的保障。 Azure 现成虚拟机不提供高可用性保证。 每当 Azure 需要回收容量时，Azure 基础结构就会发出 30 秒的通知，然后逐出 Azure 现成虚拟机。 


## <a name="eviction-policy"></a>逐出策略

可以根据你设置的容量或最高价格逐出 VM。 创建 Azure 现成虚拟机时，可将逐出策略设置为“解除分配”（默认值）或“删除” 。 

“解除分配”策略可将 VM 移到已停止解除分配的状态，以允许后续进行重新部署。 但是，不保证分配将成功。 已解除分配的 VM 将计入配额，你需要支付基础磁盘的存储费用。 

如果希望在逐出 VM 后将其删除，可以将逐出策略设置为删除。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免继续支付存储费用。 

可以选择通过 [Azure Scheduled Events](./linux/scheduled-events.md) 来接收 VM 内通知。 这样，系统就会在你的 VM 被逐出时向你发送通知。在逐出之前，你将有 30 秒的时间来完成任何作业并执行关闭任务。 


| 选项 | 业务成效 |
|--------|---------|
| 最高价格设置为 >= 当前价格。 | 如果有可用的容量和配额，则会部署 VM。 |
| 最高价格设置为 < 当前价格。 | 不会部署 VM。 你将收到一条错误消息，指出最高价格需要 >= 当前价格。 |
| 如果最大价格 >= 当前价格，则重启已停止/已解除分配的 VM | 如果有容量和配额，则会部署 VM。 |
| 如果最大价格 < 当前价格，则重启已停止/已解除分配的 VM | 你将收到一条错误消息，指出最高价格需要 >= 当前价格。 | 
| VM 的价格已提高，现在 > 最高价格。 | 将逐出 VM。 在实际逐出之前的 30 秒，你会收到通知。 | 
| 逐出后，VM 的价格将回落到最高价格以下。 | VM 不会自动重启。 你可以自行重启 VM，这会按当前价格计费。 |
| 如果最高价格设置为 `-1` | 不会出于定价原因而逐出 VM。 最高价格将是当前价格，最高为标准 VM 的价格。 永远不会以超过标准价格的价格向你收费。| 
| 更改最高价格 | 需要解除分配 VM 才能更改最高价格。 解除分配 VM，设置新的最高价格，然后更新 VM。 |


## <a name="limitations"></a>限制

Azure 现成虚拟机不支持以下 VM 大小：
 - B 系列
 - 任意大小的促销版本（例如 Dv2、NV、NC、H 促销大小）

Azure 现成虚拟机可以部署到除 Microsoft Azure 中国世纪互联以外的任何区域。

<a name="channel"></a>

当前支持以下[套餐类型](https://azure.microsoft.com/support/legal/offer-details/)：

-   企业协议 
-   即用即付套餐代码 (003P)
-   赞助（0036P 和 0136P）
- 对于云服务提供商 (CSP)，请联系合作伙伴


## <a name="pricing"></a>定价

Azure 现成虚拟机的定价因地区和 SKU 而异。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的 VM 定价。 

你还可以通过使用 [Azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查询 Spot 定价信息来查询定价信息。 `meterName` 和 `skuName` 都会包含 `Spot`。

使用可变定价，你可以设置最高价格，以美元 (USD) 为单位，最多可使用 5 个小数位。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出 VM。 VM 的价格将是 Spot 的当前价格或是标准 VM 的价格（两者中的较低者，前提是有可用的容量和配额）。

## <a name="pricing-and-eviction-history"></a>定价和逐出历史记录

你可以在门户中的某个区域查看每个大小的历史定价和逐出速率。 选择“查看定价历史记录并比较附近区域的价格”，查看特定大小的定价图或定价表。  下图中的定价和逐出速率只是示例。 

**图表**：

:::image type="content" source="./media/spot-chart.png" alt-text="地区选项的屏幕截图，其中以图表形式显示了不同的定价和逐出速率。":::

**Table**：

:::image type="content" source="./media/spot-table.png" alt-text="地区选项的屏幕截图，其中以表形式显示了不同的定价和逐出速率。":::



##  <a name="frequently-asked-questions"></a>常见问题

**问**：创建后，Azure 现成虚拟机是否与常规标准 VM 相同？

**答**：是，但 Azure 现成虚拟机没有 SLA，可以随时将其逐出。


**问：** 当被逐出但仍然需要容量时，该怎么办？

**答**：如果马上需要容量，建议使用标准 VM，而不要使用 Azure 现成虚拟机。


**问**：如何为 Azure 现成虚拟机管理配额？

**答**：Azure 现成虚拟机将具有单独的配额池。 将在 VM 与规模集实例之间共享 Spot 配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。


**问**：是否可以为 Azure 现成虚拟机申请额外的配额？

**答**：是，可以通过 [标准配额申请流程](../azure-portal/supportability/per-vm-quota-requests.md)提交申请，请求提高 Azure 现成虚拟机的配额。


**问：** 我可以在何处发布问题？

**答:** 你可以在 [问答](/answers/topics/azure-spot.html)中发布问题并使用 `azure-spot` 来标记问题。 


**问**：如何更改现成 VM 的最高价格？

**答**：需先解除分配 VM，然后才能更改最高价格。 然后，可以在 VM 的“配置”部分更改门户中的最高价格。 

## <a name="next-steps"></a>后续步骤
使用 [CLI](./linux/spot-cli.md)、[门户](spot-portal.md)、[ARM 模板](./linux/spot-template.md)或 [PowerShell](./windows/spot-powershell.md) 部署 Azure 现成虚拟机。

你还可以部署[具有 Azure 现成虚拟机实例的规模集](../virtual-machine-scale-sets/use-spot.md)。

如果遇到错误，请参阅[错误代码](./error-codes-spot.md)。
