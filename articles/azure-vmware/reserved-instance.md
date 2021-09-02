---
title: Azure VMware 解决方案的预留实例
description: 了解如何购买 Azure VMware 解决方案的预留实例。 预留实例仅涵盖使用情况的计算部分，并包括软件许可成本。
ms.topic: how-to
ms.date: 05/13/2021
ms.openlocfilehash: 6204e7cade66ee60885db20226981980f4741a76
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323669"
---
# <a name="save-costs-with-azure-vmware-solution"></a>利用 Azure VMware 解决方案节省成本

当你提交到 [Azure VMware 解决方案](introduction.md)的预留实例时，可以节省资金。 预留折扣会自动应用到与预留范围和属性相匹配的正在运行的 Azure VMware 解决方案主机。 此外，购买的预留实例仅涵盖使用情况的计算部分，并包括软件许可成本。 

## <a name="purchase-restriction-considerations"></a>购买限制注意事项

预留实例有一些例外情况。

-   **云** - 预留仅在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)页上列出的区域中可用。

-   **配额不足** - 范围限定为单个/共享订阅的预留实例必须在订阅中具有可用于新预留实例的主机配额。 你可以[创建配额增加请求](request-host-quota-azure-vmware-solution.md)以解决此问题。

-   **产品/服务资格** - 你将需要 Microsoft 的 [Azure 企业协议 (EA)](../cost-management-billing/manage/ea-portal-agreements.md)。

-   **容量限制** - 在极少数情况下，Azure 限制购买的 Azure VMware 解决方案主机 SKU 的新预留实例，因为某个区域的容量不足。

## <a name="buy-a-reservation"></a>购买预留项

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure VMware 解决方案主机实例的预留实例。

可通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。

购买预留专用主机实例时需满足以下要求：

-   你必须具有至少一个 EA 订阅或采用即用即付费率的订阅的“所有者”角色。

-   对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”选项。 如果禁用了该选项，则你必须是订阅的 EA 管理员才能启用它。

-   对于云解决方案提供商 (CSP) Azure 计划下的订阅，合作伙伴必须在 Azure 门户购买客户的预留实例。 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>为 EA 订阅购买预留实例

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务” > “预订”。

3. 选择“立即购买”，然后选择“Azure VMware 解决方案”。

4. 输入必填字段。 与正在运行的 Azure VMware 解决方案主机相匹配的所选属性可享受预留折扣。  属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

   如果你有 EA 协议，则可使用“添加更多选项”来快速添加实例。 此选项不可用于其他订阅类型。

   | 字段        |  说明 |
   | ------------ | ------------ |
   | 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须为企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）、Microsoft 客户协议或采用即用即付费率的个人订阅（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 从 Azure 预付款（以前称为货币承诺）余额中扣除费用（如果可用）或按超额收取费用。 对于采用即用即付费率的订阅，将向订阅的信用卡或发票付款方式收取费用。 |
   | 范围        | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<br><ul><li><b>单个资源组范围</b> - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li><b>单个订阅范围</b> - 将预留折扣应用到所选订阅中匹配的资源。</li><li><b>共享范围</b> - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 因此，对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>       |
   | 区域       | 预订涵盖的 Azure 区域。   |
   | 主机大小    | AV36    |
   | 术语         | 一年或三年。  |
   | 数量     | 预留中购买的实例数。 数量是可以获得计费折扣的正在运行的 Azure VMware 解决方案主机数。    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>为 CSP 订阅购买预留实例

需要为其客户购买预留实例的 CSP 必须使用[合作伙伴中心文档](/partner-center/azure-plan-manage)中的“管理员代表”(AOBO) 过程。 有关详细信息，请观看[管理员代表 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 视频。

1. 登录[合作伙伴中心](https://partner.microsoft.com)。

2. 选择“CSP”以访问“客户”区域。

3. 展开客户详细信息并选择“Microsoft Azure 管理门户”。 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="显示 Microsoft 合作伙伴中心客户区域的屏幕截图，其中选中了 Microsoft Azure 管理门户。" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. 在 Azure 门户中，选择“所有服务” > “预留”。

5. 选择“立即购买”，然后选择“Azure VMware 解决方案”。

   :::image type="content" source="media/reserved-instances/csp-buy-reserved-instance-azure-portal.png" alt-text="显示在 Microsoft Azure 门户中购买 Azure VMware 解决方案预留的屏幕截图。" lightbox="media/reserved-instances/csp-buy-reserved-instance-azure-portal.png":::

6. 输入必填字段。 与正在运行的 Azure VMware 解决方案主机相匹配的所选属性可享受预留折扣。  属性包括 SKU、区域（如果适用）和范围。 预留范围选择预留节省的应用场合。

   | 字段        |  说明 |
   | ------------ | ------------ |
   | 订阅 | 为预留提供资金的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须是符合条件的类型，在本例中为 CSP 订阅|
   | 范围        | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<br><ul><li><b>单个资源组范围</b> - 仅将预留折扣应用到所选资源组中匹配的资源。</li><li><b>单个订阅范围</b> - 将预留折扣应用到所选订阅中匹配的资源。</li><li><b>共享范围</b> - 将预留折扣应用到计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是“注册”。 因此，对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>       |
   | 区域       | 预订涵盖的 Azure 区域。   |
   | 主机大小    | AV36    |
   | 术语         | 一年或三年。  |
   | 数量     | 预留中购买的实例数。 数量是可以获得计费折扣的正在运行的 Azure VMware 解决方案主机数。     |

若要详细了解如何查看为客户购买的预留，请参阅[以云解决方案提供商 (CSP) 的身份查看 Azure 预留](../cost-management-billing/reservations/how-to-view-csp-reservations.md)一文。

## <a name="usage-data-and-reservation-usage"></a>使用情况数据和预留使用情况

对于获得预留折扣的使用情况，使用数据的有效价格为零。 可以看到哪个 Azure VMware 解决方案实例收到每个预留的预留折扣。

有关如何在使用情况数据中显示预留折扣的详细信息：

- 对于 EA 客户，请参阅[了解适用于企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- 对于个人订阅，请参阅[了解用于即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>在购买后更改预留

可以在购买后对预留进行以下更改：

-   更新预留范围

-   实例大小灵活性（如果适用）

-   所有权

还可以将预留拆分为较小的区块或合并预留。 任何更改都不会导致新的商业行为，也不会更改预留的结束日期。

有关 CSP 托管的预留的详细信息，请参阅[使用合作伙伴中心、Azure 门户或 API 向客户销售 Microsoft Azure 预留](/partner-center/azure-reservations)。



>[!NOTE]
>购买预留后，将无法直接进行以下类型的更改：
>
> - 现有预留的区域
> - SKU
> - 数量
> - 持续时间
>
>但是，如果你要进行更改，可以交换预留项。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

CSP 可以取消、交换为其客户购买的预留或退款，但有一些限制。 有关详细信息，请参阅[管理、取消、交换或退款客户的 Microsoft Azure 预留](/partner-center/azure-reservations-manage)。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure VMware 解决方案的预留实例，接下来你可能想要了解以下内容：

- [创建 Azure VMware 解决方案评估](../migrate/how-to-create-azure-vmware-solution-assessment.md)。
- [配置适用于 Azure VMware 解决方案的 DHCP](configure-dhcp-azure-vmware-solution.md)。
- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)。
 
