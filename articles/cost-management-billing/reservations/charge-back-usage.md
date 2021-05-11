---
title: 撤回 Azure 预留成本
description: 了解如何查看 Azure 预留费用以进行退款。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532768"
---
# <a name="charge-back-azure-reservation-costs"></a>撤回 Azure 预留成本

企业协议和 Microsoft 客户协议计费用户可以查看预留的摊销成本数据。 他们可以使用成本数据向其合作伙伴收回订阅、资源组、资源或标记的费用。 在摊销数据中，有效价格为按比例计算的每小时预留成本。 成本是指资源在当天使用的预留总费用。

具有单独订阅的用户可以从其使用情况文件获取摊销成本数据。 当资源获取预留折扣时，使用情况文件中的 AdditionalInfo 部分会包含预留详细信息。 有关详细信息，请参阅[从 Azure 门户下载使用情况](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv)。

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>请参阅预留使用情况数据以显示余量和进行退款

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 导航到“成本管理 + 计费” 
3. 从左侧导航栏中选择“成本分析” 
4. 在“实际成本”下，选择“摊销成本”指标 。
5. 若要查看预留使用的资源，请将筛选器应用于“预留”，然后选择“预留”。
6. 将“粒度”设置为“每月”或“每日”。  
7. 将图表类型设置为“表”。
8. 将“分组依据”选项设置为“资源” 。

[![显示可用于退款的预留资源成本的示例](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

以下视频显示了如何在 Azure 门户中查看订阅、资源组和资源级别的预留使用成本。

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>获取数据以显示余量和进行退款
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 导航到“成本管理 + 计费” 
3. 从左侧导航栏中选择“导出” 
4. 单击“添加”按钮
5. 选择“摊销成本”作为指标按钮并设置导出

获取预留折扣的用途的 EffectivePrice 是预留项的按比例分配成本（而不是零）。 这可以帮助你了解订阅、资源组或资源消耗的预留项的货币价值，并可帮助你在内部分摊预留项的使用费。 该数据集还包括预留项的未使用小时数。 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>使用 API 获取 Azure 消耗量和预留项使用情况数据

可以使用 API 获取数据，也可以从 Azure 门户下载数据。

调用[使用情况详细信息 API](/rest/api/consumption/usagedetails/list) 以获取新数据。 有关术语的详细信息，请参阅[使用情况中的术语](../understand/understand-usage.md)。

下面是对使用情况详细信息 API 的示例调用：

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

有关 {enrollmentId} 和 {billingPeriodId} 的详细信息，请参阅[使用情况详细信息 - 列出](/rest/api/consumption/usagedetails/list) API 文章。

下表中有关指标和筛选器的信息可帮助解决常见的预留项问题。

| **API 数据的类型** | API 调用操作 |
| --- | --- |
| **所有费用(使用情况和购买项目)** | 请将 {metric} 替换为 ActualCost |
| **获得预留折扣的用途** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/reservationId%20ne%20 |
| **未获得预留折扣的用途** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/reservationId%20eq%20 |
| **摊销费用(使用情况和购买项目)** | 请将 {metric} 替换为 AmortizedCost |
| **未使用的预留项报告** | 请将 {metric} 替换为 AmortizedCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'UnusedReservation' |
| **预留项购买** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'Purchase'  |
| **退款** | 请将 {metric} 替换为 ActualCost<br><br>请将 {filter} 替换为 properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>下载包含新数据的使用情况 CSV 文件

如果你是 EA 管理员，可从 Azure 门户下载包含新的使用数据的 CSV 文件。 此数据不会在 EA 门户 (ea.azure.com) 中提供，必须从 Azure 门户 (portal.azure.com) 下载使用情况文件才能看到新数据。

在 Azure 门户中，导航到[“成本管理 + 计费”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)。

1. 选择计费帐户。
2. 单击“使用情况 + 费用”。 
3. 单击“下载”  。  
![该示例显示可在 Azure 门户中的哪个位置下载 CSV 使用情况数据文件](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在“使用情况详细信息”中，选择“摊销使用情况数据” 。

下载的 CSV 文件包含实际成本和摊销成本。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure 预留使用情况数据，请参阅以下文章：
  - [企业协议和 Microsoft 客户协议预留成本和使用情况](understand-reserved-instance-usage-ea.md)
 
