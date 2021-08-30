---
title: 系统查询列表
description: 了解系统查询，你可以使用这些查询以编程方式获取有关你在 Microsoft 商业市场中的产品/服务的分析数据。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/06/2021
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.openlocfilehash: 04d56f39cc4721d0290bb258595fb95b6440cf79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748214"
---
# <a name="list-of-system-queries"></a>系统查询列表

可以通过 `QueryId` 直接在[创建报表 API](analytics-programmatic-access.md#create-report-api) 中使用以下系统查询。 系统查询类似于合作伙伴中心内六个月 (6M) 计算期的导出报表。

有关列名称、属性和说明的详细信息，请参阅以下文章：

- [商业市场分析中的“客户”仪表板](customer-dashboard.md#customer-details-table)
- [商业市场分析中的“订单”仪表板](orders-dashboard.md#orders-details-table)
- [商业市场分析中的“使用情况”仪表板](usage-dashboard.md#usage-details-table)
- [商业市场分析中的“市场见解”仪表板](insights-dashboard.md#marketplace-insights-details-table)

以下部分提供了针对各种报表的报表查询。

## <a name="customers-report-query"></a>“客户”报表查询

报表说明：最近 6 个月的“客户”报表

QueryID：`b9df4929-073f-4795-b0cb-a2c81b11e28d`

报表查询：

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>“订单”报表查询

报表说明：最近 6 个月的“订单”报表

QueryID：`fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

报表查询：

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

报表说明：最近 6 个月的订单 V2 报表

QueryID：`bd1b0cc1-ce45-4578-beba-6fe5a69fd421`

报表查询：

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId,TermStartDate,TermEndDate,PurchaseRecordId,PurchaseRecordLineItemId,HasTrial,IsTrialDeployment,estimatedcharges FROM ISVOrderV2 TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>“使用情况”报表查询

报表说明：最近 6 个月的 VM 标准化使用情况报表

QueryID：`2c6f384b-ad52-4aed-965f-32bfa09b3778`

报表查询：

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

报表说明：最近 6 个月的 VM 原始使用情况报表

QueryID：`3f19fb95-5bc4-4ee0-872e-cedd22578512`

报表查询：

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

报表说明：最近 6 个月经过计量的使用情况报表

QueryID：`f0c4927f-1f23-4c99-be4a-1371a5a9a086`

报表查询：

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>“市场见解”报表查询

报表说明：最近 6 个月的“市场见解”报表

QueryID：`6fd7624b-aa9f-42df-a61d-67d42fd00e92`

报表查询：

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>后续步骤

- [用于访问商业市场分析数据的 API](analytics-available-apis.md)
- [用于访问商业市场分析数据的示例应用程序](analytics-sample-application.md)
