---
title: 获取报表查询 API
description: 使用此 API 可获取可用于商业市场分析报表的所有查询。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583467"
---
# <a name="get-report-queries-api"></a>获取报表查询 API

获取报表查询 API 可获取可在报表中使用的所有查询。 默认情况下，它会获取所有系统定义的和用户定义的查询。

**请求语法**

| **方法** | **请求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

请求标头

| **Header** | 类型 | **说明** |
| --- | --- | --- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |
||||

路径参数

无

查询参数

| **参数名称** | **类型** | **必需** | **说明** |
| --- | --- | --- | --- |
| `queryId` | string | 否 | 进行筛选以仅获取此参数中给定 ID 的查询的详细信息 |
| `queryName` | string | 否 | 进行筛选以仅获取此参数中给定名称的查询的详细信息 |
| `IncludeSystemQueries` | boolean | 否 | 在响应中包括预定义的系统查询 |
| `IncludeOnlySystemQueries` | boolean | 否 | 在响应中仅包括系统查询 |
|||||

请求有效负载

无

**术语表**

无

**响应**

响应有效负载的结构如下所示：

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

此表介绍了响应中元素的关键定义。

| **参数** | **说明** |
| --- | --- |
| `QueryId` | 查询的唯一 UUID |
| `Name` | 创建查询时为查询指定的名称 |
| `Description` | 在查询创建过程中给出的说明 |
| `Query` | 报表查询字符串 |
| `Type` | 对于用户创建的查询，设置为 `userDefined`；对于预定义的系统查询，设置为 `system` |
| `User` | 创建查询的用户 ID |
| `CreatedTime` | 创建查询的时间 |
| `TotalCount` | 值数组中的数据集数 |
| `StatusCode` | 结果代码。 可能的值包括 200、400、401、403、500 |
|||
