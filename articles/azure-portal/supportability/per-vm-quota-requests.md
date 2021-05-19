---
title: 请求增加每个 Azure VM 系列的 vCPU 配额上限
description: 如何在 Azure 门户中请求增加 VM 系列的 vCPU 配额上限，这会将区域 vCPU 的总数上限增加相同数量。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745428"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>标准配额：按 VM 系列提高上限

Azure 资源管理器针对虚拟机支持两种类型的 vCPU 配额：

* *即用即付 VM* 和 *保留 VM 实例* 受 *标准 vCPU 配额* 的上限。
* *现成 VM* 受 *现成 vCPU 配额* 的上限。

对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留虚拟机实例的标准 vCPU 配额：

* 第一层是所有 VM 系列的“区域 vCPU 总数”限制。
* 第二层是“每个 VM 系列 vCPU 限制”，如 Dv3 系列的 vCPU。

每次部署新的现成 VM 时，所有现成 VM 实例的现有和新 vCPU 使用量不得超过已批准的现成 vCPU 配额上限。 如果超出了现成配额，则不允许使用现成 VM 部署。

可以通过使用 Azure 门户来请求提高 VM 系列的 vCPU 配额上限。 VM 序列配额增加会自动为“区域 vCPU 总数”限制增加相同的数量。

要了解有关标准 vCPU 配额的更多信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 [Azure 订阅和服务限制](./classic-deployment-model-quota-increase-requests.md)。

要了解有关增加标准配额的按区域 vCPU 上限的信息，请参阅[标准配额：按区域增加上限](regional-quota-requests.md)。

要了解有关现成 VM vCPU 上限的更多信息，请参阅[现成配额：增加所有 VM 系列的限制](low-priority-quota.md)。

可以通过两种方式中的任一方式请求提高每个 VM 系列的标准 vCPU 配额上限，如以下部分中所述。

## <a name="request-a-standard-quota-increase-from-help--support"></a>从“帮助 + 支持”请求标准配额增加

要通过“帮助 + 支持”请求为每个 VM 系列增加标准 vCPU 配额，请执行以下操作：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额上限。 有关详细信息，请参阅步骤 8。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助 + 支持”。

   ![“帮助 + 支持”链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在“帮助 + 支持”中，选择“新建支持请求” 。

    ![创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)” 。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于“订阅”，请选择要增加其配额的订阅。

   ![选择已增加配额的订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于“配额类型”，选择“计算 - VM (cores-vCPUs) 订阅限制增加”。 

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择“下一步: 解决方案”以打开“问题详细信息”。  选择“提供详细信息”以输入其他信息。

   ![“提供详细信息”链接](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在“配额详细信息”中，执行以下步骤：

   ![提供配额的其他详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于“部署模型”，选择相应的模型。

   1. 对于“位置”，请选择一个位置。 对于选定位置，在“类型”下的“选择类型”中，选择“标准”。  

      ![配额详细信息 - 配额类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在“类型”下，你可以通过多选支持从单个支持案例请求标准和现成配额类型。

      有关增加现成配额限制的详细信息，请参阅[适用于虚拟机规模集的 Azure Spot VM](../../virtual-machine-scale-sets/use-spot.md)。

   1. 在“标准”中，选择增加配额的 SKU 系列。

      ![配额详细信息 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入要用于此订阅的新配额限制。 要从列表中删除 SKU，请清除 SKU 旁边的复选框，或选择丢弃图标“X”。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要为多个位置请求增加配额，请在“位置”中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的上限。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择“保存并继续”以继续创建支持请求。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>从“订阅”请求标准配额增加

要通过“订阅”请求为每个 VM 系列增加标准 vCPU 配额，请执行以下操作：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额上限。 有关详细信息，请参阅步骤 7。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“订阅”。

   ![Azure 门户搜索中的订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选定进行更改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左侧窗格中，选择“使用量 + 配额”。

   ![“使用量 + 配额”链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角选择“请求增加”。

   ![选择以增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 对于“配额类型”，选择“计算 - VM (cores-vCPUs) 订阅限制增加”。 

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在“配额详细信息”中，执行以下步骤：

   1. 对于“部署模型”，选择适当的模型，对于“位置”，选择一个位置。 

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于选定位置，在“类型”下，选择“选择类型”，然后选择“标准”。  

      ![选择“标准”类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在“类型”下，你可以通过多选支持从单个支持案例请求标准和现成配额类型。

      有关增加现成配额限制的详细信息，请参阅[适用于虚拟机规模集的 Azure Spot VM](../../virtual-machine-scale-sets/use-spot.md)。

   1. 对于“标准”，选择要增加其配额的 SKU 系列。

      ![配额详细信息 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入要用于此订阅的新配额限制。 要从列表中删除 SKU，请取消选中 SKU 旁边的复选框，或选择废弃图标“X”。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要为多个位置请求增加配额，请在“位置”中选择其他位置，然后选择适当的 VM 类型。

   此步骤预加载你为之前的位置选择的 SKU 系列。 输入要应用于其他序列的配额限制。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择“保存并继续”以继续创建支持请求。