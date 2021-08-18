---
title: 通过预留容量节省计算成本
description: 了解如何购买 Azure 数据工厂数据流预留容量以节省计算成本。
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.date: 02/05/2021
ms.openlocfilehash: a46a84b9dfc1b167f41d15b4e9e5fa0e91fd1b69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740985"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>通过预留容量节省资源成本 - Azure 数据工厂数据流

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

与即用即付价格相比，通过提交计算资源预留可节省 Azure 数据工厂数据流成本。 通过预留容量，可以承诺为期一年或三年的 ADF 数据流使用情况，以获得可观的计算成本折扣。 若要购买预留容量，需要指定 Azure 区域、计算类型、内核计数和期限。

无需将预留分配给特定工厂或集成运行时。 现有工厂或新部署的工厂自动获得权益。 购买预留，你承诺在一年或三年内使用的数据流计算成本。 购买预留后，不再像即用即付资费一样收取与预留属性相应的计算费用。 

可以通过选择[提前预留或每月付款](https://portal.azure.com)购买[预留](../cost-management-billing/reservations/prepare-buy-reservation.md)。 购买预留容量：

- 你必须至少是一个采用即用即付费率的企业或个人订阅的所有者角色。
- 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。 预留容量。

有关预留购买如何针对企业客户和即用即付客户进行计费的详细信息，请参阅[了解适用于企业注册的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)和[了解即用即付订阅的 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

> [!NOTE]
> 购买预留容量不会在你使用时预分配或保留特定的基础结构资源（虚拟机或群集）。

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>在购买前确定所需的合适的 Azure IR 大小

预留大小应基于使用同一个计算层的现有或即将部署的数据流使用的总计算量。

例如，假设使用经 32 核优化的内存每小时执行一个管道。 接下来，假设你计划在下个月部署使用常规用途 64 内核的其他管道。 我们还假设你知道在至少 1 年的时间内，都需要这些资源。 在这种情况下，请输入每个计算类型 1 小时所需的内核数。 在 Azure 门户中，搜索“预留”。 选择“数据工厂”>“数据流”，并为经优化的内存输入“32”，为常规用途输入“64”。

## <a name="buy-reserved-capacity"></a>购买预留容量

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择“添加”，然后在“购买预留”窗格中，选择“ADF 数据流”，为 ADF 数据流购买新的预留。
4. 填写所选的必填字段和属性，以获取预留容量折扣。 实际获得折扣的数据流数取决于所选范围和数量。
5. 在“成本”部分查看容量预留成本。
6. 选择“购买”。
7. 选择“查看此预订”以查看购买的状态。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [了解 Azure 预留折扣](data-flow-understand-reservation-charges.md)
