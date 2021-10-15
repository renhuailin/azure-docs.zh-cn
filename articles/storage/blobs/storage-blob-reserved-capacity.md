---
title: 借助预留容量优化 Blob 存储的成本
titleSuffix: Azure Storage
description: 了解购买 Azure 存储预留容量，节约块 blob 和 Azure Data Lake Storage Gen2 资源成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 14f8cc665ef1d7335116a2e5b68ea3f58424063a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274437"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>借助预留容量优化 Blob 存储的成本

可以借助 Azure 存储预留容量来节约 blob 数据的存储成本。 如果你承诺预留一年或三年，Azure 存储预留容量可为标准存储帐户中的块 blob 和 Azure Data Lake Storage Gen2 数据提供容量折扣。 预留在预留期内提供固定数量的存储容量。

Azure 存储预留容量可大幅降低用于块 blob 和 Azure Data Lake Storage Gen2 数据的容量成本。 实现的成本节约取决于预留期限、选择预留的总容量以及为存储帐户选择的访问层级和冗余类型。 预留容量可提供计费折扣，并且不影响 Azure 存储资源的状态。

有关 Azure 存储预留定价的信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="reservation-terms-for-azure-storage"></a>Azure 存储的预留期限

以下各部分介绍 Azure 存储预留期限。

### <a name="reservation-capacity"></a>预留容量

可按每月 100 TiB（1 年期）或 1 PiB（3 年期）为单位购买 Azure 存储预留容量。

### <a name="reservation-scope"></a>预留范围

Azure 存储预留容量可用于单个订阅、多个订阅（共享范围）和管理组。 当范围为单个订阅时，预留折扣仅适用于所选订阅。 当范围为多个订阅时，预留折扣将在客户的计费背景下在这些订阅之间共享。 当范围限定为管理组时，预留折扣在同时属于管理组和计费范围的订阅之间共享。

如果要购买 Azure 存储预留容量，则可以将预留用于块 blob 和 Azure Data Lake Storage Gen2 数据。 预留适用于在所购买的范围内使用，且不能仅限于订阅中的特定存储帐户、容器或对象。

Azure 存储预留仅涵盖订阅或共享资源组中存储的数据量。 预留中不包括早期删除、操作、带宽和数据传输费用。 购买预留后，与预留属性匹配的容量费用将以折扣率（而不是按即用即付费率）计收。 有关 Azure 预留的详细信息，请参阅[什么是 Azure 预留？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)。

### <a name="supported-account-types-tiers-and-redundancy-options"></a>支持的帐户类型、层级和冗余选项

Azure 存储预留容量适用于标准存储帐户中的资源，包括常规用途 v2 (GPv2) 和 Blob 存储帐户。

所有访问层（热、冷和存档）都支持预留。 有关访问层的详细信息，请参阅 [Blob 数据的热访问层、冷访问层和存档访问层](access-tiers-overview.md)。

所有类型的冗余都支持预留。 有关冗余选项的详细信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。

> [!NOTE]
> Azure 存储预留容量不适用于高级存储帐户、常规用途 v1 (GPv1) 存储帐户、Azure Data Lake Storage Gen1、页 blob、Azure 队列存储或 Azure 表存储。 有关 Azure 文件存储预留容量的信息，请参阅[借助预留容量优化 Azure 文件存储的成本](../files/files-reserve-capacity.md)。

### <a name="security-requirements-for-purchase"></a>购买的安全要求

若要购买预留容量，须满足以下条件：

- 必须至少有一个采用即用即付费率的企业或个人订阅的“所有者”角色。
- 对于企业订阅，必须在 EA 门户中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Blob 存储预留容量。

## <a name="determine-required-capacity-before-purchase"></a>在购买之前确定所需容量

购买 Azure 存储预留时，必须选择预留的区域、访问层和冗余选项。 预留仅对存储在该区域、访问层和冗余级别中的数据有效。 例如，假设你使用区域冗余存储 (ZRS) 为美国西部的数据购买热层预留。 你不能将相同的预留用于美国东部的数据、存档层中的数据或异地冗余存储 (GRS) 中的数据。 不过，你可以购买其他预留来满足自己的其他需求。

预留适用于 100 TiB 或 1 PiB 的块，并且 1 PiB 的块具有更高的折扣。 在 Azure 门户中购买预留时，Microsoft 可能会根据你之前的使用情况提供建议，帮助你确定应购买的预留。

## <a name="purchase-azure-storage-reserved-capacity"></a>购买 Azure 存储预留容量

