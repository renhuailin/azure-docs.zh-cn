---
title: 现成虚拟机配额
description: 增加现成虚拟机的配额上限，这提供了 Azure 使用量模型，可让你承担较低的费用，代价是让 Azure 根据需要删除虚拟机。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745479"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>现成配额：增加所有 VM 系列的上限

现成虚拟机 (VM) 提供了不同的 Azure 使用量模型。 现成虚拟机让你承担更低的成本，代价是允许 Azure 根据需要，按照按需付费或保留 VM 实例部署删除虚拟机。 有关现成 VM 的详细信息，请参阅[用于虚拟机规模集的 Azure 现成 VM](../../virtual-machine-scale-sets/use-spot.md)。

Azure 资源管理器针对虚拟机支持两种类型的 vCPU 配额：

* *即用即付 VM* 和 *保留 VM 实例* 受 *标准 vCPU 配额* 的上限。
* *现成 VM* 受 *现成 vCPU 配额* 的上限。

对于现成 vCPU 配额类型，将在所有可用虚拟机系列中强制执行Resource Manager vCPU 配额作为单个区域上限。

每次部署新的现成 VM 时，所有现成 VM 实例的现有和新 vCPU 使用量不得超过已批准的现成 vCPU 配额上限。 如果超出了现成配额，则不允许使用现成 VM 部署。

本文介绍如何使用 Azure 门户请求增加现成 vCPU 配额上限。

要了解有关标准 vCPU 配额的更多信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

要了解有关按区域增加 vCPU 上限的信息，请参阅[标准配额：按区域增加上限](regional-quota-requests.md)。

## <a name="request-a-quota-limit-increase-from-help--support"></a>通过“帮助 + 支持”请求增加配额上限

使用“帮助 + 支持”为所有虚拟机系列请求增加现成配额上限：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额上限。 有关详细信息，请参阅步骤 8。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助和支持”。

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

   1. 对于“部署模型”，选择适当的模型，对于“位置”，选择一个位置。 

      ![提供配额的其他详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于选定位置，在“类型”下的“选择类型”中，选择“现成”。  

      ![选择现成类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

       在“类型”下，你可以通过多选支持从单个支持案例请求标准和现成配额类型。

       有关详细信息，请参阅[标准配额：按 VM 序列增加上限](per-vm-quota-requests.md)。

   1. 输入要用于此订阅的新配额限制。

      ![为现成 VM 选择新配额](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要为多个位置请求增加配额，请在“位置”中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的上限。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择“保存并继续”以继续创建支持请求。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>从“订阅”窗格中请求增加配额上限

要从“订阅”窗格中请求增加所有 VM 系列的现成配额上限，请执行以下操作：

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

1. 选择“下一步: 解决方案”以打开“问题详细信息”。  选择“提供详细信息”以输入其他信息。 在“配额详细信息”中，输入以下信息：

   1. 对于“部署模型”，选择适当的模型，对于“位置”，选择一个位置。 

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于选定位置，在“类型”下的“选择类型”中，选择“现成”。  

      ![选择“现成”类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

      有关详细信息，请参阅[标准配额：按 VM 序列增加上限](per-vm-quota-requests.md)。

   1. 输入要用于此订阅的新配额限制。

      ![为 vCPU 限制输入新值](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要为多个位置请求增加配额，请在“位置”中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的上限。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择“保存并继续”以继续创建支持请求。
