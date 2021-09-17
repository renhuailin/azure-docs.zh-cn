---
title: 预付 Azure 专用主机以节省资金
description: 了解如何购买 Azure 专用主机预留实例以节省计算成本。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 464ffc8b5d4d04aeb7e4013f5b25d240d5ee9b06
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688961"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>通过 Azure 专用主机预留来节省成本

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

当提交到 Azure 专用主机的预留实例时，可以节省资金。 预留折扣会自动应用到正在运行的专用主机，具体数量需与预留范围和属性相匹配。 无需向专用主机分配预留，即可享受折扣。 购买的预留实例仅涵盖使用过程中的计算部分，并包括软件许可成本。 请参阅[适用于虚拟机的 Azure 专用主机概述](./dedicated-hosts.md)。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>购买之前，先确定适当的专用主机 SKU


在购买预留之前，应先确定所需的专用主机。 为专用主机定义 SKU，以指示 VM 系列和类型。 

首先，检查受支持的 [Windows 虚拟机](./sizes.md)或 [Linux 虚拟机](./sizes.md)大小，以确定 VM 系列。

然后，检查其在 Azure 专用主机上是否受支持。 [Azure 专用主机定价](https://aka.ms/ADHPricing)页提供了专用主机 SKU 及其 CPU 信息和各种定价选项（包括预留实例）的完整列表。

可能存在多个 SKU 都支持所选 VM 系列（分属不同类型）的情况。 通过比较主机容量（vCPU 的数量），确定最佳 SKU。 请注意，预留可以应用于支持同一 VM 系列的多个专用主机 SKU（例如 DSv3_Type1 和 DSv3_Type2），但是不能跨不同 VM 系列（例如 DSv3 和 ESv3）。



## <a name="purchase-restriction-considerations"></a>购买限制注意事项

预留实例适用于大多数专用主机大小，但也有一些例外。

预留折扣不适用于以下情况：

- **云** - 不能在德国或中国区域中购买预留。

- **配额不足** - 范围仅限于单个订阅的预留必须在订阅中具有充足的可用 vCPU 配额才能获得新的预留实例。 例如，目标订阅的配额限制为 10 个 vCPU（适用于 DSv3 系列），则不能再购买支持此系列的预留专用主机。 预留的配额检查包括已部署在订阅中的 VM 和专用主机。 可以[创建配额增加请求](../azure-portal/supportability/resource-manager-core-quotas-request.md)以解决此问题。

- **容量限制** - 在极少数情况下，由于某个区域的容量不足，Azure 会限制为专用主机 SKU 的子集购买新的预留。

## <a name="buy-a-reservation"></a>购买预留项

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure 专用主机实例的预留实例。

通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留专用主机实例时需满足以下要求：

- 你必须具有至少一个 EA 订阅或采用即用即付费率的订阅的所有者角色。

- 对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。

- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务”\>“预留”。

3. 选择“添加”以购买新的预留，然后单击“专用主机” 。

4. 输入必填字段。 与所选属性相匹配的正在运行的专用主机实例有资格获得预订折扣。 获得折扣的专用主机实例的实际数目取决于所选范围和数量。

如果你有 EA 协议，则可使用“添加更多选项”来快速添加其他实例。 此选项不可用于其他订阅类型。

| 字段           | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 订阅        | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或采用即用即付费率的个人订阅中担任所有者角色（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）。 从 Azure 预付款（以前称为货币承诺）余额中扣除费用（如果可用）或按超额收取费用。 对于采用即用即付费率的订阅，将向订阅的信用卡或发票付款方式收取费用。 |
| 范围               | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 区域              | 预订涵盖的 Azure 区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 专用主机大小 | 专用主机实例的大小。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 术语                | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 数量            | 预订中购买的实例数。 数量是指可以获得计费折扣的正在运行的专用主机实例数。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。

- **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。

- **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

## <a name="usage-data-and-reservation-utilization"></a>使用数据和预留使用情况

对于获得预留折扣的使用量，使用数据的有效价格为零。 这样可以看出每个预留项对应的预留折扣分别应用于哪一个 VM 实例。

有关使用情况数据中如何显示预留折扣的详细信息，EA 客户可参阅[了解企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)。 如果你使用个人订阅，请参阅[了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在购买后更改预留

可以在购买后对预留进行以下类型的更改：

- 更新预留范围

- 实例大小灵活性（如果适用）

- 所有权

还可以将预留项拆分为多个小区块，或者对已拆分的预留项进行合并。 任何更改都不会导致新的商业交易，也不会更改预留项的结束日期。

在购买后，不能直接进行以下类型的更改：

- 现有预留的区域

- SKU

- 数量

- 持续时间

但是，如果你要进行更改，可以交换预留项。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预留，请参阅[管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [使用 Azure 专用主机](./dedicated-hosts.md)

- [专用主机定价](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 中的预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [了解预留折扣的应用方式](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [了解采用即用即付费率的订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [预留未包含的 Windows 软件成本](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)
