---
title: 更新报表 API
description: 使用此 API 可为商业市场分析报表报告参数。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 35c01bf83d4c5ecf98a49a17fce62bfd757be122
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748307"
---
# <a name="update-report-api"></a>更新报表 API

此 API 可帮助你修改报表参数。

**请求语法**

| 方法 | 请求 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

请求标头

| 标头 | 类型 | 说明 |
| ------------ | ------------- | ------------- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |
||||

**路径参数**

无

**查询参数**

| 参数名称 | 必需 | 类型 | 说明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 是 | 字符串 | 要修改的报表的 ID |
|||||

**请求有效负载**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**术语表**

此表列出了请求有效负载中元素的关键定义。

| 参数 | 必需 | 说明 | 允许的值 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | 是 | 要分配给报表的名称 | string |
| `Description` | 否 | 创建的报表的说明 | 字符串 |
| `StartTime` | 是 | 开始生成报表的时间戳 | string |
| `RecurrenceInterval` | 否 | 应以小时为单位生成报表的频率。 最小值为 4 | integer |
| `RecurrenceCount` | 否 | 要生成的报表数。 默认值为无限 | integer |
| `Format` | 是 | 导出的文件的文件格式。 默认值为 CSV。 | CSV/TSV |
| `CallbackUrl` | 是 | 要在生成报表时调用的 https 回调 URL | 字符串 |
|||||

**术语表**

无

**响应**

响应有效负载的结构如下：

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
      "RecurrenceCount": 0,
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

| 参数 | 说明 |
| ------------ | ------------- |
| `ReportId` | 正在更新的报表的全局唯一标识符 (UUID) |
| `ReportName` | 在请求有效负载中为报表指定的名称 |
| `Description` | 在报表创建过程中给出的说明 |
| `QueryId` | 在创建报表时传递的查询 ID |
| `Query` | 将为此报表执行的查询文本 |
| `User` | 用于创建报表的用户 ID |
| `CreatedTime` | 创建报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | 上次修改报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | 开始执行报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | 报表执行的状态。 可能的值为“已暂停”、“活动”和“非活动”。 |
| `RecurrenceInterval` | 在报表创建期间提供的定期执行间隔 |
| `RecurrenceCount` | 在报表创建期间提供的定期执行计数 |
| `CallbackUrl` | 请求中提供的回叫 URL |
| `Format` | 报表文件的格式 |
|||
