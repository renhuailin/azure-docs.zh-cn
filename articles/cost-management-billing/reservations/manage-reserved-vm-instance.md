---
title: 管理 Azure 预留
description: 了解如何管理 Azure 预留。 请参阅更改预留范围、拆分预留和优化预留使用的步骤。
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: cee0acf851d82ba09867b8d66c09a17b21e7af45
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989050"
---
# <a name="manage-reservations-for-azure-resources"></a>管理 Azure 资源的预留

购买 Azure 预留后，可能需要将预留应用到其他订阅，更改可以管理预留的人员，或更改预留的范围。 还可以将预留拆分为两个预留，以将购买的一些实例应用于另一个订阅。

如果购买了 Azure 虚拟机预留实例，则可以更改预留的优化设置。 预留折扣可以应用于同一系列中的 VM，也可以为特定的 VM 大小保留数据中心容量。 你应该尝试优化预留，使之充分发挥作用。

*管理预留所需的权限不同于订阅权限。*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>预留订单和预留

购买预留时，会创建两个对象：**预留订单** 和 **预留**。

在购买时，预留订单下有一个预留。 拆分、合并、部分退款或交换之类的操作会在“预留订单”下创建新的预留。

若要查看预留订单，请转到“预留”，选择预留，然后选择“预留订单 ID” 。

![预留订单详细信息的示例，显示预留订单 ID ](./media/manage-reserved-vm-instance/reservation-order-details.png)

预留从其预留订单继承权限。 若要交换预留或对其退款，应将用户添加到预留订单。

## <a name="change-the-reservation-scope"></a>更改预留范围

 预留折扣适用于与预留匹配并在预留范围内运行的虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源。 计费上下文依赖于用于购买预订的订阅。

若要更新预订范围，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
5. 更改范围。

如果要从“共享”更改为单个范围，只能选择自己是其所有者的订阅。 只能选择与该预留相同的计费上下文中的订阅。

此范围仅适用于使用即用即付费率的单个订阅（套餐 MS-AZR-0003P 或 MS-AZR-0023P），企业套餐 MS-AZR-0017P 或 MS-AZR-0148P，或 CSP 订阅类型。

## <a name="who-can-manage-a-reservation-by-default"></a>默认情况下谁可以管理预留

默认情况下，以下用户可以查看和管理预留：

- 购买预留的人员和计费订阅的帐户所有者可获得对预留订单的 Azure RBAC 访问权限。
-  企业协议和 Microsoft 客户协议计费参与者可通过“成本管理 + 计费”>“预留交易”> 选择蓝色横幅来管理所有预留。

若要允许其他人管理预留，可通过两种方式实现：

- 通过向单个预留订单的资源范围内的用户分配“所有者”角色，委托该预留订单的访问权限管理。 如果希望提供有限的访问权限，请选择其他角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

- 将用户作为计费管理员添加到企业协议或 Microsoft 客户协议：
    - 对于企业协议，请添加具有“企业管理员”角色的用户，以便查看和管理适用于企业协议的所有预留订单。 具有“企业管理员（只读）”角色的用户可以查看预留。 除非使用访问控制 (IAM) 将部门管理员和帐户所有者显式添加到预留中，否则他们无法查看相关预留。 有关详细信息，请参阅[管理 Azure 企业角色](../manage/understand-ea-roles.md)。

        企业管理员可以获得预留订单的所有权，并可以使用访问控制 (IAM) 将其他用户添加到预留。
    - 在 Microsoft 客户协议下，具有计费配置文件所有者角色或计费配置文件参与者角色的用户可以管理使用计费配置文件完成的所有预留购买。 计费配置文件阅读者和发票管理员可以通过计费配置文件查看付费的所有预留。 但是，他们不能对预留进行更改。
    有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="how-billing-administrators-view-or-manage-reservations"></a>计费管理员如何查看或管理预留

如果你是计费管理员，请使用以下步骤来查看和管理所有预留项与预留交易。

