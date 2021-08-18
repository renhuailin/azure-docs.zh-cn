---
title: 获取所有数据集 API
description: 使用此 API 获取用于商业市场分析的所有可用数据集。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 487c1b4ad58eb17fc90bb78f0dbc4346de8a62d3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747237"
---
# <a name="get-all-datasets-api"></a>获取所有数据集 API

获取所有数据集 API 可获取所有可用数据集。 数据集列出了表、列、指标和时间范围。

**请求语法**

| **方法** | **请求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

请求标头

| **Header** | 类型 | **说明** |
| --- | --- | --- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |

**路径参数**

无

**查询参数**

| **参数名称** | **类型** | **必需** | **说明** |
| --- | --- | --- | --- |
| `datasetName` | string | 否 | 筛选以仅获取一个数据集的详细信息 |

**请求有效负载**

无

**术语表**

无

**响应**

响应有效负载的结构如下：

响应代码：200、400、401、403、404、500

响应有效负载示例：

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**术语表**

下表定义了响应中的关键元素：

| **参数** | **说明** |
| --- | --- |
| `DatasetName` | 此数组对象定义的数据集的名称 |
| `SelectableColumns` | 可在选定的列中指定的原始列 |
| `AvailableMetrics` | 可在选定的列中指定的聚合/指标列名称 |
| `AvailableDateRanges` | 可在数据集的报表查询中使用的日期范围 |
| `NextLink` | 如果数据进行了分页，则链接到下一页 |
| `TotalCount` | 值数组中的数据集数 |
| `StatusCode` | 结果代码。 可能的值包括 200、400、401、403、500 |
