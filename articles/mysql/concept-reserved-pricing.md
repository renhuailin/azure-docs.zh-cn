---
title: 为具有预留容量的计算资源预付费用 - Azure Database for MySQL
description: 为具有预留容量的 Azure Database for MySQL 计算资源预付费用
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 976ed2f1af2fe4481e8327e4e83b7ba6c5b61fa2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675775"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>为具有预留容量的 Azure Database for MySQL 计算资源预付费用

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL 现在可以让你通过预付计算资源费用来节省资金（与即用即付价格相比）。 使用 Azure Database for MySQL 预留容量，你可以提前承诺为期一年或三年的 MySQL 服务器使用量，以获得可观的计算资源价格折扣。 若要购买 Azure Database for MySQL 预留容量，需要指定 Azure 区域、部署类型、性能层和期限。 </br>

不需要将预留容量分配到特定 Azure Database for MySQL 服务器。 已在运行的或新部署的 Azure Database for MySQL 会自动获得预留定价的权益。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买预留容量后，与预留属性匹配的 Azure Database for MySQL 计算资源费用不再按照即用即付的费率计收。 预留容量不包括与 MySQL 数据库服务器关联的软件、网络或存储费用。 预留期限结束时，计费权益随即过期，Azure Database for MySQL 将按即用即付价格计费。 虚拟机预留实例不自动续订。 有关定价信息，请参阅 [Azure Database for MySQL 预留容量产品/服务](https://azure.microsoft.com/pricing/details/mysql/)。 </br>

可在 [Azure 门户](https://portal.azure.com/)中购买 Azure Database for MySQL 预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

* 必须具有至少一个采用即用即付费率的企业或个人订阅的所有者角色。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Database for MySQL 预留容量。 </br>

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业合约的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。


## <a name="determine-the-right-database-size-before-purchase"></a>购买之前确定正确的数据库大小

预留容量的大小应基于特定区域中使用相同性能层和硬件代系的现有的或即将部署的服务器所用的计算资源总量。</br>

例如，假设你要运行一个常规用途的第 5 代 32 vCore MySQL 数据库，以及两个内存优化的第 5 代 16 vCore MySQL 数据库。 此外，假设你打算在下个月额外部署一个常规用途的第 5 代 32 vCore 数据库服务器，以及一个内存优化的第 5 代 16 vCore 数据库服务器。 我们假设你知道在至少 1 年的时间内，你都需要这些资源。 在这种情况下，应该购买 64 (2x32) 个 vCore 的 1 年期常规用途第 5 代单一数据库预留容量，以及 48 (2x16 + 16) 个 vCore 的 1 年期内存优化第 5 代单一数据库预留容量


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>购买 Azure Database for MySQL 预留容量

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”，然后在“购买预留容量”窗格中选择“Azure Database for MySQL”，为你的 MySQL 数据库购买新的预留容量。 
4. 填写必填字段。 与所选属性匹配的现有的或新的数据库符合预留容量折扣的条件。 获得折扣的 Azure Database for MySQL 服务器的实际数目取决于所选范围和数量。


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="预留定价概述":::


下表描述了必填字段。

| 字段 | 说明 |
| :------------ | :------- |
| 订阅   | 用于支付 Azure Database for MySQL 预留容量预订费用的订阅。 订阅付款方式是收取 Azure Database for MySQL 预留容量的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或采用即用即付定价的个人协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。 对于采用即用即付定价的个人订阅，将按订阅上的信用卡或发票付款方式收取费用。
| 范围 | vCore 预留范围可以包括一个订阅或多个订阅（共享范围）。 如果选择： </br></br> “共享”，vCore 预留折扣将应用到计费上下文中任何订阅中运行的 Azure Database for MySQL 服务器。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br>“管理组”，预留折扣将应用于在同时属于管理组和计费范围的任何订阅中运行的 Azure Database for MySQL 服务器。</br></br> “单个订阅”，vCore 预留折扣将应用到此订阅中的 Azure Database for MySQL 服务器。 </br></br> “单个资源组”，预留折扣将应用到所选订阅中的 Azure Database for MySQL 服务器，以及该订阅内的所选资源组。
| 区域 | 预订的 Azure Database for MySQL 预留容量涵盖的 Azure 区域。
| 部署类型 | 要为其购买预留容量的 Azure Database for MySQL 资源类型。
| 性能层 | Azure Database for MySQL 服务器的服务层。
| 术语 | 一年
| 数量 | 在 Azure Database for MySQL 预留容量预订中购买的计算资源量。 数量是预留并将享受计费折扣的所选 Azure 区域和性能层中的 vCores 的数量。 例如，如果在“美国东部”区域运行或计划运行具有 Gen5 16 vCore 总计算容量的 Azure Database for MySQL 服务器，则将数量指定为 16，以将所有服务器的权益最大化。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="vcore-size-flexibility"></a>vCore 大小灵活性

vCore 大小灵活性有助于在同一性能层和区域内纵向扩展或收缩，且不会丢失预留容量权益。 

## <a name="need-help--contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

vCore 预留折扣自动应用到与 Azure Database for MySQL 预留容量预订范围和属性匹配的 Azure Database for MySQL 服务器数目。 可以通过 Azure 门户、PowerShell、CLI 或 API 更新 Azure Database for MySQL 预留容量的预订范围。 </br></br>
若要了解如何管理 Azure Database for MySQL 预留容量，请参阅“管理 Azure Database for MySQL 保留容量”。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

* [什么是 Azure 预留项](../cost-management-billing/reservations/save-compute-costs-reservations.md)？
* [管理 Azure 预留项](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [了解 Azure 预留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
* [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)