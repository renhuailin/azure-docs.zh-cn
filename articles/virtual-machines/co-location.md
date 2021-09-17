---
title: 邻近放置组
description: 了解如何在 Azure 中使用邻近放置组。
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 7693bc23e68880346c09d3c55c84858283c735ba
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696595"
---
# <a name="proximity-placement-groups"></a>邻近放置组

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

将 VM 置于单个区域可以减少实例之间的物理距离。 将它们置于单一可用性区域中也会减少它们的物理距离。 但是，随着 Azure 占用空间的增加，单个可用性区域可能会跨多个物理数据中心，这可能导致网络延迟，对应用程序造成影响。 

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将 VM 部署到一个邻近放置组中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。

- 独立 VM 之间的低延迟。
- 单个可用性集或虚拟机规模集中的 VM 之间的低延迟。 
- 独立 VM、多个可用性集或多个规模集中的 VM 之间的低延迟。 单个放置组中可以有多个计算资源，形成一个多层应用程序。 
- 多个使用不同硬件类型的应用程序之间的低延迟。 例如，运行在可用性集中使用 M 系列的后端，以及在单个邻近放置组的规模集的 D 系列实例上的前端。

![邻近放置组的插图](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用邻近放置组 

Azure 邻近放置组是 Azure 中的一种资源。 在将邻近放置组用于其他资源之前，需要先创建邻近放置组。 创建后，可将其用于虚拟机、可用性集或虚拟机规模集。 创建计算资源时，可以通过提供邻近放置组 ID 来指定邻近放置组。 

还可将现有资源移入邻近放置组。 将资源移入邻近放置组时，应该先停止（解除分配）资产，因为可能需要将资产重新部署到区域中的另一数据中心以满足共置约束。 

对于可用性集和虚拟机规模集，应在资源级别而不是在单个虚拟机上设置邻近放置组。 

邻近放置组是一种共置约束，而不是一种固定机制。 它会与要使用它的第一个资源的部署一起固定到特定的数据中心。 停止（解除分配）或删除使用邻近放置组的所有资源后，邻近放置组将不再固定。 因此，在将一个邻近放置组用于多个 VM 系列时，必须尽可能地提前在模板中指定全部所需的类型，或者按照可以提高成功部署几率的部署顺序来进行。 如果部署失败，请使用已失败的第一个待部署 VM 大小重新开始部署。

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>使用邻近放置组时预期会发生的情况 
邻近放置组提供在同一数据中心内的归置。 但是，因为邻近放置组提供一个附加的部署约束，因此可能会发生分配失败。 在少数用例中，使用邻近放置组时你可能会遇到分配失败：

- 当你请求邻近放置组中的第一个虚拟机时，会自动选择数据中心。 在某些情况下，当请求另一个虚拟机 SKU 时，如果它在该数据中心内不存在，则该请求可能会失败。 在这种情况下，将返回 OverconstrainedAllocationRequest 错误  。 为了避免这种情况，请尝试更改 SKU 的部署顺序，或者使用单个 ARM 模板部署这两个资源。
-   对于你在其中添加和删除 VM 实例的弹性工作负荷，在部署上施加邻近放置组约束可能会导致去满足请求时失败，进而导致 AllocationFailure 错误  。 
- 根据需要停止（解除分配）再启动 VM 是实现弹性的另一种方式。 由于停止（解除分配）VM 后不会保留容量，因此再次启动该 VM 可能会导致 AllocationFailure 错误  。

## <a name="planned-maintenance-and-proximity-placement-groups"></a>计划内维护和邻近放置组

计划内维护事件（如 Azure 数据中心的硬件解除）可能会影响邻近放置组中资源的对齐情况。 资源可转移到其他数据中心，这会干扰与邻近放置组相关的归置和延迟预期。

### <a name="check-the-alignment-status"></a>检查对齐状态

可以执行以下操作来检查邻近放置组的对齐状态。

- 可以使用门户、CLI 和 PowerShell 查看邻近放置组的归置状态。

    -   使用 PowerShell 时，可以使用 Get-AzProximityPlacementGroup cmdlet 并通过包含可选参数“-ColocationStatus”来获取归置状态。

    -   使用 CLI 时，可以使用 `az ppg show` 并通过包含可选参数“--include-colocation-status”来获取归置状态。

- 对于每个邻近放置组，“归置状态”属性提供了分组资源的当前对齐状态摘要。 

    - 对齐：资源在邻近放置组的相同延迟信封内。

    - 未知：至少已解除分配了一个 VM 资源。 成功启动它们后，状态应返回到“对齐”。

    - 未对齐：至少一个 VM 资源未与邻近放置组对齐。 未对齐的特定资源也会在成员资格部分中单独调出

- 对于可用性集，可以在“可用性集概述”页中查看有关各个 VM 的对齐信息。

- 对于规模集，可在规模集“概述”页的“实例”选项卡中查看有关各个实例的对齐信息 。 

### <a name="re-align-resources"></a>重新对齐资源 

如果邻近放置组为 `Not Aligned`，则可以停止\解除分配，然后重新启动受影响的资源。 如果 VM 位于可用性集或规模集中，则必须先停止\解除分配可用性集或规模集中的所有 VM，然后再重新启动它们。

如果由于部署约束而导致分配失败，则可能需要先停止\解除分配受影响的邻近放置组中的所有资源（包括对齐的资源），然后重新启动它们以恢复对齐。

## <a name="best-practices"></a>最佳做法 
- 若要实现最低的延迟，请将邻近放置组与加速网络一起使用。 有关详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](../virtual-network/create-vm-accelerated-networking-cli.md)或[创建具有加速网络的 Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)。
- 在单个模板中部署所有 VM 大小。 为了避免使用不支持所需的全部 VM SKU 和大小的硬件，请将所有应用层包括在单个模板中，以便同时部署它们。
- 如果使用 PowerShell、CLI 或 SDK 来编写部署脚本，则可能会出现分配错误 `OverconstrainedAllocationRequest`。 在这种情况下，应停止/解除分配所有现有的 VM，并更改部署脚本中的顺序，从失败的 VM SKU/大小开始。 
- 重复使用现有的放置组时，如果系统正在删除其中的 VM，请等待删除操作全部完成再向该组添加 VM。
- 如果延迟是首要考虑的事项，请将 VM 置于邻近放置组中，将整个解决方案置于一个可用性区域中。 但是，如果复原能力是首要考虑的事项，请将实例分散到多个可用性区域（单个邻近放置组不能跨多个区域）。

## <a name="next-steps"></a>后续步骤

- 使用 [Azure CLI](./linux/proximity-placement-groups.md) 或 [PowerShell](./windows/proximity-placement-groups.md) 将 VM 部署到邻近放置组。
- 了解如何[测试网络延迟](../virtual-network/virtual-network-test-latency.md)。
- 了解如何[优化网络吞吐量](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
- 了解如何[将邻近放置组用于 SAP 应用程序](./workloads/sap/sap-proximity-placement-scenarios.md)。
