---
title: 了解 Azure 虚拟机预留实例折扣
description: 了解如何将 Azure 虚拟机预留实例折扣应用于正在运行的虚拟机。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 44c3966b429df61539bb86d7d0191ee0eae48e59
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675262"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>如何将 Azure 预留折扣应用于虚拟机

购买 Azure 虚拟机预留实例后，预留折扣将自动应用于与预留的属性和数量匹配的虚拟机。 预留涵盖虚拟机的计算成本。

预留折扣适用于从 Azure 市场购买的基础 VM。

有关 SQL 数据库保留容量，请参阅[了解 Azure 预留实例折扣](../reservations/understand-reservation-charges.md)。

下表说明了购买虚拟机预留实例后的虚拟机成本。 在任何情况下，均按标准费率收取存储和网络费用。

| 虚拟机类型  | 通过虚拟机预留实例收费 |
|-----------------------|--------------------------------------------|
|未安装其他软件的 Linux VM | 预留涵盖 VM 基础结构成本。|
|具有软件费用的 Linux VM（例如，Red Hat） | 预留涵盖基础结构成本。 将收取其他软件费用。|
|未安装其他软件的 Windows VM |预留涵盖基础结构成本。 将收取 Windows 软件费用。|
|安装了其他软件（例如，SQL server）的 Windows VM | 预留涵盖基础结构成本。 将收取 Windows 软件和其他软件费用。|
|具有 [Azure 混合权益](../../virtual-machines/windows/hybrid-use-benefit-licensing.md)的 Windows VM | 预留涵盖基础结构成本。 Azure 混合权益涵盖 Windows 软件成本。 其他任何软件都是单独收费的。|

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源或缩放 VM 数时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

## <a name="reservation-discount-for-non-windows-vms"></a>非 Windows VM 的预留折扣

 Azure 预留折扣按小时应用于正在运行的 VM 实例。 已购买的预订将与正在运行的 VM 所发送的使用情况信息进行匹配以应用预订折扣。 对于可能无法运行整个小时的 VM，将从其他未使用预订的 VM（包括同时运行的 VM）填充预订。 在一个小时结束时，将锁定该小时内的 VM 预留应用程序。 如果 VM 未运行一小时或该小时内同时运行的 VM 未填充该小时的预留，则该小时的预留未充分利用。 下图说明了如何将预订应用于应计费的 VM 使用量。 该说明基于一次预订购买和两个匹配的 VM 实例。

![一个已应用预留和两个匹配的 VM 实例的屏幕截图](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. 超出预留范围的任何使用量将按常规即用即付费率进行收费。 对于预留范围内的任何使用量不收取任何费用，因为这些使用量已在购买预留时付费。
2. 在第 1 个小时内，实例 1 运行了 0.75 小时，实例 2 运行了 0.5 小时。 第 1 个小时的总体使用情况为 1.25 小时。 将按即用即付费率收取剩余 0.25 小时的费用。
3. 在第 2 个小时和第 3 个小时内，这两个实例都各运行了 1 小时。 一个实例的费用由预订费用涵盖，按即用即付费率对另一个实例收费。
4. 在第 4 个小时内，实例 1 运行了 0.5 小时，实例 2 运行了 1 小时。 预订费用完全涵盖了实例 1 的费用，并涵盖了实例 2 的 0.5 小时费用。 将按即用即付费率收取剩余 0.5 小时的费用。

要了解 Azure 预留应用情况并在计费使用情况报告中查看该信息，请参阅[了解预留使用情况](../reservations/understand-reserved-instance-usage-ea.md)。

## <a name="reservation-discount-for-windows-vms"></a>Windows VM 的预留折扣

正在运行 Windows VM 实例时，将应用预留以涵盖基础结构成本。 对 Windows VM 的 VM 基础结构成本应用预订与对非 Windows VM 的 VM 基础结构成本应用预订相同。 将按 vCPU 对 Windows 软件单独收费。 请参阅 [Windows 软件的预留成本](../reservations/reserved-instance-windows-software-costs.md)。 可以利用 [Windows Server 的 Azure 混合权益](../../virtual-machines/windows/hybrid-use-benefit-licensing.md)来涵盖 Windows 许可费用。

## <a name="discount-can-apply-to-different-sizes"></a>折扣适用于不同大小

如果购买预留 VM 实例，并选择“针对实例大小灵活性进行优化”，则折扣覆盖范围适用于所选择的 VM 大小。 它还适用于相同系列实例大小灵活性组中的其他 VM 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../../virtual-machines/reserved-vm-instance-size-flexibility.md)。

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>高级存储 VM 无法获得非高级折扣

下面是一个示例。 假设你购买了五个 Standard_D1 VM 的预留，则预留折扣仅适用于 Standard_D1 VM 或同一实例系列中的其他 VM。 此折扣不适用于 Standard_DS1 VM 或 DS1 实例大小灵活性组中的其他大小。

应用预留折扣时会忽略用于 VM 的计量，仅评估 ServiceType。 查看 `AdditionalInfo` 中的 `ServiceType` 值，确定 VM 的实例灵活性组/系列信息。 这些值位于使用情况 CSV 文件中。

在购买后，不能直接更改预留的实例灵活性组/系列。 但是，可以将一个 VM 预留从一个实例灵活性组/系列交换到另一个实例灵活性组/系列。

## <a name="services-that-get-vm-reservation-discounts"></a>可获得 VM 保留折扣的服务

VM 预留可以应用于从多个服务发出的 VM 使用量 - 而不仅应用于 VM 部署。 可获得预留折扣的资源会根据实例大小灵活性设置而变化。

### <a name="instance-size-flexibility-setting"></a>实例大小灵活性设置

实例大小灵活性设置确定哪些服务可获得预留实例折扣。

无论该设置是打开还是关闭，预留折扣在 *ConsumedService* 为 `Microsoft.Compute` 时会自动应用到任何匹配的 VM 使用量。 因此，请检查使用情况数据中的 ConsumedService 值。 示例包括：

- 虚拟机
- 虚拟机规模集
- 容器服务
- Azure Batch 部署（在用户订阅模式下）
- Azure Kubernetes 服务 (AKS)
- Service Fabric

打开此设置后，当 ConsumedService 为以下任一项时，预留折扣将自动应用于匹配的 VM 使用量：

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

检查使用情况数据中的 ConsumedService 值，以确定该使用是否符合预留折扣的条件。

有关实例大小灵活性的详细信息，请参阅[虚拟机预留实例的虚拟机大小灵活性](../../virtual-machines/reserved-vm-instance-size-flexibility.md)。


## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](../reservations/save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../../virtual-machines/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../../azure-sql/database/reserved-capacity-overview.md)
- [管理 Azure 预留](../reservations/manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](../reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../reservations/understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)
- [预留未包含的 Windows 软件成本](../reservations/reserved-instance-windows-software-costs.md)
