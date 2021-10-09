---
title: 为具有预留容量的计算资源预付费用 - Azure Cache for Redis
description: 为具有预留容量的 Azure Cache for Redis 计算资源预付费用
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: ce88d3e6916a2b8a802fe390ac256e00dfc29aec
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537646"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>为具有预留容量的 Azure Cache for Redis 计算资源预付费用

与即用即付价格相比，Azure Cache for Redis 现在可以通过预付计算资源费用来节省成本。 通过 Azure Cache for Redis 预留容量，可以提前承诺为期一年或三年的缓存购买量，以获得可观的计算资源价格折扣。 若要购买 Azure Cache for Redis 预留容量，需要指定 Azure 区域、服务层级和期限。

无需为特定的 Azure Cache for Redis 分配预留。 已在运行或新部署的 Azure Cache for Redis 将自动获得预留定价的权益，高达预留缓存大小。 购买预留容量便会预付为期一年或三年的计算资源费用。 购买预留容量后，与预留属性匹配的 Azure Cache for Redis 计算资源费用不再按照即用即付的费率计收。 预留不包括与缓存相关联的网络或存储费用。 预留期限结束时，计费权益随即过期，Azure Cache for Redis 将按即用即付价格计费。 预留容量不自动续订。 有关定价信息，请参阅 [Azure Cache for Redis 预留容量套餐](https://azure.microsoft.com/pricing/details/cache)。

可在 [Azure 门户](https://portal.azure.com/)中购买 Azure Cache for Redis 预留容量。 购买预留容量：

* 必须具有至少一个采用即用即付费率的企业或个人订阅的所有者角色。
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com/)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Cache for Redis 预留容量。

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。


## <a name="determine-the-right-cache-size-before-purchase"></a>购买之前确定正确的缓存大小

预留容量的大小应基于特定区域中使用相同服务层级的现有或即将部署的缓存所用的内存大小总量。

例如，假设要运行两个缓存 - 一个为 13 GB，另一个为 26 GB。 运行两个缓存至少需要一年。 接下来，假设计划将现有的 13 GB 缓存扩展到 26 GB 达一个月，以满足季节性需求，然后再缩减回去。 在这种情况下，可以在一年的预留期内购买 1 个 P2 缓存和 1 个 P3 缓存或 3 个 P2 缓存，以最大限度地节省成本。 你可享受的预留的缓存内存总量的折扣，与在缓存中分配该总量的方式无关。


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>购买 Azure Cache for Redis 预留容量

可在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/)中购买虚拟机预留实例。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”，然后在购买预留容量窗格中选择“Azure Cache for Redis”，为你的缓存购买新的预留容量。 
4. 填写必填字段。 与所选属性匹配的现有或新数据库符合预留容量折扣的条件。 可享受折扣的 Azure Cache for Redis 实例的实际数目取决于所选范围和数量。


![预留定价概述](media/cache-reserved-pricing/cache-reserved-price.png)


下表描述了必填字段。

| 字段 | 说明 |
| :------------ | :------- |
| 订阅   | 用于支付 Azure Cache for Redis 预留容量预订费用的订阅。 订阅付款方式是收取 Azure Cache for Redis 预留容量的预付费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或采用即用即付定价的个人协议（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）。 对于企业订阅，从注册的 Azure 预付款（以前称为货币承诺）余额中扣除费用或按超额收取费用。 对于采用即用即付定价的个人订阅，将按订阅上的信用卡或发票付款方式收取费用。
| 范围 | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择： </br></br> 共享，预留折扣将应用到计费上下文内任何订阅中运行的 Azure Cache for Redis 实例。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有即用即付订阅。</br></br> 单个订阅，预留折扣将应用到此订阅中的 Azure Cache for Redis 实例。 </br></br> 单个资源组，预留折扣将应用到所选订阅中的 Azure Cache for Redis 实例，以及该订阅内的所选资源组。</br></br>管理组，将预留折扣应用于同时属于管理组和计费范围的订阅列表中的匹配资源。
| 区域 | 预订的 Azure Cache for Redis 预留容量涵盖的 Azure 区域。
| 定价层 | Azure Cache for Redis 服务器的服务层级。
| 术语 | 一年或三年
| 数量 | 在 Azure Cache for Redis 预留容量预定中购买的计算资源量。 数量是预留并将享受计费折扣的所选 Azure 区域和服务层级中的缓存的数量。 例如，如果在美国东部区域正在运行或计划运行包含 26 GB 总缓存容量的 Azure Cache for Redis 服务器，则需指定相当于 26 GB 的容量，以最大限度地享受所有缓存的权益。 数量可以是一个 P3 缓存或两个 P2 缓存。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="cache-size-flexibility"></a>缓存大小灵活性

缓存大小灵活性有助于在同一服务层级和区域内纵向扩展或收缩，且不会丢失预留容量权益。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

预订折扣会自动应用于与预留范围和属性相匹配的 Azure Cache for Redis 实例。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  要了解预留容量折扣如何应用到 Azure Cache for Redis，请参阅[了解 Azure 预留折扣](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* 若要了解有关 Azure 预订的详细信息，请参阅以下文章：

    * [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [管理 Azure 预留项](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [了解 Azure 预留折扣](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)