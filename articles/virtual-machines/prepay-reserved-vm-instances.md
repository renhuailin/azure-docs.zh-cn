---
title: 预付 Azure 虚拟机费用以节省资金
description: 了解如何购买 Azure 预订虚拟机实例以节省计算成本。
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 819732043ce035214fc9a56e22a99e9a3dec28a2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694281"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>借助 Azure 虚拟机预留实例节省资金

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

承诺购买 Azure 虚拟机预留实例可以节省资金。 预订折扣将自动应用于正在运行的与预订范围和属性匹配的虚拟机数量。 无需向虚拟机分配预留即可享受折扣。 购买的预留实例仅涵盖了所用 VM 的计算部分。 对于 Windows VM，使用量计量器分为两个单独的计量器， 包括一个计算计量器（与 Linux 计量器相同）和一个 Windows IP 计量器。 购买时看到的费用只是计算成本。 这些费用不包括 Windows 软件成本。 有关软件成本的详细信息，请参阅 [Azure 虚拟机预留实例未包含的软件成本](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)。

## <a name="determine-the-right-vm-size-before-you-buy"></a>在购买前确定正确的 VM 大小

在购买预留项之前，应确定所需的 VM 大小。 以下部分可帮助你确定适当的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用预留建议

可使用预留建议来帮助确定你应购买的预留项。

- 在 Azure 门户中购买虚拟机预留实例时，会看到购买建议和推荐的数量。
- Azure 顾问会为每个订阅提供购买建议。  
- 可使用 API 来获取有关共享范围和单个订阅范围的购买建议。 有关详细信息，请查看[适用于企业客户的预留实例购买建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- 对于企业协议 (EA) 和 Microsoft 客户协议 (MCA) 客户，可通过 [Azure Consumption Insights Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)获取共享范围和单个订阅范围的购买建议。

### <a name="services-that-get-vm-reservation-discounts"></a>可获得 VM 保留折扣的服务

VM 预留可以应用于从多个服务发出的 VM 使用量 - 而不仅应用于 VM 部署。 可获得预留折扣的资源会根据实例大小灵活性设置而变化。

#### <a name="instance-size-flexibility-setting"></a>实例大小灵活性设置

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

有关实例大小灵活性的详细信息，请参阅[虚拟机预留实例的虚拟机大小灵活性](reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-your-usage-information"></a>分析使用信息

分析你的使用信息，帮助确定应购买哪一种预留。 可在使用情况文件和 API 中获取使用数据。 可结合这两者来确定要购买哪一种预留。 查看每日使用量较高的 VM 实例，确定要购买的预留数量。 避免在使用情况数据中填写 `Meter` 子类别和 `Product` 字段。 它们不区分使用高级存储的 VM 大小。 如果使用这些字段来确定所购买预留项的 VM 大小，可能会买到错误的大小。 于是，你将享受不到预期的预留折扣。 请参阅使用情况文件或使用情况 API 中的 `AdditionalInfo` 字段，以确定正确的 VM 大小。

使用情况文件按计费周期和每日使用情况显示费用信息。 若要了解如何下载使用情况文件，请参阅[查看和下载 Azure 使用情况信息和费用](../cost-management-billing/understand/download-azure-daily-usage.md)。 然后，借助使用情况文件信息，可[确定要购买的具体预留](../cost-management-billing/reservations/determine-reservation-purchase.md)。

### <a name="purchase-restriction-considerations"></a>购买限制注意事项

虚拟机预留实例适用于大多数 VM 大小，不过存在一些例外情况。 预留折扣不适用于以下 VM：

- VM 系列 - A 系列、Av2 系列或 G 系列

- 预览版或促销 VM - 以预览版提供的或者使用促销计量器的任何 VM 系列或大小。

- **云** - 不能在德国或中国区域中购买预留。

- 配额不足 - 其范围仅限单个订阅的预留必须在订阅中具有可用的 vCPU 配额才能获得新的 RI。 例如，如果目标订阅的配额限制为 10 个 vCPU（适用于 D 系列），则不能为 11 个 Standard_D1 实例购买预留。 虚拟机预留实例的配额检查包括已在订阅中部署的 VM。 例如，如果该订阅的配额为针对 D 系列购买 10 个 vCPU，并且已部署两个 Standard_D1 实例，则可在该订阅中为 10 个 Standard_D1 实例购买预留。 可以[创建配额提高请求](../azure-portal/supportability/resource-manager-core-quotas-request.md)来解决此问题。

- 容量限制 - 在极少数情况下，Azure 会由于区域的容量不足而限制购买部分 VM 大小的新预留项。

## <a name="buy-a-reserved-vm-instance"></a>购买虚拟机预留实例

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买虚拟机预留实例。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。
购买虚拟机预留实例时需满足以下要求：

- 你必须具有至少一个 EA 订阅或采用即用即付费率的订阅的所有者角色。
- 对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务” > “预订”。
1. 选择“添加”以购买新的预留，然后单击“虚拟机” 。
1. 输入必填字段。 与所选属性匹配的正在运行的 VM 实例有资格获得预订折扣。 实际获得折扣的 VM 实例数取决于所选范围和数量。

如果你有 EA 协议，则可使用“添加更多选项”来快速添加其他实例。 此选项不可用于其他订阅类型。


| 字段      | 说明|
|------------|--------------|
|订阅|用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或采用即用即付费率的个人订阅中担任所有者角色（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）。 从 Azure 预付款（以前称为货币承诺）余额中扣除费用（如果可用）或按超额收取费用。 对于采用即用即付费率的订阅，将向订阅的信用卡或发票付款方式收取费用。|    
|范围       |预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>|
|区域    |预订涵盖的 Azure 区域。|    
|VM 大小     |VM 实例的大小。|
|优化对象     |默认已选择 VM 实例大小灵活性。 单击“高级设置”可更改实例大小灵活性值，以将预留折扣应用于同一 [VM 大小组](reserved-vm-instance-size-flexibility.md)中的其他 VM。 容量优先级可以对部署优先使用数据中心容量。 它使你在需要时能够更加有把握地启动 VM 实例。 仅当预留范围为单个订阅时，容量优先级才可用。 |
|术语        |一年或三年。 此外，还有一个仅适用于 HBv2 VM 的 5 年期限。|
|数量    |预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 VM 实例数。 例如，如果你在美国东部运行 10 个 Standard_D2 VM，请将数量指定为 10，以将所有正在运行的 VM 的权益最大化。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>使用情况数据和预留使用量

对于获得预留折扣的使用量，使用数据的有效价格为零。 这样可以看出每个预留项对应的预留折扣分别应用于哪一个 VM 实例。

有关使用情况数据中如何显示预留折扣的详细信息，EA 客户可参阅[了解企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)。 如果你使用个人订阅，请参阅[了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在购买后更改预留

可以在购买后对预留进行以下类型的更改：

- 更新预留范围
- 实例大小灵活性（如果适用）
- 所有权

还可以将预留项拆分为多个小区块，或者对已拆分的预留项进行合并。 任何更改都不会导致新的商业交易，也不会更改预留项的结束日期。

在购买后，不能直接进行以下类型的更改：

- 现有预留项的区域
- SKU
- 数量
- 持续时间

但是，如果你要进行更改，可以交换预留项。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [了解采用即用即付费率的订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)