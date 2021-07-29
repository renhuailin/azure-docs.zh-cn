---
title: 使你的业务与我们的电子商务平台和 Microsoft Azure 市场保持一致
description: 将你的业务与我们的电子商务平台（Microsoft Azure 市场）相结合。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: 118f78c6e7b06ddf77533266581de382afcfe47a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984241"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>将你的业务与我们的电子商务平台相结合

本文介绍了商业市场用户界面 (UI) 如何结合应用程序编程接口 (API) 支持业务流程。 API 下的链接指向特定接口开发人员，可用于将其 CRM 系统与商业市场集成。

## <a name="overview-of-activities"></a>活动概述

以下活动并非连续。 你使用的活动取决于你的业务需求和销售流程。 本指南演示如何集成不同的 API 来自动执行每项活动。

| <center>活动 | ISV 销售活动 | 对应的市场 API | 对应的市场 UI |
| --- | --- | --- | --- |
| <center>**1. 产品市场营销**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | 创建产品信息宣传、定位、促销、定价 | 创建产品信息宣传、定位、促销、定价<br>[合作伙伴引入 API](https://apidocs.microsoft.com/services/partneringestion/)<br>[Azure 应用载入 API](azure-app-apis.md)</ul> | 创建产品信息宣传、定位、促销、定价<br>合作伙伴中心 (PC) → 产品/服务创建 |
| <center>**2. 需求挖掘**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | 产品促销<br>潜在顾客培育<br>Eval、试用和 PoC | 产品促销<br>潜在顾客培育<br>Eval、试用和 PoC<br>[用于 D365、SFDC 和 Marketo 的潜在顾客 CRM 连接器](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[用于 Salesforce CRM 的联合销售连接器](/partner-center/connector-salesforce)<br>[用于 Dynamics 365 CRM 的联合销售连接器](/partner-center/connector-dynamics) | 产品促销<br>潜在顾客培育<br>Eval、试用和 PoC<br>Microsoft Azure 市场和 AppSource<br>电脑市场见解<br>电脑联合销售机会 |
| <center>**3. 协商和报价创建**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | T&C<br>定价<br>折扣审核<br>最终报价 | T&C<br>定价<br>折扣审核<br>最终报价<br>[适用于 VM 的 CPP API](cloud-partner-portal-api-overview.md)<br>[适用于 AAD API 的 Microsoft Graph](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[合作伙伴中心“7”API 系列](https://apidocs.microsoft.com/services/partnercenter) | T&C<br>定价<br>折扣审核<br>最终报价<br>电脑 → 计划（公共或专用） |
| <center>**4. 销售**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | 合同签名<br>收入确认<br>发票<br>计费 | 合同签名<br>收入确认<br>发票<br>计费<br>[SaaS 履行 API v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>[报告 API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | 合同签名<br>收入确认<br>发票<br>计费<br>Azure 门户/管理中心<br>电脑市场回报<br>电脑付款报表<br>电脑市场分析<br>电脑联合销售结单 |
| <center>**5. 维护**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | 定期结算<br>超额部分<br>产品支持 | 定期结算<br>超额部分<br>产品支持<br>[SaaS/AMA：计费 API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[SaaS 履行 API v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>[合作伙伴付款 API](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[按流量计费 API](marketplace-metering-service-apis.md)<br>[（EA 客户）Azure 使用情况 API](/rest/api/consumption/)<br>[（EA 客户）Azure 费用列表 API](/rest/api/consumption/charges/list) | 定期结算<br>超额部分<br>产品支持<br>电脑付款报表<br>电脑市场分析 |
| <center>**6. 合同结束**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | 续订或<br>Terminate |续订或<br>Terminate <br>[SaaS 履行 API v.2](partner-center-portal/pc-saas-fulfillment-api-v2.md)<br>AMA/VM：自动续订 | 续订或<br>Terminate<br>电脑市场分析 |
|

## <a name="next-steps"></a>后续步骤

- 根据需要访问上述每个 API 的链接。