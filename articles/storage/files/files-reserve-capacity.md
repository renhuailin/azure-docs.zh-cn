---
title: 使用预留容量优化 Azure 文件存储的成本
titleSuffix: Azure Files
description: 了解如何使用 Azure 文件存储预留容量来节省 Azure 文件共享部署的成本。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 216ebdeb843f2faa76751f333e838c3cc32a6664
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539261"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>使用预留容量优化 Azure 文件存储的成本
可以通过产能预留来节省 Azure 文件共享的存储成本。 如果你承诺预留一年或三年，Azure 文件存储预留容量可提供容量折扣，以节省存储成本。 预留在预留期内提供固定数量的存储容量。

Azure 文件存储预留容量可大幅降低在 Azure 文件共享中存储数据的容量成本。 保存量将取决于预留期、选择预留的总容量以及为 Azure 文件共享选择的层级和冗余设置。 预留容量可提供计费折扣，并且不影响 Azure 文件共享的状态。

有关 Azure 文件存储预留容量的定价信息，请参阅 [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

## <a name="reservation-terms-for-azure-files"></a>Azure 文件存储的预留条款
以下各部分介绍 Azure 文件存储产能预留的条款。

### <a name="reservation-capacity"></a>预留容量
可以每月 10 TiB（1 年期）或 100 TiB（3 年期）为单位购买 Azure 文件存储预留容量。

### <a name="reservation-scope"></a>预留范围
Azure 文件存储预留容量可用于单个订阅或多个订阅（共享范围）。 当范围为单个订阅时，预留折扣仅适用于所选订阅。 当范围为多个订阅时，预留折扣将在客户的计费背景下在这些订阅之间共享。 预留适用于在所购买的范围内使用，且不能仅限于订阅中的特定存储帐户、容器或对象。

Azure 文件存储的产能预留仅涵盖订阅或共享资源组中存储的数据量。 预留中不包括事务、带宽和数据传输费用。 购买预留后，与预留属性匹配的容量费用将以折扣率（而不是按即用即付费率）计收。 有关 Azure 预留的详细信息，请参阅[什么是 Azure 预留？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)。

### <a name="supported-tiers-and-redundancy-options"></a>支持的层和冗余选项
Azure 文件存储预留容量可用于高级、热层和冷层文件共享。 预留容量不适用于事务优化层中的 Azure 文件共享。 所有存储冗余都支持预留。 有关冗余选项的详细信息，请参阅 [Azure 文件存储冗余](storage-files-planning.md#redundancy)。

### <a name="security-requirements-for-purchase"></a>购买的安全要求
若要购买预留容量，须满足以下条件：

- 必须至少有一个采用即用即付费率的企业或个人订阅的“所有者”角色。
- 对于企业订阅，必须在 EA 门户中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure 文件存储预留容量。

## <a name="determine-required-capacity-before-purchase"></a>在购买之前确定所需容量
购买 Azure 文件存储预留时，必须选择预留的区域、层级和冗余选项。 预留仅对存储在该区域、层级和冗余级别中的数据有效。 例如，假设你使用区域冗余存储 (ZRS) 为美国西部的数据购买热层预留。 该预留将不适用于美国东部的数据、冷层中的数据或异地冗余存储 (GRS) 中的数据。 不过，你可以购买其他预留来满足自己的其他需求。  

预留适用于 10 TiB 或 100 TiB 的块，并且 100 TiB 的块具有更高的折扣。 在 Azure 门户中购买预留时，Microsoft 可能会根据你之前的使用情况提供建议，帮助你确定应购买的预留。

## <a name="purchase-azure-files-reserved-capacity"></a>购买 Azure 文件存储预留容量
可以通过 [Azure 门户](https://portal.azure.com)购买 Azure 文件存储预留容量。 通过提前付款或按月付款的方式为预留付款。 有关通过月付方式购买的详细信息，请参阅[按预付或月付方式购买 Azure 预留](../../cost-management-billing/reservations/prepare-buy-reservation.md)。

如需帮助来确定适合你的场景的预留条款，请参阅[了解 Azure 存储预留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

请按照以下步骤购买预留容量：

1. 导航到 Azure 门户中的[购买预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)选项卡。  
1. 选择“Azure 文件存储”购买新的预留。  
1. 填写必填字段，如下表所示：

    ![显示如何购买预留容量的屏幕截图](./media/files-reserve-capacity/select-reserved-capacity.png)

   |字段  |说明  |
   |---------|---------|
   |**范围**   |  指示可享受与预留关联的计费权益的订阅数。 它还控制将预留应用于特定订阅的方式。 <br/><br/> 如果选择“共享”，则预留折扣将应用到计费背景下任何订阅中的 Azure 文件存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包括由帐户管理员创建的所有采用即用即付费率的个人订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure 文件存储容量。 <br/><br/> 如果选择“单个资源组”，预留折扣将应用到所选订阅中的 Azure 文件存储容量，以及该订阅内的所选资源组。 <br/><br/> 购买预留后，可以更改预留范围。  |
   |**订阅**  | 用于支付 Azure 文件存储预留的订阅。 收取费用时将采用所选订阅中的付款方式。 订阅必须是以下类型之一： <br/><br/>  企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）：对于企业订阅，从注册的 Azure 预付款余额（以前称为货币承诺）中扣除费用或作为超额费用收取。 <br/><br/> 采用即用即付费率的个人订阅（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）：对于采用即用即付费率的个人订阅，通过信用卡收取费用或按订阅中的发票付款方式收费。    |
   | **区域** | 预留有效的区域。 |
   | **层** | 预留所适用的层。 选项包括“高级”、“热层”和“冷层”  。 |
   | **冗余** | 预留的冗余选项。 选项包括“LRS”、“ZRS”、“GRS”和“GZRS”   。 有关冗余选项的详细信息，请参阅 [Azure 文件存储冗余](storage-files-planning.md#redundancy)。 |
   | **账单频率** | 指示帐户按预留计费的频率。 选项包括“月付”或“预付” 。 |
   | **大小** | 要预留的容量总量。 |
   |**条款**  | 一年或三年。   |

1. 选择预留参数后，Azure 门户会显示费用。 该门户还显示即用即付账单的折扣百分比。

1. 在“购买预留”边栏选项卡中，查看预留的总费用。 还可以为预留提供名称。

购买预留后，预留便会自动应用到任何与该预留条款匹配的现有 Azure 文件共享。 如果尚未创建任何 Azure 文件共享，则每次创建与预留条款匹配的资源时，都将应用预留。 无论哪种情况，预留的期限都会在成功购买后立即开始。

## <a name="exchange-or-refund-a-reservation"></a>对预留执行交换或退款操作
可以对预留执行交换或退款操作，但有一定限制。 后续部分将介绍这些选项。

若要对预留执行交换或退款操作，请导航到 Azure 门户中的预留详细信息。 选择“交换”或“退款”，然后按照说明提交支持请求 。 处理完请求后，Microsoft 将向你发送一封电子邮件以确认请求已完成。

有关 Azure 预留策略的详细信息，请参阅 [Azure 预留自助式交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>调换预留
通过交换预留，用户可根据未使用的预留部分按比例获得退款。 然后，可以将退款应用到新的 Azure 文件存储预留的购买价格。

可以进行的交互数没有限制。 此外，交换是免费的。 所购买新预留的价值必须等于或大于初始预留的按比例分配的额度。 Azure 文件存储预留只能与其他 Azure 文件存储预留进行交换，而不能与任何其他 Azure 服务的预留进行交换。

### <a name="refund-a-reservation"></a>预留退款
可以随时取消 Azure 文件存储预留。 取消时，基于预留的剩余期限按比例退款，并减去 12% 的提前终止手续费。 退款上限每年为 50,000 美元。

取消预留会立即终止预留，并将剩余的月份退还给 Microsoft。 剩余按比例分配的余额减去手续费后，将按原购买方式返还退款。

## <a name="expiration-of-a-reservation"></a>预留的过期时间
预留到期后，在期限内所使用的任何 Azure 文件存储容量都将按即用即付费率计费。 预留容量不会自动续订。

你将在预留过期前 30 天收到电子邮件通知，并在过期日期当天再次收到通知。 若要继续享有预留提供的成本节约益处，请在到期日期前进行续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们
如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预留折扣应用到 Azure 存储服务](../../cost-management-billing/reservations/understand-storage-charges.md)
