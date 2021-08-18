---
title: 以编程方式访问分析数据的常见问题
description: 有关在合作伙伴中心以编程方式访问商业市场产品/服务的分析数据的常见问题。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: cd0e3253eaa79c2d5a4d5bdd4d6f7d5c7098a228
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748250"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>以编程方式访问分析数据的常见问题

本文解答有关如何在合作伙伴中心以编程方式访问商业市场产品/服务的分析数据的常见问题。

## <a name="api-responses"></a>API 响应

在哪些不同场景下，我会收到除 200（成功）以外的 API 响应？

下表说明了 API 响应以及你在收到这些响应时应采取的措施。

| 错误说明 | 错误代码 | 疑难解答 |
| ------------ | ------------- | ------------- |
| 未授权 | 401 | 这是一个身份验证异常。 检查 Azure Active Directory (Azure AD) 令牌的正确性。 Azure AD 令牌的有效期为 60 分钟，之后你将需要重新生成 Azure AD 令牌。 |
| 表名无效 | 400 | 数据集名称错误。 通过调用“获取所有数据集”API 重新检查数据集名称。 |
| 列名不正确 | 400| 查询中的列名不正确。 通过调用“获取所有数据集”API 重新检查列名，或参考下表中的列名：<br><ul><li>[订单详细信息表](orders-dashboard.md#orders-details-table)</li><li>[使用情况详细信息表](usage-dashboard.md#usage-details-table)</li><li>[客户详细信息表](customer-dashboard.md#customer-details-table)</li><li>[市场见解详细信息表](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| 值为 Null 或缺失 | 400 | 你可能缺少作为 API 请求有效负载一部分的必需参数。 |
| 报表参数无效 | 400 | 确保报表参数正确无误。 例如，你为 `RecurrenceInterval` 参数提供的值可能小于 4。 |
| 重复间隔必须介于 4 和 90 之间 | 400 | 确保 `RecurrenceInterval` 请求参数的值介于 4 和 90 之间。 |
| 无效的 QueryId | 400 | 重新检查生成的 `QueryId`。 |
| 用于创建的报表参数无效 - 报表的开始时间应至少是当前 UTC 时间的 4 小时后 - | 400 | 作为请求有效负载一部分的 Start Time 参数不应是过去的时间。 报表的开始时间应至少是当前 UTC 时间的 4 小时后。 |
| 找不到请求的值“string” | 400 | 检查是否已更新请求参数 `callbackurl` 或 `format`。 |
| 使用给定筛选器找不到任何项。 | 404 | 检查获取报表执行 API 中使用的 `reportID` 参数。 |
| 对于给定筛选条件，没有发生任何执行。 请重新检查 reportId 或 executionId，然后在报表的计划执行时间后重试 API | 404 | 确保 `reportId` 正确无误。 在请求有效负载中指定的报表的计划执行时间后重试 API。 |
| 创建报表时遇到内部错误。 相关 ID <> | 500 | 确保字段“StartTime”、“QueryStartTime”和“QueryEndTime”的日期格式正确无误。 |
| 服务不可用 | 500 | 如果连续接收到“服务不可用”（5xx 错误），请创建[支持票证](support.md)。 |
||||

## <a name="no-records"></a>无记录

当我从安全位置下载报表时，我收到 API 响应 200。 为什么我没有收到记录？

检查查询中的字符串是否具有列标题允许的值之一。 例如，下面的查询将返回零个结果：

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

在此示例中，SKUBillingType 的允许值为“Paid”或“Free”。 有关各列的所有可能值，请参阅下表：

- [订单详细信息表](orders-dashboard.md#orders-details-table)
- [使用情况详细信息表](usage-dashboard.md#usage-details-table)
- [客户详细信息表](customer-dashboard.md#customer-details-table)
- [市场见解详细信息表](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>后续步骤

- [用于访问商业市场分析数据的 API](analytics-available-apis.md)
