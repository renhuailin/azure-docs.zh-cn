---
title: 删除报表 API
description: 使用此 API 可删除商业市场分析报表的所有报表和报表执行记录。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9cb8f12061e53a89fa0380d717c0ad947515ab5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747243"
---
# <a name="delete-report-api"></a>删除报表 API

执行时，此 API 会删除所有报表和报表执行记录。

**请求语法**

| 方法 | 请求 URI |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

请求标头

| 标头 | 类型 | 说明 |
| ------------ | ------------- | ------------- |
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 `Bearer <token>` |
| 内容类型 | string | `Application/JSON` |
||||

路径参数

无

查询参数

| 参数名称 | 必须 | string | 说明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 是 | 字符串 | 正在修改的报表的 ID |
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
| `ReportId` | 已删除报表的唯一 UUID |
| `ReportName` | 在创建过程中为报表指定的名称 |
| `Description` | 在报表创建过程中给出的说明 |
| `QueryId` | 在创建报表时传递的查询 ID |
| `Query` | 将为此报表执行的查询文本 |
| `User` | 用于创建报表的用户 ID |
| `CreatedTime` | 创建报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | 上次修改报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | 开始执行报表的时间。 时间格式为 yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | 报表执行的状态。 可能的值为 Paused、Active 和 Inactive。 |
| `RecurrenceInterval` | 在报表创建期间提供的定期执行间隔 |
| `RecurrenceCount` | 在报表创建期间提供的定期执行计数 |
| `CallbackUrl` | 请求中提供的回叫 URL |
| `Format` | 报表文件的格式 |
|||
