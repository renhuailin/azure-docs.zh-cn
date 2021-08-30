---
title: Azure 虚拟机的状态和计费
description: 概述 VM 可以进入的各种状态以及何时对用户计费。
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 3d710038c00dff56d4840f7252dcb992dcb1b8c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749268"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Azure 虚拟机的状态和计费

Azure 虚拟机 (VM) 经历的不同状态可以归类为“预配”状态和“电源”状态。 本文旨在介绍这些状态并专门突出显示了何时会对客户收取实例使用费用。 

## <a name="get-states-using-instance-view"></a>使用实例视图获取状态

实例视图 API 提供 VM 运行状态信息。 有关详细信息，请参阅 [Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview)（虚拟机 - 实例视图）API 文档。

Azure 资源浏览器提供一个简单的 UI，用于查看 VM 运行状态：[资源浏览器](https://resources.azure.com/)。

预配状态在 VM 属性和实例视图中可见。 电源状态在 VM 的实例视图中提供。

若要检索订阅中所有 VM 的电源状态，请使用[虚拟机 - 列出所有 API](/rest/api/compute/virtualmachines/listall)，并将参数 statusOnly 设置为 true。

> [!NOTE]
> 将[虚拟机 - 列出所有 API](/rest/api/compute/virtualmachines/listall) 的参数 statusOnly 设置为 true 后，它可以检索订阅中所有 VM 的电源状态。 但在一些罕见的情况下，由于检索过程中的间歇性问题，可能无法获取电源状态。 在这种情况下，我们建议使用同一 API 重试，或者使用 [Azure 资源运行状况](../service-health/resource-health-overview.md)或 [Azure Resource Graph](..//governance/resource-graph/overview.md) 来检查 VM 的电源状态。
 
## <a name="power-states-and-billing"></a>电源状态和计费

电源状态表示 VM 的上一个已知状态。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="图像显示了 VM 可以经历的电源状态的示意图。":::

下表描述每个实例状态并指示是否会对其收取实例使用费用。

| 电源状态 | 说明 | 计费 |  
|---|---|---|
| 正在启动| 虚拟机正在启动。 | 计费 | 
| 运行 | 虚拟机已启动。 这是标准工作状态。 | 计费 | 
| 正在停止 | 这是运行和停止之间的过渡状态。 | 计费| 
|已停止 | 虚拟机已在主机上分配，但未运行。 也称为 PoweredOff 状态或“已停止(已分配)”。 这可能是调用 PowerOff API 操作或从来宾 OS 中调用关闭而导致的。 在 VM 创建期间或从“解除分配”状态启动 VM 时，也可能会暂时看到“已停止”状态。  | 计费 | 
| 正在解除分配 | 这是运行和已解除分配之间的过渡状态。 | 不计费* | 
| 已解除分配 | 虚拟机已解除对基础硬件的租用，并且已完全关闭电源。 这种状态也称为“已停止（已解除分配）”。 | 不计费* | 

&#42; 某些 Azure 资源（如[磁盘](https://azure.microsoft.com/pricing/details/managed-disks)和[网络](https://azure.microsoft.com/pricing/details/bandwidth/)）仍会继续产生费用。


## <a name="provisioning-states"></a>预配状态

预配状态是用户在 VM 上启动的控制平面操作的状态。 以下状态独立于 VM 的电源状态。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="图像显示了 VM 可以经历的预配状态。":::

| 正在设置状态 | 说明 | 电源状态 | 计费 | 
|---|---|---|---|
| 创建 | 创建虚拟机。 | 正在启动 | 不计费* | 
| 更新 | 更新现有虚拟机的模型。 对虚拟机的一些非模型更改，如启动和重启，都属于更新状态。 | 运行 | 计费 | 
| 删除 | 删除虚拟机。 | 正在解除分配 | 不计费* |
| 释放 | 虚拟机完全停止并从基础主机中删除。 可以将虚拟机的解除分配视为一种更新，因此它会显示与更新类似的预配状态。 | 正在解除分配 | 不计费* | 

&#42; 某些 Azure 资源（如[磁盘](https://azure.microsoft.com/pricing/details/managed-disks)和[网络](https://azure.microsoft.com/pricing/details/bandwidth/)）仍会继续产生费用。

## <a name="os-provisioning-states"></a>OS 预配状态
OS 预配状态仅适用于使用 OS 映像创建的虚拟机。 专用映像将不会显示这些状态。 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="图像显示了 VM 可以经历的 OS 预配状态。":::

| OS 预配状态 | 说明 | 电源状态 | 计费 | 
|---|---|---|---|
| OSProvisioningInProgress | VM 正在运行，并且正在安装来宾 OS。 | 运行 | 计费 | 
| OSProvisioningComplete | 这是一种短暂的状态。 虚拟机将从此状态快速转换为“成功”。 如果仍在安装扩展，你将继续看到此状态，直到安装完成。 | 运行 | 计费 | 
| 成功 | 用户发起的操作已完成。 | 运行 | 计费 | 
| 失败 | 表示操作失败。 有关详细信息和可能的解决方案，请参阅错误代码。 | 运行  | 计费 | 


## <a name="next-steps"></a>后续步骤
- 请参阅 [Azure 成本管理和计费文档](../cost-management-billing/index.yml)
- 使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)来规划你的部署。
- 若要详细了解如何监视 VM，请参阅[在 Azure 中监视虚拟机](../azure-monitor/vm/monitor-vm-azure.md)。
