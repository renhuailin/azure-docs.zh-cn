---
title: 节省预留容量的计算成本
description: 了解如何购买 Azure 数据工厂数据流保留容量以节省计算成本。
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 01/25/2021
ms.openlocfilehash: 8bea1ff0dcb945e969553a4f643e289a5157f966
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811631"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>节省预留容量的资源成本-Azure 数据工厂数据流

与即用即付价格相比，通过提交到计算资源的预留来节省 Azure 数据工厂的数据流成本。 利用预留容量，你可以在一年或三年的时间内对 ADF 数据流使用作出承诺，以获得有关计算成本的重要折扣。 若要购买保留容量，需要指定 Azure 区域、计算类型、核心计数和术语。

不需要将保留分配给特定的工厂或集成运行时。 现有工厂或新部署的工厂自动获得权益。 通过购买预留，你可以在一年或三年的时间内提交数据流计算成本的使用情况。 一旦购买了预订，就不再按即用即付费率对与预订属性匹配的计算费用收费。 

可以在 [Azure 门户](https://portal.azure.com)中购买保留容量。 通过[提前付款或按月付款](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

- 对于至少一个企业或个人订阅，你必须以即用即付费率作为所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。 保留容量。

有关企业客户和即用即付客户如何针对预订购买付费的详细信息，请参阅 [了解企业注册的 azure 保留使用情况](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 和 [了解即用即付订阅的 azure 保留使用情况](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage.md)。

> [!NOTE]
> 购买保留容量不会为你使用)  (虚拟机或群集预分配或保留特定的基础结构资源。

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>确定在购买前需要适当的 Azure IR 大小

预留的大小应基于使用相同计算层的现有或即将部署的数据流所使用的计算总量。

例如，假设你正在使用使用32核心优化的内存每小时执行一次管道。 接下来，假设你计划在下个月中部署使用常规用途为64核心的其他管道。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，应购买32个核心1年保留的内存优化数据流和一般用途64核1年的保留期。

## <a name="buy-reserved-capacity"></a>购买预留容量

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”   > “预订”  。
3. 选择 " **添加** "，然后在 " **购买保留** " 窗格中，选择 " **ADF** 数据流"，为 ADF 数据流购买新的保留。
4. 填写所选的必填字段和属性，以获取预留容量折扣。 获取折扣的实际数据流数取决于所选的范围和数量。
5. 在 " **成本** " 部分中查看容量预留的成本。
6. 选择“购买”。 
7. 选择“查看此预订”以查看购买的状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [了解 Azure 预留折扣](data-flow-understand-reservation-charges.md)
