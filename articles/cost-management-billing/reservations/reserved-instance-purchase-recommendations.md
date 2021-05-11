---
title: Azure 预留建议
description: 了解 Azure 预留建议。
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767918"
---
# <a name="reservation-recommendations"></a>预留建议

Azure 预留实例 (RI) 购买建议通过 Azure 消耗[预留建议 API](/rest/api/consumption/reservationrecommendations)、[Azure 顾问](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)和 Azure 门户中的预留购买体验提供。

以下步骤定义如何计算建议：

1. 建议引擎评估在过去 7 天、30 天和 60 天内给定范围内资源的每小时使用情况。
2. 引擎在模拟成本时可以使用预留，也可以不使用预留，具体取决于使用情况数据。
3. 针对不同数量来模拟成本，建议使用可以将节省最大化的数量。
4. 如果资源定期关闭，则模拟会找不到任何节省项目，因此不提供购买建议。
5. 计算购买建议时会考虑按需使用率上可能享有的所有特殊折扣。

## <a name="recommendations-in-the-azure-portal"></a>Azure 门户中的建议

预留购买建议还会显示在购买体验的 Azure 门户中。 显示建议时会附带建议的数量。 在购买时，Azure 建议的数量将实现最大的费用节省。 虽然可购买你喜欢的任何数量，但如果你购买其他数量，将达不到最佳成本节约。

让我们通过一些示例来探索原因。

在以下示例图中，对于所选的建议，Azure 建议的购买数量是 6。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="显示预留购买建议的示例" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

选择“查看详细信息”时，将显示该建议的详细信息。 下图显示了有关此建议的详细信息。 建议的数量是根据历史使用情况针对可能的最高使用量计算得出的。 如果使用量不一致，则建议可能不是针对 100% 利用率。 在本例中请注意，利用率在一段时间内波动。 系统将显示预留的成本、可能的费用节省和利用率百分比。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="显示预留购买建议详细信息的示例" :::

增加建议的数量时，图表和估计值会发生变化。 如果增加预留数量，你节省的费用将会减少，原因是预留利用率最终会降低。 换句话说，你将为未完全使用的预留付费。

如果减少预留数量，节省的费用也会减少。 尽管利用率会提高，但很可能在某些时段内，预留无法完全满足使用需求。 超出预留数量的使用量将由较昂贵的即用即付资源使用。 以下示例图说明了这一点。 我们已手动将预留数量减少到 4。 预留利用率会增加，但节省的总费用会减少，因为产生了即用即付费用。

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="显示已更改的预留购买建议详细信息的示例" :::

若要最大程度地节省预留费用，请尝试尽可能按照建议购买预留。

## <a name="recommendations-in-azure-advisor"></a>Azure 顾问中的建议

Azure 顾问中提供了预留购买建议。 请注意以下几点：

- 顾问只有单一订阅范围的建议。 若要查看整个计费范围（计费帐户或计费对象信息）的相关建议：
  -  在 Azure 门户中，导航到“预留” > “添加”，然后选择要查看其建议的类型 。
- 顾问中提供的建议考虑到了你过去 30 天的使用趋势。
- 建议数量和节省的费用针对的是 3 年期预留（若可用）。 如果此服务不出售 3 年期预留，则按 1 年期预留价格计算建议。
- 计算购买建议时会考虑按需使用率上可能享有的所有特殊折扣。
- 如果购买范围共享的预留，则顾问预留购买建议需要几天才会消失（不超过 5 天）。

## <a name="other-expected-api-behavior"></a>其他预期的 API 行为

- 当使用七天的回溯期时，如果 VM 关闭超过一天，则可能无法获得建议。

## <a name="next-steps"></a>后续步骤
- 获取[使用 REST API 的预留建议](/rest/api/consumption/reservationrecommendations/list)。
- 了解 [Azure 预留折扣如何应用于虚拟机](../manage/understand-vm-reservation-charges.md)。
