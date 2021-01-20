---
title: 利用 Azure VMware 解决方案预订实例节省成本
description: 了解如何购买 Azure VMware 解决方案的预订实例。
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 74344889cd107350e5026c4a0369382fb9c7ef51
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600859"
---
# <a name="save-costs-with-azure-vmware-solution"></a>利用 Azure VMware 解决方案节省成本

当你提交到 [Azure VMware 解决方案](introduction.md)的保留实例时，可以节省资金。  预订折扣将自动应用于与预订范围和属性匹配的运行中的 Azure VMware 解决方案主机。 购买的预订实例仅涵盖使用情况的计算部分，并包括软件许可成本。 

## <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留实例可用，但有一些例外情况。

-   **云** 预订仅在 " [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) " 页上列出的区域中可用。

-   **配额不足** -作用域为单个/共享订阅的保留必须在订阅中为新的保留实例提供主机配额。 你可以 [创建配额增加请求](enable-azure-vmware-solution.md) 以解决此问题。

-   **提供资格**-你将需要 [AZURE 企业协议 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 与 Microsoft 配合使用。

-   **容量限制** -在极少数情况下，azure 会限制购买新保留的 Azure VMware 解决方案主机 sku，因为区域中的容量不足。

## <a name="buy-a-reservation"></a>购买预留项

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure VMware 解决方案主机实例的保留实例。

您可以提前支付预订费用，也可以 [按月](../cost-management-billing/reservations/prepare-buy-reservation.md)支付。

这些要求适用于购买保留的专用主机实例：

-   对于至少一个 EA 订阅或具有即用即付费率的订阅，必须是 *所有者* 角色。

-   对于 EA 订阅，必须在 [ea 门户](https://ea.azure.com/)中启用 "**添加保留实例**" 选项。 如果禁用，你必须是订阅的 EA 管理员才能启用它。

-   对于云解决方案提供商提供的订阅 (CSP) Azure 计划，合作伙伴必须在 Azure 门户为客户购买保留实例。 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>为 EA 订阅购买保留实例

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务” > “预订”。

3. 选择 " **立即购买** "，并选择 " **Azure VMware 解决方案**"。

4. 输入必填字段。 与正在运行的 Azure VMware 解决方案相匹配的所选属性托管了预订折扣的资格。  属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

   如果你有 EA 协议，则可以使用 " **添加更多" 选项** 快速添加实例。 选项不适用于其他订阅类型。

   | 字段        |  说明 |
   | ------------ | ------------ |
   | 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须是 (产品/服务的企业协议： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) 、Microsoft 客户协议，或者使用即用即付费率的单个订阅 (产品编号： MS-BC-OP-NT-AZR-Ms-azr-0003p 或 bc-op-nt-azr-0023P) 。 从 Azure 预付款 (之前称为货币承诺) 余额（如果可用）或按超额支付收费。 对于使用即用即付费率的订阅，会对订阅的信用卡或发票付款方式收取费用。 |
   | 范围        | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<br><ul><li><b>单个资源组作用域</b> -仅将预订折扣应用于所选资源组中的匹配资源。</li><li><b>单个订阅范围</b> -将预订折扣应用于所选订阅中的匹配资源。</li><li><b>共享作用域</b> -将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文为 "注册"。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>       |
   | 区域       | 预订涵盖的 Azure 区域。   |
   | 主机大小    | AV36    |
   | 术语         | 一年或三年。  |
   | 数量     | 要在保留中购买的实例数。 数量是可以获得计费折扣的正在运行的 Azure VMware 解决方案主机数。    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>为 CSP 订阅购买保留实例

需要为其客户购买预订实例的 Csp 必须从 [合作伙伴中心文档](/partner-center/azure-plan-manage)中代表 (AOBO) 过程使用 **管理员**。 有关详细信息，请 [) 视频上代表 (AOBO 查看管理员 ](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 。

1. 登录[合作伙伴中心](https://partner.microsoft.com)。

2. 选择 " **CSP** " 以访问 " **客户** " 区域。

3. 展开 "客户详细信息" 并选择 **Microsoft Azure 管理门户**。 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft 合作伙伴中心客户区域" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. 在 Azure 门户中，选择 "**所有服务**  >  **保留**"。

5. 选择 " **立即购买** "，并选择 " **Azure VMware 解决方案**"。

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure 门户预订" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. 输入必填字段。 与正在运行的 Azure VMware 解决方案相匹配的所选属性托管了预订折扣的资格。  属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

   | 字段        |  说明 |
   | ------------ | ------------ |
   | 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须是符合条件的类型，在本例中为 CSP 订阅|
   | 范围        | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<br><ul><li><b>单个资源组作用域</b> -仅将预订折扣应用于所选资源组中的匹配资源。</li><li><b>单个订阅范围</b> -将预订折扣应用于所选订阅中的匹配资源。</li><li><b>共享作用域</b> -将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文为 "注册"。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>       |
   | 区域       | 预订涵盖的 Azure 区域。   |
   | 主机大小    | AV36    |
   | 术语         | 一年或三年。  |
   | 数量     | 要在保留中购买的实例数。 数量是可以获得计费折扣的正在运行的 Azure VMware 解决方案主机数。     |

若要了解有关如何查看客户购买的预订的详细信息，请参阅 [查看 Azure 预订作为云解决方案提供商 (CSP) ](../cost-management-billing/reservations/how-to-view-csp-reservations.md) 文章。

## <a name="usage-data-and-reservation-usage"></a>使用情况数据和预订使用情况

获取预订折扣的使用量的有效价格为零。 你可以看到哪个 Azure VMware 解决方案实例收到每个预订的预订折扣。

有关如何在使用情况数据中显示预订折扣的详细信息：

- 对于 EA 客户，请参阅 [了解企业注册的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- 对于单独的订阅，请参阅 [了解即用即付订阅的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>在购买后更改保留

可以在购买后对保留进行以下更改：

-   更新预留范围

-   实例大小灵活性 (（如果适用）) 

-   所有权

你还可以将预订拆分为较小的区块或合并保留。 任何更改都不会导致新的商业交易，也不会更改预订的结束日期。

有关 CSP 管理的预订的详细信息，请参阅 [使用合作伙伴中心、Azure 门户或 api 向客户销售 Microsoft Azure 预订](/partner-center/azure-reservations)。



>[!NOTE]
>购买保留后，将无法直接进行这些类型的更改：
>
> - 现有预订的区域
> - SKU
> - 数量
> - 持续时间
>
>不过，如果想要进行更改，则可以 *交换* 预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

Csp 可以取消、交换或退款，并为其客户购买某些限制。 有关详细信息，请参阅 [客户的管理、取消、交换或退款 Microsoft Azure 预订](/partner-center/azure-reservations-manage)。