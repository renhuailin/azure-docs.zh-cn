---
title: 从 Enterprise 报告迁移到 Azure 资源管理器 API
description: 本文可帮助你了解报告 API 与 Azure 资源管理器 API 之间的差异、迁移到 Azure 资源管理器 API 时应发生的情况，以及新的 Azure 资源管理器 API 提供的新功能。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 24fbf52c8fd0338537862a54e15e8a249541a701
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635784"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>从 Enterprise 报告迁移到 Azure 资源管理器 API

本文可帮助使用[适用于企业客户的 Azure 报告 API](../manage/enterprise-api.md) 构建了自定义解决方案的开发人员迁移到用于成本管理的 Azure 资源管理器 API。 新的 Azure 资源管理器 API 的服务主体支持现已正式发布。 Azure 资源管理器 API 正在积极开发中。 请考虑停止使用适用于企业客户的旧 Azure 报告 API，以迁移到这些新的 API。 旧 API 将被弃用。 本文可帮助你了解报告 API 与 Azure 资源管理器 API 之间的差异、迁移到 Azure 资源管理器 API 时应发生的情况，以及新的 Azure 资源管理器 API 提供的新功能。

## <a name="api-differences"></a>API 差异

以下信息描述了适用于企业客户的旧报告 API 与新 Azure 资源管理器 API 之间的差异。

| **使用** | **企业协议 API** | **Azure 资源管理器 API** |
| --- | --- | --- |
| 身份验证 | 企业协议 (EA) 门户中预配的 API 密钥 | 使用用户令牌或服务主体的 Azure Active Directory (Azure AD) 身份验证。 服务主体取代了 API 密钥。 |
| 范围和权限 | 所有请求都处于注册范围内。 API 密钥权限分配将确定是否返回整个注册、某个部门或某个特定帐户的数据。 无用户身份验证。 | 为用户或服务主体分配了对注册、部门或帐户范围的访问权限。 |
| URI 终结点 | https://consumption.azure.com | https://management.azure.com |
| 开发状态 | 处于维护模式。 即将弃用。 | 正在积极开发 |
| 可用 API | 仅限当前可用 | 可使用等效的 API 替换每个 EA API。 <p> 还提供了其他[成本管理 API](/rest/api/cost-management/)，其中包括： <p> <ul><li>预算</li><li>警报<li>导出</li></ul> |

## <a name="migration-checklist"></a>迁移核对清单

