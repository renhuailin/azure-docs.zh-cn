---
title: 预留计算定价 - Azure Database for PostgreSQL - 超大规模 (Citus)
description: 通过预留容量预付费购买 Azure Database for PostgreSQL 超大规模 (Citus) 计算资源。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 422f8533c78d2242e1ef520d42dd09982990871a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577966"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>通过预留容量预付费购买 Azure Database for PostgreSQL - 超大规模 (Citus) 计算资源

与即用即付价格相比，Azure Database for PostgreSQL 超大规模 (Citus) 现可通过预付计算资源费用来帮助你节省成本。 通过超大规模 (Citus) 预留容量，可提前承诺使用 1 年或 3 年的超大规模 (Citus) 服务器组，以获得可观的计算价格折扣。 若要购买超大规模 (Citus) 预留容量，需要指定 Azure 区域、预留期限和记帐频率。

> [!IMPORTANT]
> 本文介绍 Azure Database for PostgreSQL 超大规模 (Citus) 的预留容量。 若要了解 Azure Database for PostgreSQL 单一服务器的预留容量，请查看[通过预留容量预付费购买 Azure Database for PostgreSQL 单一服务器计算资源](./concept-reserved-pricing.md)。

不需要向特定的超大规模 (Citus) 服务器组分配预留。 已在运行的超大规模 (Citus) 服务器组或新部署的服务器组将自动获享预留价格。 购买预留容量时，将预付 1 年或 3 年的计算费用。 一旦购买预留容量后，与预留属性匹配的超大规模 (Citus) 计算费用就不再按照即用即付的费率计收。 

预留容量不涵盖与超大规模 (Citus) 服务器组关联的软件、网络或存储费用。 预留期限结束时，计费权益随即过期，超大规模 (Citus) 服务器组将按即用即付价格计费。 预留容量不会自动续订。 有关定价信息，请查看 [Azure Database for PostgreSQL - 超大规模 (Citus) 预留容量套餐](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

可在 [Azure 门户](https://portal.azure.com/)中购买超大规模 (Citus) 预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 若要购买预留容量：

* 必须至少有一个采用即用即付费率的企业协议 (EA) 或个人订阅的所有者角色。
* 对于企业协议订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”。 如果禁用了该设置，则必须是订阅的企业协议管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买超大规模 (Citus) 预留容量。

要了解企业协议客户和即用即付客户如何对预留购买付费，请参阅：
- [了解企业协议注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>购买之前确定正确的服务器组大小

预留容量的大小基于特定区域内超大规模 (Citus) 服务器组中的现有或即将部署的协调器和工作器节点所使用的计算总量。

例如，假设你正在运行一个超大规模 (Citus) 服务器组，其中包含一个 16 个 vCore 的协调器和三个 8 个 vCore 的工作器节点。 接下来，假设你计划在下个月内部署一个额外的超大规模 (Citus) 服务器组，其中包含一个 32 个 vCore 的协调器和两个 4 个 vCore 的工作器节点。 我们还假设你需要使用这些资源至少一年。

在这种情况下，请购买 1 年的预留容量：

* 协调器节点共计 16 个 vCore + 32 个 vCore = 48 个 vCore
* 工作器节点共计 3 个节点 x 8 个 vCore + 2 个节点 x 4 个 vCore = 24 + 8 = 32 个 vCore

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>购买 Azure Database for PostgreSQL 预留容量

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择“所有服务” > “预订”。
1. 选择“添加”。 在“购买预留容量”窗格中选择“Azure Database for PostgreSQL”，为你的 PostgreSQL 数据库购买新的预留容量 。
1. 选择要购买的“超大规模(Citus)计算”类型，然后单击“选择” 。
1. 在“产品”选项卡上查看所选计算类型的数量。
1. 继续转到“购买 + 查看”选项卡完成购买。

下表描述了必填字段。

| 字段        | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 订阅 | 用于支付 Azure Database for PostgreSQL 预留容量预定费用的订阅。 订阅付款方式是收取 Azure Database for PostgreSQL 预留容量的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或采用即用即付定价的个人协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业协议订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。 对于采用即用即付定价的个人订阅，将按订阅上的信用卡或发票付款方式收取费用。                                                                                  |
| 范围        | vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择“共享”，vCore 预留折扣将应用于计费上下文内任何订阅中运行的超大规模 (Citus) 服务器组。 对于企业协议客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。 如果选择“管理组”，则预留折扣将应用于在同时属于管理组和计费范围的任何订阅中运行的超大规模 (Citus) 服务器组。 如果选择“单个订阅”，则 vCore 预留折扣将应用到此订阅中的超大规模 (Citus) 服务器组。 如果选择“单个资源组”，预留折扣将应用到所选订阅中的超大规模 (Citus) 服务器组，以及该订阅内的所选资源组。 |
| 区域       | 预订的 Azure Database for PostgreSQL 超大规模 (Citus) 预留容量涵盖的 Azure 区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 术语         | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 数量     | 在超大规模 (Citus) 预留容量预留中购买的计算资源量。 具体而言，即所选 Azure 区域中要预留的协调器或工作器节点 vCore 的数量，这些 vCore 将获得计费折扣。 例如，如果你在美国东部区域运行（或计划运行）总计算容量为 64 个协调器节点 vCore 和 32 个工作器节点 vCore 的超大规模 (Citus) 服务器组，请分别将协调器和工作器节点的数量指定为 64 和 32，以将所有服务器的权益最大化。                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留项的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一区域内纵向扩展或缩减协调器和工作器节点，且不会丢失预留容量权益。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣自动应用到与 Azure Database for PostgreSQL 预留容量预留范围和属性匹配的超大规模 (Citus) 服务器组数目。 可通过 Azure 门户、PowerShell、Azure CLI 或 API 更新 Azure Database for PostgreSQL 超大规模 (Citus) 预留容量预留范围。

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

* [什么是 Azure 预留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [了解 Azure 预留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
* [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [了解企业协议注册的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [合作伙伴中心云解决方案提供商计划中的 Azure 预留](/partner-center/azure-reservations)