可以通过 [Azure 门户](https://portal.azure.com)购买 Azure 存储预留容量。 通过提前付款或按月付款的方式为预留付款。 有关通过月付方式购买的详细信息，请参阅[按预付或月付方式购买 Azure 预留](../../cost-management-billing/reservations/prepare-buy-reservation.md)。

如需帮助来确定适合你的场景的预留期限，请参阅[了解 Azure 存储预留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

请按照以下步骤购买预留容量：

1. 导航到 Azure 门户中的[购买预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。
1. 选择“Azure Blob 存储”购买新的预留。
1. 填写必填字段，如下表所示：

    ![显示如何购买预留容量的屏幕截图](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |字段  |说明  |
   |---------|---------|
   |**范围**   |  指示可享受与预留关联的计费权益的订阅数。 它还控制将预留应用于特定订阅的方式。 <br/><br/> 如果选择“共享”，则预留折扣将应用到计费背景下任何订阅中的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包括由帐户管理员创建的所有采用即用即付费率的个人订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择“单个资源组”，预留折扣将应用到所选订阅中的 Azure 存储容量，以及该订阅内的所选资源组。 <br/><br/> 购买预留后，可以更改预留范围。  |
   |**订阅**  | 用于支付 Azure 存储预留的订阅。 收取费用时将采用所选订阅中的付款方式。 订阅必须是以下类型之一： <br/><br/>  企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）：对于企业订阅，从注册的 Azure 预付款余额（以前称为货币承诺）中扣除费用或作为超额费用收取。 <br/><br/> 采用即用即付费率的个人订阅（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）：对于采用即用即付费率的个人订阅，通过信用卡收取费用或按订阅中的发票付款方式收费。    |
   | **区域** | 预留有效的区域。 |
   | **访问层** | 预留所适用的访问层。 选项包括“热”、“冷”或“存档”  。 有关访问层的详细信息，请参阅 [Blob 数据的热访问层、冷访问层和存档访问层](access-tiers-overview.md)。 |
   | **冗余** | 预留的冗余选项。 选项包括“LRS”、“ZRS”、“GRS”、“GZRS”、“RA-GRS”和“RA-GZRS”     。 有关冗余选项的详细信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。 |
   | **计费频率** | 指示帐户按预留计费的频率。 选项包括“月付”或“预付” 。 |
   | **大小** | 要预留的容量总量。 |
   |**条款**  | 一年或三年。   |

1. 选择预留参数后，Azure 门户会显示费用。 该门户还显示即用即付账单的折扣百分比。

1. 在“购买预留”窗格中，查看预留的总费用。 还可以为预留命名。

    ![显示如何购买预留的屏幕截图](media/storage-blob-reserved-capacity/purchase-reservations.png)

购买预留后，预留会自动应用到与预留期限匹配的任何现有块 blob 和 Azure Data Lake Storage Gen2 资源。 如果尚未创建任何 Azure 存储资源，则每次创建与预留期限匹配的资源时，都将应用预留。 无论哪种情况，预留的期限都会在成功购买后立即开始。

## <a name="exchange-or-refund-a-reservation"></a>对预留执行交换或退款操作

可以对预留执行交换或退款操作，但有一定限制。 后续部分将介绍这些选项。

若要对预留执行交换或退款操作，请导航到 Azure 门户中的预留详细信息。 选择“交换”或“退款”，然后按照说明提交支持请求 。 处理完请求后，Microsoft 将向你发送一封电子邮件以确认请求已完成。

有关 Azure 预留策略的详细信息，请参阅 [Azure 预留自助式交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>调换预留

通过交换预留，用户可根据未使用的预留部分按比例获得退款。 然后，可以将退款应用到新的 Azure 存储预留的购买价格。

可以进行的交换数没有限制。 此外，交换是免费的。 所购买新预留的价值必须等于或大于初始预留的按比例分配的额度。 Azure 存储预留只能与其他 Azure 存储预留进行交换，而不能与任何其他 Azure 服务的预留进行交换。

### <a name="refund-a-reservation"></a>预留退款

可以随时取消 Azure 存储预留。 取消时，基于预留的剩余期限按比例退款，并减去 12% 的提前终止手续费。 退款上限每年为 50,000 美元。

取消预留会立即终止预留，并将剩余的月份退还给 Microsoft。 剩余按比例分配的余额减去手续费后，将按原购买方式返还退款。

## <a name="expiration-of-a-reservation"></a>预留的过期时间

预留到期后，在期限内所使用的任何 Azure 存储容量都将按即用即付费率计费。 预留容量不会自动续订。

你将在预留过期前 30 天收到电子邮件通知，并在过期日期当天再次收到通知。 若要继续享有预留提供的成本节约益处，请在到期日期前进行续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预留折扣应用于 Azure 存储](../../cost-management-billing/reservations/understand-storage-charges.md)
