---
title: Azure Cosmos DB 中用于优化成本的预留容量
description: 了解如何购买 Azure Cosmos DB 预留容量以节省计算成本。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 7726960ac4fc5a4a3a64fe8b51e23e77e157ba6d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037855"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 预留容量优化成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 预留容量可通过预付为期一年或三年的 Azure Cosmos DB 资源预留费用为你省钱。 使用 Azure Cosmos DB 预留容量，可以获得为 Cosmos DB 资源预配的吞吐量的折扣。 资源示例包括数据库和容器（表、集合和图）。

Azure Cosmos DB 预留容量可以大幅降低 Cosmos DB 成本，相比正常价格最多可以降低 65%，前提是提前承诺使用一年或三年的服务。 预留容量提供一种计费折扣，并且不会影响 Azure Cosmos DB 资源的运行时状态。

Azure Cosmos DB 预留容量涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。 购买预留后，不再像即用即付资费一样收取与预留属性相应的吞吐量费用。 预留相关详细信息，请参阅 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)一文。

可从 [Azure 门户](https://portal.azure.com)购买 Azure Cosmos DB 预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

* 必须至少有一个采用即用即付费率的企业或个人订阅的“所有者”角色。  
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”  。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Cosmos DB 预留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>在购买之前确定所需吞吐量

预留容量购买大小应该基于现有的或即将部署的 Azure Cosmos DB 资源将按小时使用的吞吐量总量。 例如：如果你的每小时使用模式一贯是 30,000 RU/秒，请购买这种大小的预留容量。 在此示例中，超过 30,000 RU/秒的任何预配吞吐量将按照即用即付费率计费。 如果在某一小时内预配吞吐量低于 30,000 RU/秒，则会浪费掉该小时内多余的预留容量。

我们将根据你的每小时使用模式计算建议购买大小。 我们将分析过去 7 天、30 天和 60 天的使用量，并推荐能够最大限制节省成本的预留容量购买大小。 可以在 Azure 门户中使用以下步骤查看建议的预留大小：

1. 登录 [Azure 门户](https://portal.azure.com)。  

2. 选择“所有服务” > “预留” > “添加”  。

3. 在“购买预留项”窗格中选择“Azure Cosmos DB” 。

4. 选择“建议”选项卡查看建议的预留项：

可按以下特性筛选建议：

- 期限（1 年或 3 年）
- 账单频率（每月或预付）
- 吞吐量类型（RU/秒或多区域写入 RU/秒）

此外，可将建议范围限定为单个资源组、单个订阅或整个 Azure 注册。 

下面是一个示例建议：

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="预留容量建议":::

此项购买 30,000 RU/秒预留容量的建议指出，对于 3 年的预留期，购买 30,000 RU/秒预留大小可以最大限度地节省成本。 在本例中，该建议大小是根据过去 30 天的 Azure Cosmos DB 使用量计算的。 如果此客户预计过去 30 天的 Azure Cosmos DB 使用量也代表了将来的使用量，则购买 30,000 RU/秒的预留大小可以最大限度地节省成本。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>购买 Azure Cosmos DB 预留容量

1. 登录 [Azure 门户](https://portal.azure.com)。  

2. 选择“所有服务” > “预留” > “添加”  。  

3. 在“购买预留项”窗格中，选择“Azure Cosmos DB”以购买新的预留项 。  

4. 填写必填字段，如下表所示：

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="填写预留容量表":::

   |字段  |说明  |
   |---------|---------|
   |作用域   |   控制有多少订阅可以使用与预留关联的计费权益的选项。 它还控制将预留应用于特定订阅的方式。 <br/><br/>  如果选择“共享”，预留折扣将应用到计费上下文内任何订阅中运行的 Azure Cosmos DB 实例。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的所有采用即用即付费率的个人订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure Cosmos DB 实例。 <br/><br/> 如果选择“单个资源组”，预留折扣将应用到所选订阅中的 Azure Cosmos DB 实例，以及该订阅中的所选资源组。 <br/><br/> 购买预留容量后，可以更改预留范围。  |
   |订阅  |   用于支付 Azure Cosmos DB 预留容量费用的订阅。 收取费用时将采用所选订阅中的付款方式。 订阅必须是以下类型之一： <br/><br/>  企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）：对于企业订阅，从注册的 Azure 预付款余额（以前称为货币承诺）中扣除费用或作为超额费用收取。 <br/><br/> 采用即用即付费率的个人订阅（产品/服务编号：MS-AZR-0003P 或 MS-AZR-0023P）：对于采用即用即付费率的个人订阅，通过信用卡收取费用或按订阅中的发票付款方式收费。    |
   | 资源组 | 要应用预留容量折扣的资源组。 |
   |术语  |   一年或三年。   |
   |吞吐量类型   |  吞吐量以请求单位预配。 可为以下两种设置购买预配吞吐量的预留大小：单区域写入和多区域写入。 可为吞吐量类型选择两个值：每小时 100 RU/秒，每小时 100 个多区域写入 RU/秒。|
   | 预留容量单位| 想预留的吞吐量的量。 可以通过确定每个区域的所有 Cosmos DB 资源（例如，数据库或容器）所需的吞吐量来计算此值。 然后，将它乘以将与 Cosmos 数据库关联的区域数。 例如：如果有五个区域，每个区域 1 百万 RU/秒，则购买预留容量时应选 5 百万 RU/秒。 |


5. 填写该表单后，将计算购买预留容量所需的价格。 输出中还会显示所选选项享受的折扣百分比。 接下来，单击“选择”

6. 在“购买预留项”窗格中，查看预留项的折扣和价格。 此预留价格适用于使用跨所有区域预配的吞吐量的 Azure Cosmos DB 资源。  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="预留容量摘要":::

7. 依次选择“查看 + 购买”、“立即购买” 。 购买成功后，会看到如下页面所示内容：

购买预留后，它立即应用到与预留期相符的所有现有 Azure Cosmos DB 资源。 如果还没有 Azure Cosmos DB 资源，则将在你部署符合预留期的新 Cosmos DB 实例时应用预留。 这两种情况的预留期均于成功购买后立即开始。

预留到期时，Azure Cosmos DB 实例会继续运行，按正常即用即付资费计费。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>后续步骤

预留折扣自动应用于与预留范围和属性相符的 Azure Cosmos DB 资源。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  要了解预留容量折扣是如何应用到 Azure Cosmos DB 的，请参阅[了解 Azure 预留折扣](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)。

* 若要了解有关 Azure 预留的详细信息，请参阅以下文章：

   * [什么是 Azure 预留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [合作伙伴中心 CSP 计划中的 Azure 预留](/partner-center/azure-reservations)

尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
* 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
* 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。