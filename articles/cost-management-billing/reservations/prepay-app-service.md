---
title: 通过预留容量为 Azure 应用服务节省费用
description: 了解可以如何节省 Azure 应用服务 Premium v3 和独立 v2 预留实例费用和独立印花费。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/04/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: f3c45799d88601bb8ef9a2d652bb07d7d54aceb9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456606"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>节省 Azure 应用服务预留实例的费用

本文介绍了可以如何使用 Azure 应用服务预留实例节省 Premium v3 和独立 v2 实例费用和独立印花费。

## <a name="save-with-premium-v3-reserved-instances"></a>使用 Premium v3 预留实例节省费用

承诺使用 Azure 应用服务 Premium v3 预留实例后，即可节省费用。 预订折扣会自动应用于与预留范围和属性相匹配的正在运行的虚拟机数量。 无需向实例分配预留即可享受折扣。

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>购买前请确定适当的预留实例大小

购买预留之前，应确定所需的 Premium v3 预留实例的大小。 以下部分将帮助你确定适当的 Premium v3 预留实例大小。

### <a name="use-reservation-recommendations"></a>使用预留建议

可使用预留建议来帮助确定你应购买的预留项。

- 在 Azure 门户中购买 Premium v3 预留实例时，会看到购买建议和建议的数量。
- Azure 顾问会为每个订阅提供购买建议。
- 可使用 API 来获取有关共享范围和单个订阅范围的购买建议。 有关详细信息，请查看[适用于企业客户的预留实例购买建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- 对于企业协议 (EA) 和 Microsoft 客户协议 (MCA) 客户，可通过 [Azure Consumption Insights Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)获取共享范围和单个订阅范围的购买建议。

### <a name="analyze-your-usage-information"></a>分析使用信息

分析你的使用信息，帮助确定应购买哪一种预留。 可在使用情况文件和 API 中获取使用数据。 可结合这两者来确定要购买哪一种预留。 查看每日使用量较高的 Premium v3 实例，确定要购买的预留数量。

使用情况文件按计费周期和每日使用情况显示费用信息。 若要了解如何下载使用情况文件，请参阅[查看和下载 Azure 使用情况信息和费用](../understand/download-azure-daily-usage.md)。 然后，借助使用情况文件信息，可[确定要购买的具体预留](determine-reservation-purchase.md)。

## <a name="buy-a-premium-v3-reserved-instance"></a>购买 Premium v3 预留实例

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买预留的 Premium v3 预留实例。 通过[提前付款或按月付款](prepare-buy-reservation.md)的方式为预留付款。 购买 Premium v3 预留实例时需满足以下要求：

- 你必须具有至少一个 EA 订阅或采用即用即付费率的订阅的所有者角色。
- 对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务”   > “预订”  。
3. 选择“添加”以购买新的预留，然后单击“实例” 。
4. 输入必填字段。 与所选属性匹配的正在运行的 Premium v3 预留实例有资格享受预留折扣。 可享受折扣的 Premium v3 预留实例的实际数目取决于所选范围和数量。

如果你有 EA 协议，则可使用“添加更多选项”来快速添加其他实例。 此选项不可用于其他订阅类型。

| 字段 | **说明** |
|---|---|
| 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或采用即用即付费率的个人订阅中担任所有者角色（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）。 从货币承诺余额中扣除费用（如果可用）或作为超额收取费用。 对于采用即用即付费率的订阅，将向订阅的信用卡或发票付款方式收取费用。 |
| 范围 | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： <ul><li>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。 </li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li><li>管理组 - 将预留折扣应用于同时属于管理组和计费范围的订阅列表中的匹配资源。</li></ul> |
| 区域 | 预订涵盖的 Azure 区域。 |
| Premium v3 预留实例大小 | Premium v3 预留实例的大小。 |
| 术语 | 一年或三年。 还有一个 5 年期，它仅适用于 HBv2 Premium v3 预留实例。 |
| 数量 | 预订中购买的实例数。 数量是正在运行且可享受计费折扣的 Premium v3 预留实例数量。 例如，如果你正在美国东部运行 10 个 Standard\_D2 Premium v3 预留实例，则将数量指定为 10，以使所有正在运行的 Premium v3 预留实例尽可能获得最大优势。 |

## <a name="buy-an-isolated-v2-reserved-instance"></a>购买独立 v2 预留实例

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买独立 v2 预留实例。 通过[提前付款或按月付款](prepare-buy-reservation.md)的方式为预留付款。 购买独立 v2 预留实例时需满足以下要求：

- 你必须具有至少一个 EA 订阅或采用即用即付费率的订阅的所有者角色。
- 对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务”   > “预订”  。
3. 选择“添加”以购买新的预留，然后单击“实例” 。
4. 输入必填字段。 与所选属性匹配的正在运行的独立 v2 预留实例有资格享受预留折扣。 可享受折扣的独立 v2 预留实例的实际数目取决于所选范围和数量。

如果你有 EA 协议，则可使用“添加更多选项”来快速添加其他实例。 此选项不可用于其他订阅类型。

| 字段 | **说明** |
| --- | --- |
| 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或采用即用即付费率的个人订阅中担任所有者角色（套餐编号：MS-AZR-0003P 或 MS-AZR-0023P）。 从货币承诺余额中扣除费用（如果可用）或作为超额收取费用。 对于采用即用即付费率的订阅，将向订阅的信用卡或发票付款方式收取费用。 |
| 范围 | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<UL><LI>**单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li>**单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。</li><li>**共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li><li>管理组 - 将预留折扣应用于同时属于管理组和计费范围的订阅列表中的匹配资源。</li></ul> |
| 区域 | 预订涵盖的 Azure 区域。 |
| 独立 v2 预留实例大小 | 独立 v2 预留实例的大小。 |
| 术语 | 一年或三年。 还有一个仅适用于 HBv2 独立 v2 预留实例的 5 年期。 |
| 数量 | 预订中购买的实例数。 数量是正在运行且可享受计费折扣的独立 v2 预留实例数量。 例如，如果你在美国东部运行 10 个 Standard\_D2 独立 v2 预留实例，则需将数量指定为 10，以充分发挥所有正在运行的独立 v2 预留实例的优势。 |

## <a name="save-with-isolated-stamp-fees"></a>节省独立印花费用

可以通过承诺在三年内使用预留的量，节省 Azure 独立应用服务印花费。 若要购买独立印花费预留容量，需选择要在其中部署印花的 Azure 区域，以及要购买的印花数。

在你购买预留容量后，与预留属性匹配的独立印花费使用量不再按即用即付费率进行收费。 预留自动应用到与预留容量范围和区域匹配的独立印花数。 不需为独立印花分配预留。 预留不适用于辅助角色，因此与印花相关的任何其他资源将单独收费。

如果预留容量到期，独立印花会继续运行，但按即用即付的费率进行计费。 预留容量不会自动续订。

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>确定要购买的适当独立印花预留

购买预留是指你承诺在接下来的三年中使用预留的量。 查看使用数据，确定你在未来会持续使用和可能使用的应用服务独立印花量。

另外，确保你了解独立印花如何发出 Linux 或 Windows 计量。

- 默认情况下，空的独立印花发出 Windows 印花计量。 例如，在没有部署辅助角色的情况下。 如果 Windows 辅助角色部署到印花上，它会继续发出此计量。
- 如果部署 Linux 辅助角色，则计量更改为 Linux 印花计量。
- 如果同时部署 Linux 和 Windows 辅助角色，则印花发出 Windows 记量。

因此，印花计量可以在印花生存期中在 Windows 和 Linux 之间来回变换。

如果印花上有一个或多个 Windows 辅助角色，请购买 Windows 印花预留。 只购买 Linux 印花预留的前提是计划只  在印花上部署 Linux 辅助角色。

## <a name="buy-isolated-stamp-reserved-capacity"></a>购买独立印花预留容量

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)中购买独立印花预留容量。 通过[提前付款或按月付款](./prepare-buy-reservation.md)的方式为预留付款。 若要购买预留容量，你必须拥有至少一个企业订阅的所有者角色，或者拥有使用即用即付费率的单个订阅的所有者角色。

- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项  。 或者，如果禁用了该设置，则必须是 EA 管理员。
- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Synapse Analytics 预留容量。

**若要进行购买，请执行以下操作：**

1. 转到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)。
1. 选择一个订阅。 使用“订阅”列表，选择用于支付预留容量费用的订阅。  将向订阅的付款方式收取预留容量的费用。 订阅类型必须为企业协议（套餐编号：MS-AZR-0017P 或 MS-AZR-0148P），或即用即付（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）或 CSP 订阅。
    - 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。
    - 对于即用即付订阅，将向订阅的信用卡或发票付款方式收取费用。
1. 选择“范围”，以便选择订阅范围。 
    - **单个资源组范围** - 仅将预留折扣应用到所选资源组中匹配的资源。
    - **单个订阅范围** - 将预留折扣应用到所选订阅中匹配的资源。
    - **共享范围** - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于企业协议客户，计费上下文为注册。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。
    - 管理组 - 将预留折扣应用于同时属于管理组和计费范围的订阅列表中的匹配资源。
1. 选择“区域”，以便选择一个  预留容量涵盖的 Azure 区域，然后将预留添加到购物车。
1. 选择一个独立计划类型，然后单击“选择”。   
    ![示例 ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. 输入要预留的独立应用服务印花的数量。 例如，输入数量 3 即可每个区域获得 3 个预留印花。 单击“下一步:  查看 + 购买”。
1. 检查信息，并单击“立即购买”。 

购买后，转到[预留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，查看购买状态并随时对其进行监视。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](exchange-and-refund-azure-reservations.md)。

## <a name="discount-application-shown-in-usage-data"></a>在使用数据中显示的折扣应用情况

对于获得预留折扣的使用量，使用数据的有效价格为零。 使用数据显示每个预留中的每个印花实例的预留折扣。

若要详细了解预留折扣在使用数据中的显示情况，而且你是企业协议 (EA) 客户，请参阅[获取企业协议预留成本和使用情况](understand-reserved-instance-usage-ea.md)。 否则，请参阅[了解采用即用即付费率的个人订阅的 Azure 预留使用情况](understand-reserved-instance-usage.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [了解如何应用 Azure 应用服务独立印花预留折扣](reservation-discount-app-service.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
