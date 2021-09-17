---
title: 预付软件计划费 - Azure 预留
description: 了解如何通过即用即付成本为软件计划预付，从而节省资金。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 6321058c863b6db190ec987887e61cf20ed8be50
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692681"
---
# <a name="prepay-for-azure-software-plans"></a>为 Azure 软件计划预付

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

为 Azure 中使用的 SUSE 和 RedHat 软件预付时，可以通过即用即付成本来节省资金。 折扣仅适用于 SUSE 和 RedHat 计量器，而不适用于虚拟机的使用。 可以单独购买虚拟机预留项，以节省更多成本。

可以在 Azure 门户中购买 SUSE 和 RedHat 软件计划。 若要购买计划：

- 必须至少是一个采用即用即付定价的企业或个人订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项  。 如果禁用了该设置，则购买方必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，管理代理或销售代理可以购买软件计划。

## <a name="buy-a-software-plan"></a>购买软件计划

1. 登录到 Azure 门户并转到“[预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)”。
2. 单击“添加”，然后选择要购买的软件计划。
填写必填字段。 与所购买计划的属性匹配的任何 SUSE Linux VM 或 RedHat VM 都可以享受折扣。 实际获得折扣的部署数取决于所选范围和数量。
3. 选择一个订阅。 该订单用于支付计划费用。
将从订阅中的付款方式收取预留项的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或采用即用即付定价的个人协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。
    - 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。
    - 对于采用即用即付定价的个人订阅，将从订阅中的信用卡或发票付款方式收取费用。
4. 选择一个范围。 范围可以包含一个订阅或多个订阅（共享范围）。
    - 单个订阅 - 计划折扣将应用于订阅中匹配的使用情况。
    - 共享 - 计划折扣将应用于计费上下文中任何订阅内的匹配实例。 对于企业客户，共享范围是注册，包括注册中的所有订阅。 对于拥有即用即付定价客户的单个计划，计费上下文包含帐户管理员创建的即用即付定价订阅的所有个人计划。
    - 单个资源组 - 仅将预留折扣应用于所选资源组中匹配的资源。
5. 选择一个产品以选择 VM 大小和映像类型。 折扣仅适用到选定的 VM 大小。
6. 选择一年或三年期限。
7. 选择数量，即，可以享受计费折扣的预付费 VM 实例数。
8. 将产品添加到购物车，检查购买内容，然后购买。

预留折扣将自动应用于预先支付的软件计量器。 计划不涵盖 VM 计算费用。 可以单独购买 VM 预留项。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣将应用于不同的 SUSE VM 大小

与预留的 VM 实例一样，SUSE Linux 计划提供实例大小灵活性。 即使部署的 VM 与购买的 SUSE 计划中的大小不同，也会应用折扣。 有关详细信息，请参阅[了解如何应用软件计划折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 计划折扣

计划仅适用于 Red Hat Enterprise Linux 虚拟机。 折扣不适用于 RedHat Enterprise Linux SAP HANA VM 或 RedHat Enterprise Linux SAP Business Apps VM。

RedHat 计划折扣仅应用于购买时选择的 VM 大小。 购买后，RHEL 计划不允许退款或交换。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允许取消和交换

无法取消或交换已购买的 SUSE 或 RedHat 计划。 请检查你的使用情况，确保购买的是正确的计划。 如需我们帮助你确定要购买哪种计划，请参阅[了解软件计划折扣的应用方式](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预留，请参阅[管理 Azure 预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解更多信息，请参阅下列文章：

- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 中的预留](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解如何应用 SUSE 预留折扣](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
