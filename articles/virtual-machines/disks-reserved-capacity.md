---
title: 通过预留优化 Azure 磁盘存储的成本
description: 了解如何购买 Azure 磁盘存储预留来节省高级 SSD 托管磁盘的成本。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 4ec7c3640a242212010de68a9f08c4d6e3e27300
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694317"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>通过 Azure 磁盘预留降低成本

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

使用预留容量节省 Azure 磁盘存储用量费用。 Azure 磁盘存储预留与 Azure 虚拟机预留实例相使用，可降低虚拟机 (VM) 的总成本。 预留折扣会自动应用于所选预留范围中的匹配磁盘。 由于这种自动应用，无需将预留分配给托管磁盘即可获享折扣。

折扣按小时应用，具体由磁盘用量而定。 未使用的预留容量不会转结。 Azure 磁盘存储预留折扣不适用于非托管磁盘、超级磁盘或页 Blob 消耗。

## <a name="determine-your-storage-needs"></a>确定存储需求

购买预留之前，请确定存储需求。 目前，Azure 磁盘存储预留仅适用于特选 Azure 高级 SSD SKU。 磁盘的大小和性能由高级 SSD 的 SKU 决定。

在确定存储需求时，不要只根据容量来考虑磁盘。 例如，不能预留一个 P40 磁盘，再用它来为两个更小的 P30 磁盘付费。 购买预留时，只需针对每个 SKU 的磁盘总数购买预留。

磁盘预留按磁盘 SKU 进行。 因此，预留消耗量基于磁盘 SKU 的单位而不是提供的大小。

例如，假设你预留了一个预配存储容量为 2 TiB 的 P40 磁盘。 然后，假设你仅分配两个 P30 磁盘。 在这种情况下，P40 预留不考虑 P30 消耗量，你需要在 P30 磁盘上按即用即付费率进行支付。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>购买注意事项

考虑购买磁盘预留时，建议采用以下做法：

- 分析你的使用信息，帮助确定应购买哪一种预留。 请确保跟踪磁盘 SKU 中的使用情况，而不是预配或使用的磁盘容量。
- 检查磁盘预留以及 VM 预留。 强烈建议为 VM 使用和磁盘使用进行预留，以最大程度地节省费用。 可从确定正确的 VM 预留开始，然后评估磁盘预留。 通常，每个工作负载都有一个标准配置。 例如，一个 SQL Server 服务器可能有两个 P40 数据磁盘和一个 P30 操作系统磁盘。
  
  这种模式可帮助你确定你可能购买的预留量。 此方法可简化评估过程，并确保为 VM 和磁盘制定一致的计划。 计划包含订阅或区域等注意事项。

## <a name="purchase-restrictions"></a>购买限制

预留折扣当前不可用于以下各项：

- 非托管磁盘或页 Blob。
- 标准 SSD 或标准硬盘驱动器 (HDD)。
- 小于 P30 的高级 SSD SKU：P1、P2、P3、P4、P6、P10、P15 和 P20 SSD SKU。
- Azure 政府、Azure 德国或 Azure 中国区的磁盘。

在极少数情况下，Azure 会限制购买部分磁盘 SKU 的新预留，因为某个区域的容量不足。

## <a name="buy-a-disk-reservation"></a>购买磁盘预留

可通过 [Azure 门户](https://portal.azure.com/)购买 Azure 磁盘存储预留。 可通过提前付款或按月付款的方式为预留付款。 若要详细了解如何通过按月付款进行购买，请查看[按月付款购买预留](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)。

请按照以下步骤购买预留容量：

1. 转到 Azure 门户中的[购买预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。

1. 选择“Azure 托管磁盘”以购买预留。

    ![购买预留的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表中所述的所需值：

   |元素  |说明  |
   |---------|---------|
   |**范围**   |  可享受与预留关联的计费权益的订阅数。 此值还指定将预留应用于特定订阅的方式。 <br/><br/> 如果选择“共享”，则预留折扣将应用到计费上下文内每个订阅中的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包括由帐户管理员创建的所有采用即用即付费率的个人订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择“单个资源组”，预留折扣将应用到所选订阅中的 Azure 存储容量，以及该订阅的所选资源组。 <br/><br/> 购买预留后，可以更改预留范围。  |
   |**订阅**  | 用于支付 Azure 存储预留费用的订阅。 收取费用时将采用所选订阅中的付款方式。 订阅必须是以下类型之一：<br/><ul><li> 企业协议（产品/服务编号为 MS-AZR-0017P 和 MS-AZR-0148P）。 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。</li><br/><li>采用即用即付费率的单个订阅（产品/服务编号为 MS-AZR-0003P 和 MS-AZR-0023P）。 对于采用即用即付费率的个人订阅，将按订阅上的信用卡或发票付款方式收取费用。</li></ul>    |
   | **磁盘** | 要创建的 SKU。 |
   | **区域** | 预留有效的区域。 |
   | **记帐频率** | 帐户针对预留计费的频率。 选项包括“月付”和“预付” 。 |

    ![用于选择要购买的产品的窗格.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 指定预留值后，Azure 门户会显示费用。 该门户还显示即用即付账单的折扣百分比。 选择“下一步”，继续到“购买预留”窗格 。

1. 在“购买预留”窗格中，可对预留命名，然后选择要进行预留的总数量。 预留数与磁盘数一致。 例如，如果要预留 100 个磁盘，请输入“数量”值 100 。

1. 查看预留的总成本。

    ![“购买预留”窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

购买预留后，该预留会自动应用到任何与该预留期限匹配的现有磁盘存储资源。 如果尚未创建任何磁盘存储资源，则每次创建与预留期限匹配的资源时，都将应用预留。 无论哪种情况，都会在成功购买后立即开始预留期限。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可在一定的限制内对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="expiration-of-a-reservation"></a>预留的过期时间

预留到期后，在期限内所使用的任何 Azure 磁盘存储容量都将按即用即付费率计费。 预留容量不会自动续订。

你将在预留过期前 30 天收到电子邮件通知，在过期日期当天再次收到通知。 若要继续享有预留提供的成本节约益处，请在到期日期前进行续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解预留折扣如何应用于 Azure 磁盘存储](../cost-management-billing/reservations/understand-disk-reservations.md)