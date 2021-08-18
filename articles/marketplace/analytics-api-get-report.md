---
title: 获取报表 API
description: 使用此 API 可获取已在合作伙伴中心计划的分析报表。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: d9248559f785ffb3b63e1f3d275e33a205cb682f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748362"
---
# <a name="get-report-api"></a>获取报表 API

此 API 获取已计划的所有报表。

**请求语法**

| **方法** | **请求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

请求标头

| **Header** | 类型 | **说明** |
| --- | --- | --- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |

路径参数

无

查询参数

| **参数名称** | **必需** | 类型 | **说明** |
| --- | --- | --- | --- |
| `reportId` | 否 | 字符串 | 通过筛选来仅获取具有此参数中指定的 `reportId` 的报表的详细信息 |
| `reportName` | 否 | 字符串 | 通过筛选来仅获取具有此参数中指定的 `reportName` 的报表的详细信息 |
| `queryId` | 否 | boolean | 在响应中包括预定义的系统查询 |

**术语表**

无

**响应**

响应有效负载以 JSON 格式构建，如下所示：

响应代码：200、400、401、403、404、500

响应有效负载：

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
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
| `ReportId` | 创建的报表的唯一 UUID |
| `ReportName` | 在请求有效负载中为报表提供的名称 |
| `Description` | 创建报表时给出的说明 |
| `QueryId` | 创建报表时传递的查询 ID |
| `Query` | 将为此报表执行的查询文本 |
| `User` | 用于创建报表的用户 ID |
| `CreatedTime` | 创建报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | 上次修改报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | 执行将开始的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | 报表执行的状态。 可能的值为“已暂停”、“活动”和“非活动”。 |
| `RecurrenceInterval` | 在报表创建期间提供的定期执行间隔 |
| `RecurrenceCount` | 在报表创建期间提供的定期执行计数 |
| `CallbackUrl` | 请求中提供的回叫 URL |
| `Format` | 报表文件的格式 |
