---
title: 发出第一次 API 调用以访问商业市场分析数据
description: 有关如何使用 API 访问商业市场分析数据的示例。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f4437782f46e3144b3917a16fec14c9e92dfd303
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748286"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>发出第一次 API 调用以访问商业市场分析数据

有关用于访问商业市场分析数据的 API 列表，请参阅[用于访问商业市场分析数据的 API](analytics-available-apis.md)。 在发出第一次 API 调用之前，请确保满足以编程方式访问商业市场分析数据的[先决条件](analytics-prerequisites.md)。

## <a name="token-generation"></a>令牌生成

在调用任何方法之前，必须先获取一个 Azure Active Directory (Azure AD) 访问令牌。 需将该 Azure AD 访问令牌传递给 API 中每个方法的授权标头。 获取访问令牌后，请在 60 分钟内使用它，否则它会过期。 该令牌过期后，可以刷新该令牌，并继续使用它来进一步调用 API。

请参阅下面用于生成令牌的示例请求。 生成令牌所需的三个值为 `clientId`、`clientSecret` 和 `tenantId`。 `resource` 参数应设置为 `https://graph.windows.net`。

***请求示例***：

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***响应示例***：

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

有关如何获取应用程序的 Azure AD 令牌的详细信息，请参阅[使用存储服务访问分析数据](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token)。

## <a name="programmatic-api-call"></a>编程式 API 调用

根据上一部分所述获取 Azure AD 令牌后，请遵循以下步骤创建第一份编程式访问报表。

可从以下数据集 (datasetName) 下载数据：

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

后续部分提供了有关如何以编程方式访问 ISVOrder 数据集中的 `OrderId` 的示例。

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>步骤 1：使用“获取数据集”API 发出 REST 调用

API 响应将提供数据集名称，从中可以下载报表。 对于特定的数据集，API 响应还会提供可用于自定义报表模板的可供选择的列列表。 还可以参考下表来获取列的名称：

- [订单详细信息表](orders-dashboard.md#orders-details-table)
- [使用情况详细信息表](usage-dashboard.md#usage-details-table)
- [客户详细信息表](customer-dashboard.md#customer-details-table)
- [市场见解详细信息表](insights-dashboard.md#marketplace-insights-details-table)

***请求示例***：

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***响应示例***：

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>步骤 2：创建自定义查询

在此步骤，我们将使用“订单”报表中的“订单 ID”针对所需的报表创建自定义查询。 如果未在查询中指定，则默认的 `timespan` 为 6 个月。

***请求示例***：

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***响应示例***：

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

成功执行查询后，将生成一个需要用来生成报表的 `queryId`。

### <a name="step-3-execute-test-query-api"></a>步骤 3：执行测试查询 API

在此步骤，我们将使用测试查询 API 获取已创建的查询的前 100 行。

***请求示例***：

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***响应示例***：

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>步骤 4：创建报表

在此步骤，我们将使用前面生成的 `QueryId` 来创建报表。

***请求示例***：

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**表 1：此请求示例中使用的参数的说明**_

| 参数 | 说明 |
| ------------ | ------------- |
| `Description` | 提供所要生成的报表的简要说明。 |
| `QueryId` | 这是在“步骤 2：创建自定义查询”中创建查询时生成的 `queryId`。 |
| `StartTime` | 首次开始执行报表的时间。 |
| `RecurrenceInterval` | 创建报表期间提供的重复间隔。 |
| `RecurrenceCount` | 创建报表期间提供的重复次数。 |
| `Format` | 支持 CSV 和 TSV 文件格式。 |
|||

***响应示例***：

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

成功执行后，将生成一个需要用来计划报表下载的 `reportId`。

### <a name="step-5-execute-report-executions-api"></a>步骤 5：执行“报表执行”API

为了获取报表的安全位置 (URL)，我们现在将执行“报表执行”API。

***请求示例***：

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***响应示例***：

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>后续步骤

- 可以通过 [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) 试用 API
- [以编程方式访问的范例](analytics-programmatic-access.md)