1. 登录到 [Azure 门户](https://portal.azure.com)，导航到“成本管理 + 计费”。
    - 如果你是 EA 管理员，请在左侧菜单中选择“计费范围”，然后在计费范围列表中选择一个范围。
    - 如果你是 Microsoft 客户协议计费配置文件所有者，请在左侧菜单中选择“计费配置文件”。 在计费配置文件列表中选择一个计费配置文件。
2. 在左侧菜单中，选择“产品 + 服务” > “预留项” 。
3. 此时将显示 EA 注册或计费配置文件的完整预留项列表。
4. 计费管理员可以通过选择预留项，然后在出现的窗口中选择“授予访问权限”，来取得预留项的所有权。

## <a name="change-billing-subscription-for-an-azure-reservation"></a>更改 Azure 预留的计费订阅

我们不允许在购买预留后更改计费订阅。 如果要更改订阅，请使用交换过程为预留设置合适的计费订阅。

## <a name="split-a-single-reservation-into-two-reservations"></a>将一个预订拆分为两个预订

 购买一个预留中的多个资源实例后，可能会希望将一个预留中的实例分配给其他订阅。 默认情况下，所有实例都有一个范围 - 单个订阅、资源组或共享。 假设已购买 10 VM 实例的预留并将范围指定为订阅 A。现在需要将 7 VM 实例的范围更改为订阅 A，将剩余 3 个预留范围更改为订阅 B。通过拆分预留可以执行此操作。 拆分预留后，将取消原始 ReservationID 并创建两个新预留。 拆分不会影响预留订单 - 拆分不带来新的商业交易，新预留的结束日期与被拆分的预留相同。

 可以通过 PowerShell、CLI 或通过 API 将一个预订拆分为两个预订。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 拆分预订

1. 通过运行以下命令获取预订订单 ID：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 获取预订的详细信息：

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 将该预订拆分为两个预订并分配实例：

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 可运行以下命令更新范围：

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>更改预留 VM 实例的优化设置

 购买 VM 预留实例时，可以选择实例大小的灵活性或容量优先级。 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](../../virtual-machines/reserved-vm-instance-size-flexibility.md)中的其他 VM。 容量优先级为部署指定最重要的数据中心容量。 此选项使你在需要时能够更加有把握地启动 VM 实例。

默认情况下，在共享预留范围时，将启用实例大小灵活性。 数据中心容量未针对 VM 部署设置优先级。

对于只有一个范围的预留，可以根据容量优先级而不是 VM 实例大小灵活性来优化预留。

若要更新预留的优化设置，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
  ![显示配置项目的示例](./media/manage-reserved-vm-instance/add-product03.png)
5. 更改“优化对象”设置。
  ![显示“针对设置进行优化”的示例](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>优化预留的使用

只有持续使用资源，才会有 Azure 预留节省好处。 进行预留购买时，你要为一或三年的期限内基本上 100% 可能的资源使用付费。 尝试将预留最大化，以获取尽可能多的使用和节省。 下面各部分介绍了如何监视预留并优化其使用。

### <a name="view-reservation-use-in-the-azure-portal"></a>在 Azure 门户中查看预留使用

查看预留使用的一种方式是使用 Azure 门户。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > [ **“预留”**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，记下某个预留的“利用率(%)”。   
  ![显示预留列表的图像](./media/manage-reserved-vm-instance/reservation-list.png)
3. 选择一个预留。
4. 查看一段时间的预留使用趋势。
  ![显示预留使用的图像](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>通过 API 查看预留使用

如果你是企业协议 (EA) 客户，则可以编程方式查看组织中的预留是如何使用的。 通过使用数据获取未使用的预留 查看预留费用时，请记住，数据按实际成本和摊销成本划分。 实际成本提供要与每月账单对帐的数据。 它还包括预留项购买成本和预留项应用详细信息。 摊销成本类似于实际成本，只是预留使用量的实际价格按比例计算。 未使用的预留小时数显示在摊销成本数据中。 有关 EA 客户使用数据的详细信息，请参阅[获取企业协议预留成本和使用情况](understand-reserved-instance-usage-ea.md)。

对于其他订阅类型，请使用 API [预览摘要 - 按预留订单和预留列出](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

### <a name="optimize-your-reservation"></a>优化预留

如果发现组织的预留未充分使用，请执行以下操作：

- 确保组织创建的虚拟机数与预留的 VM 大小相匹配。
- 确保已启用实例大小灵活性。 有关详细信息，请参阅[管理预留 - 更改预留 VM 实例的优化设置](#change-optimize-setting-for-reserved-vm-instances)。
- 将预留范围更改为“共享”，使其应用得更广。 有关详细信息，请参阅[更改预留的范围](#change-the-reservation-scope)。
- 考虑交换未使用的数量。 有关详细信息，请参阅[取消和交换](#cancel-exchange-or-refund-reservations)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：
 - [查看预留利用率](reservation-utilization.md)
 - [换货和退款](exchange-and-refund-azure-reservations.md)
 - [续订预留](reservation-renew.md)
 - [租户之间的传输](troubleshoot-reservation-transfers-between-tenants.md)
 - [从 Azure 日志中查找预留项购买者](find-reservation-purchaser-from-logs.md)
