---
title: 删除报表查询 API
description: 使用此 API 可删除用于商业市场分析的用户定义查询。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 927534b11bc00b3ec21db2ab5efef2eb09104976
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748364"
---
# <a name="delete-report-queries-api"></a>删除报表查询 API

此 API 删除用户定义的查询。

**请求语法**

| **方法** | **请求 URI** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

请求标头

| **Header** | 类型 | **说明** |
| --- | --- | --- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |

路径参数

| **参数名称** | 类型 | **说明** |
| --- | --- | --- |
| `queryId` | string | 进行筛选以仅获取此参数中给定 ID 的查询的详细信息 |

查询参数

无

请求有效负载

无

**术语表**

无

**响应**

响应有效负载的结构如下所示，采用 JSON 格式。

响应代码：200、400、401、403、404、500

响应有效负载：

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**术语表**

此表列出了响应中元素的关键定义。

| **参数** | **说明** |
| --- | --- |
| `QueryId` | 已删除查询的唯一 UUID。 |
| `Name` | 已删除查询的名称 |
| `Description` | 已删除查询的说明 |
| `Query` | 已删除查询的报表查询字符串 |
| `Type` | userDefined |
| `User` | 创建查询的用户 ID |
| `CreatedTime` | 创建查询的时间 |
| `ModifiedTime` | Null |
| `TotalCount` | 值数组中的数据集数 |
| `StatusCode` | 结果代码。 可能的值包括 200、400、401、403、500 |
