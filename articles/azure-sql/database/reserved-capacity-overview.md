---
title: 通过预留容量节省计算成本
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 了解如何购买 Azure SQL 数据库和 SQL 托管实例预留容量以节省计算成本。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 10/13/2020
ms.openlocfilehash: e3ff2d28aa3e2b54a43c65057f59a21534806e87
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658804"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>通过预留容量节省资源成本 - Azure SQL 数据库和 SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

与即用即付价格相比，通过承诺预留计算资源，Azure SQL 数据库和 SQL 托管实例可以节省资金。 通过预留容量，你可以承诺使用一年或三年 SQL 数据库和/或 SQL 托管实例，以获得可观的计算成本折扣。 若要购买预留容量，需要指定 Azure 区域、部署类型、性能层和期限。

不需要将预留分配给特定的数据库或托管实例。 已在运行的现有匹配部署或者新部署的匹配部署将自动获得此权益。 通过购买预留，你承诺了在一年或三年内使用的计算成本。 购买预留后，与预留属性匹配的计算费用不再按照即用即付的费率计收。 

预留同时适用于主要计算副本和可计费辅助计算副本，但不包括与服务相关联的软件、网络或存储费用。 在预留期限结束时，计费权益随即过期，数据库或托管实例将按即用即付价格计费。 预留不自动续订。 有关定价信息，请参阅[预留容量产品/服务](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

可在 [Azure 门户](https://portal.azure.com)中购买预留容量。 通过[提前付款或按月付款](../../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

- 你必须至少是一个采用即用即付费率的企业或个人订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。 预留容量。

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业合约的 Azure 预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

> [!NOTE]
> 购买预留容量不会预分配或保留供你使用的特定基础结构资源（虚拟机或节点）。

## <a name="determine-correct-size-before-purchase"></a>在购买之前确定正确的大小

预留的大小应基于特定区域中使用相同性能层和硬件代系的现有的或即将部署的数据库或托管实例所用的计算资源总量。

例如，假设你要运行一个常规用途的第 5 代 16 vCore 弹性池，以及两个业务关键型的第 5 代 4 vCore 单一数据库。 此外，假设你打算在下个月额外部署一个常规用途的第 5 代 16 vCore 弹性池，以及一个业务关键型的第 5 代 32 vCore 弹性池。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，应该购买 32 (2x16) 个 vCore 的 1 年期“常规用途 - 第 5 代”单一数据库/弹性池预留容量，以及 40 (2x4 + 32) 个 vCore 的 1 年期“业务关键 - 第 5 代”单一数据库/弹性池预留容量。

## <a name="buy-reserved-capacity"></a>购买预留容量

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”，然后在“购买预留”窗格中选择“SQL 数据库”以购买 SQL 数据库的新预留。  
4. 填写必填字段。 SQL 数据库和 SQL 托管实例中与所选属性匹配的现有数据库符合享受预留容量折扣的条件。 享受折扣的数据库或托管实例的实际数目取决于所选范围和数量。

    ![提交预留容量订单之前的屏幕截图](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    下表描述了必填字段。
    
    | 字段      | 说明|
    |------------|--------------|
    |订阅|用于支付产能预留费用的订阅。 将向订阅的付款方式收取预订的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或采用即用即付定价的个人协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。 对于采用即用即付定价的个人订阅，将按订阅上的信用卡或发票付款方式收取费用。|
    |范围       |vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择 <br/><br/>“共享”，则 vCore 预留折扣将应用于计费上下文中任何订阅中运行的数据库或托管实例。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。<br/><br/>“单个订阅”，则 vCore 预留折扣将应用于此订阅中的数据库或托管实例。 <br/><br/>“单个资源组”，则预留折扣将应用于所选订阅中的数据库实例或托管实例，以及该订阅中的所选资源组。</br></br>管理组，预留折扣将应用于同时属于管理组和计费范围的订阅列表中的匹配资源。|
    |区域      |产能预留涵盖的 Azure 区域。|
    |部署类型|要为其购买预留容量的 SQL 资源类型。|
    |性能层|数据库或托管实例的服务层级。 |
    |术语        |一年或三年。|
    |数量    |在产能预留中购买的计算资源量。 数量是预留并将享受计费折扣的所选 Azure 区域和性能层中的 vCores 的数量。 例如，如果在“美国东部”区域运行或计划运行总计算容量为 Gen5 16 vCore 的多个数据库，则将数量指定为 16，以使所有数据库的权益最大化。 |

1. 在“成本”部分查看产能预留成本。
1. 选择“购买”。
1. 选择“查看此预订”以查看购买的状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 预留容量还提供了灵活性，使你可以在正常操作过程中临时将正在使用的数据库移入和移出弹性池（在同一区域和性能层内），而不会失去预留容量权益。 通过在预留中保留一个未应用的缓冲区，你可以在不超出预算的情况下有效地管理性能峰值。

## <a name="limitation"></a>限制

不能在 SQL 数据库中预留基于 DTU 的（基本、标准或高级）数据库。 仅处于“正式发布”状态的功能和产品支持预留容量定价。 

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣自动应用于与产能预留范围和属性匹配的数据库或托管实例数目。 可以通过 [Azure 门户](https://portal.azure.com)、PowerShell、Azure CLI 或 API 更新产能预留的范围。

- 有关 vCore 模型的 Azure SQL 数据库服务层级的信息，请参阅 [vCore 模型概述 - Azure SQL 数据库](service-tiers-sql-database-vcore.md)。
- 有关 vCore 模型的 Azure SQL 托管实例服务层级的信息，请参阅 [vCore 模型概述 - Azure SQL 托管实例](../managed-instance/service-tiers-managed-instance-vcore.md)。

若要了解如何管理产能预留，请参阅[管理预留容量](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [管理 Azure 预留项](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [了解 Azure 预留折扣](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)
