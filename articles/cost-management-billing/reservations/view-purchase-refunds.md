---
title: 查看 Azure 预留购买和退款交易
description: 了解如何查看 Azure 预留购买和退款交易。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 06/14/2021
ms.author: banders
ms.openlocfilehash: f2b98768f10ad2c85a1808907e309a99bf685131
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646770"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>查看预留购买和退款交易

有几种不同方法可以查看预留购买和退款交易。 可以使用 Azure 门户、Power BI 和 REST API。 你可以在事务中将交换的预留视为退款和购买。

## <a name="view-reservation-purchases-in-the-azure-portal"></a>在 Azure 门户中查看预留购买情况

企业协议和 Microsoft 客户协议计费读者可以在“成本分析”中查看预留的累积购买情况。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到“成本管理 + 计费”。
1. 选择左侧菜单中的“成本分析”。
1. 对“定价模型”应用筛选器，然后选择“预留” ****  。
1. 若要查看预留的购买情况，请对“费用类型”应用筛选器，然后选择“购买” ****  。
1. 将“粒度”设置为“每月” ****   **** 。
1. 将图表类型设置为“柱形图(堆叠)”。 ****

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="显示成本分析中的预留购买情况的屏幕截图。" lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>在 Azure 门户中查看预留交易

企业注册或 Microsoft 客户协议计费管理员可以在成本管理和计费中查看预留交易。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。
1. 选择“预留交易”。
1. 若要筛选结果，请选择“时间范围”、“类型”或“说明”。  
1. 选择“应用”。

[![显示 Azure 门户中的预留交易的屏幕截图。](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>在 Power BI 中查看预留交易

企业注册费管理员可以通过成本管理 Power BI 应用查看预留交易。

1. 获取[成本管理 Power BI 应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)。
1. 导航到 RI 购买报表。

[![显示预留交易的示例。](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

若要了解详细信息，请参阅[适用于企业协议的 Azure 成本管理 Power BI 应用](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md)。

## <a name="use-apis-to-get-reservation-transactions"></a>使用 API 获取预留交易

企业协议 (EA) 和 Microsoft 客户协议用户可以使用[预留交易 - 列表 API](/rest/api/consumption/reservationtransactions/list) 获取预留交易数据。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](manage-reserved-vm-instance.md)