- 熟悉 [Azure 资源管理器 REST API](/rest/api/azure)。
- 确定要使用的 EA AP，并在 [EA API 映射到新的 Azure Resource Manager API](#ea-api-mapping-to-new-azure-resource-manager-apis) 部分查看要移动到哪个 Azure Resource Manager API。
- 为 Azure 资源管理器 API 配置服务授权和身份验证
  - 如果你未曾用过 Azure 资源管理器 API，请[将客户端应用注册到 Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)。 注册会创建一个服务主体，用于调用 API。
  - 如下所述，将服务主体访问权限分配到所需的范围。
  - 更新任何编程代码，以对服务主体使用 [Azure AD 身份验证](/rest/api/azure/#create-the-request)。
- 测试 API，然后更新任何编程代码，将 EA API 调用替换为 Azure 资源管理器 API 调用。
- 更新错误处理，以使用新的错误代码。 部分注意事项包括：
  - Azure 资源管理器 API 的超时期限为 60 秒。
  - Azure 资源管理器 API 设置了速率限制。 如果超过了速率，将导致 429 限制错误。 构建解决方案，以便在短时间内不会进行太多的 API 调用。
- 查看通过 Azure 资源管理器提供的其他成本管理 API，并评估以备后用。 有关详细信息，请参阅[使用其他成本管理 API](#use-additional-cost-management-apis)。

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>将服务主体访问权限分配给 Azure 资源管理器 API

创建服务主体以编程方式调用 Azure 资源管理器 API 后，需要为其分配适当的权限，以对 Azure 资源管理器中的请求进行授权并执行这些请求。 针对不同情况有两种权限框架。

### <a name="azure-billing-hierarchy-access"></a>Azure 计费层次结构访问

若要将服务主体权限分配给企业计费帐户、部门或注册帐户范围，请参阅[将角色分配给 Azure 企业协议服务主体名称](../manage/assign-roles-azure-service-principals.md)。

### <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

新的服务主体支持扩展到特定于 Azure 的范围，如管理组、订阅和资源组。 你可以直接在 [Azure 门户](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)中或通过使用 [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role) 将服务主体权限分配给这些范围。

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>EA API 映射到新的 Azure 资源管理器 API

使用下表来确定当前使用的 EA API，以及要改用的替代 Azure 资源管理器 API。

| **方案** | **EA API** | **Azure 资源管理器 API** |
| --- | --- | --- |
| 余额摘要 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| 价目表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – 用于协商价格 <p> [零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) – 用于零售价格 |
| 预留实例详细信息 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| 预留实例摘要 | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| 预留实例建议 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 预留实例费用 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>按 API 的迁移详细信息

以下部分介绍了旧的 API 请求示例和新的替换 API 示例。

### <a name="balance-summary-api"></a>余额摘要 API

在调用新的余额摘要 API 时，请使用以下请求 URI。 你的注册号应用作 billingAccountId。

#### <a name="supported-requests"></a>支持的请求

[获取注册](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>响应正文更改

旧响应正文：

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

新响应正文：

现在，新 API 响应的 `properties` 字段中提供了相同的数据。 某些字段名称的拼写可能有细微的更改。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>价目表

在调用新的价目表 API 时，请使用以下请求 URI。

#### <a name="supported-requests"></a>支持的请求

 可以使用以下作用域调用 API：

- 注册：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 订阅：`subscriptions/{subscriptionId}`

[获取当前计费周期](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[获取指定计费周期](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>响应正文更改

旧响应：

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

新响应：

旧数据现在位于新 API 响应的 `pricesheets` 字段中。 还提供了计量详细信息。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>预留实例使用情况详细信息

Microsoft 未主动处理基于同步的预留详细信息 API。 建议你在迁移过程中切换为更新的支持 SPN 的异步 API 调用模式。 异步请求可更好地处理大量数据，并减少超时错误。

#### <a name="supported-requests"></a>支持的请求

在调用新的异步预留详细信息 API 时，请使用以下请求 URI。 你的注册号应用作 `billingAccountId`。 可以使用以下作用域调用 API：

- 注册：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>生成预留详细信息报告的示例请求

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>轮询报告生成状态的示例请求

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>示例轮询响应

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>响应正文更改

下面是旧的基于同步的预留详细信息 API 的响应。

旧响应：

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

新响应：

新 API 可为你创建 CSV 文件。 请参阅以下文件字段。

| 旧属性 | 新属性 | 备注 |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | 实例灵活性的新属性。 |
|  | InstanceFlexibilityRatio | 实例灵活性的新属性。 |
| instanceId | InstanceName |  |
|  | 类型 | 这是一个新属性。 值为 `None`、`Reservation` 或 `IncludedQuantity`。 |
| reservationId | 预订 ID |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>预留实例使用情况摘要

使用以下请求 URI 调用新的预留摘要 API。

#### <a name="supported-requests"></a>支持的请求

 使用以下作用域调用 API：

- 注册：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[每日获取预留摘要](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[每月获取预留摘要](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>响应正文更改

旧响应：

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

新响应：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>预留实例建议

使用以下请求 URI 调用新的预留建议 API。

#### <a name="supported-requests"></a>支持的请求

 使用以下作用域调用 API：

- 注册：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 订阅：`subscriptions/{subscriptionId}`
- 资源组：`subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[获取建议](/rest/api/consumption/reservationrecommendations/list)

共享作用域和单作用域建议均可通过此 API 获得。 还可以将作用域作为可选的 API 参数进行筛选。

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>响应正文更改

共享作用域和单作用域的建议合并到一个 API 中。

旧响应：

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

新响应：

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>预留实例费用

使用以下请求 URI 调用新的预留实例费用 API。

#### <a name="supported-requests"></a>支持的请求

[按日期范围获取预留费用](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>响应正文更改

旧响应：

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
新响应：

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>使用其他成本管理 API

在现有报告方案中迁移到 Azure 资源管理器 API 后，也可以使用许多其他 API。 还可以通过 Azure 资源管理器获取这些 API，并使用基于服务主体的身份验证自动执行这些 API。 以下是可以使用的新功能的快速摘要。

- [预算](/rest/api/consumption/budgets/createorupdate) - 用于设置阈值，以主动监视成本、提醒相关利益干系人，以及根据阈值违反情况自动执行操作。

- [警报](/rest/api/cost-management/alerts) - 用于查看警报信息，包括但不限于预算警报、账单警报、信用警报和配额警报。

- [导出](/rest/api/cost-management/exports) - 用于计划将费用数据定期导出到所选的 Azure 存储帐户。 对于需要分析数据并将其用于自己的内部系统且大量使用 Azure 的客户，建议使用这种解决方案。

## <a name="next-steps"></a>后续步骤

- 熟悉 [Azure 资源管理器 REST API](/rest/api/azure)。
- 如果需要，请确定你使用的 EA AP，并在 [EA API 映射到新的 Azure Resource Manager API](#ea-api-mapping-to-new-azure-resource-manager-apis) 部门查看要移动到哪个 Azure Resource Manager API。
- 如果你未曾用过 Azure 资源管理器 API，请[将客户端应用注册到 Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 如果需要，请更新任何编程代码，以对服务主体使用 [Azure AD 身份验证](/rest/api/azure/#create-the-request)。