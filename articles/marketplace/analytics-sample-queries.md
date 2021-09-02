---
title: 编程式分析的示例查询
description: 使用这些示例查询以编程方式访问 Microsoft 商业市场分析数据。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 8/06/2021
ms.openlocfilehash: ac276f495ac2a5eb3bee6ac0f682185cf8424611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748232"
---
# <a name="sample-queries-for-programmatic-analytics"></a>编程式分析的示例查询

本文提供针对 Microsoft 商业市场“订单”、“使用量”和“客户”报表的示例查询。 可以通过调用[创建报表查询](analytics-programmatic-access.md#create-report-query-api) API 终结点来使用这些查询。 如果需要，可以修改[创建报表查询](analytics-programmatic-access.md#create-report-query-api) API 调用，以添加更多的列，调整计算周期并添加筛选条件。 支持的时间段为 6 个月 (6M)、12 个月 (12M) 和自定义时间段。

有关列名、特性和说明的详细信息，请参阅下表：

- [客户详细信息表](customer-dashboard.md#customer-details-table)
- [订单详细信息表](orders-dashboard.md#orders-details-table)
- [使用情况详细信息表](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>“客户”报表查询

这些示例查询适用于“客户”报表。

| **查询说明** | **示例查询** |
| --- | --- |
| 在所选日期之前合作伙伴的活跃客户 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| 在所选日期之前合作伙伴的已流失客户 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| 过去 6 个月来自特定地理位置的新客户列表 | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>“使用量”报表查询

这些示例查询适用于“使用量”报表。

| **查询说明** | **示例查询** |
| --- | --- |
| 过去 6 个月“通过 Azure 计费”市场许可证类型的虚拟机 (VM) 的规范化使用量 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 过去 12 个月“通过 Azure 计费”市场许可证类型的 VM 的原始使用量 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| 过去 6 个月“自带许可证”市场许可证类型的 VM 的规范化使用量 | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 过去 6 个月“自带许可证”市场许可证类型的 VM 的原始使用量 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| 根据使用日期，查询付费计划在过去一个月的每日规范化总使用量和“估算的扩增费用 (PC/CC)” | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| 根据使用日期，查询付费计划在过去一个月的每日原始总使用量和“估算的扩增费用 (PC/CC)” | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| 根据特定的产品/服务名称，查询过去 6 个月“通过 Azure 计费”市场许可证类型的 VM 的规范化使用量 | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 根据特定的产品/服务名称，查询过去 6 个月的计量使用量 | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>“订单”报表查询

这些示例查询适用于“订单”报表。

| **查询说明** | **示例查询** |
| --- | --- |
| 过去 6 个月 Azure 许可证类型为“企业”的“订单”报表 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| 过去 6 个月 Azure 许可证类型为“即用即付”的“订单”报表 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| 过去 6 个月特定产品/服务名称的“订单”报表 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| 过去 6 个月有效订单的“订单”报表 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 过去 6 个月已取消的订单的“订单”报表 | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
| 带有条款开始日期、条款结束日期、估计费用和货币的“订单”报表 | `SELECT OrderId, TermStartId, TermEndId, estimatedcharges from ISVOrderV2 WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| 过去 6 个月活动的试用订单的“订单”报表 | `SELECT OrderId from ISVOrderV2 WHERE OrderStatus = ‘Active’ and HasTrial = ‘True’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>后续步骤

- [用于访问商业市场分析数据的 API](analytics-available-apis.md)
