---
title: 试用报表查询 API
description: 使用此 API 可对商业市场分析报表执行报表查询。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583422"
---
# <a name="try-report-queries-api"></a>试用报表查询 API

此 API 会执行报表查询语句。 此 API 仅返回 10 条记录。作为合作伙伴，你可以使用这些记录来验证数据是否符合预期。

> [!IMPORTANT]
> 此 API 的查询执行超时值为 100 秒。 如果你注意到此 API 执行的时间超过 100 秒，则很可能此查询在语法上是正确，否则你已收到非 200 错误代码。 如果查询语法正确，则实际的报表生成会获得通过。

**请求语法**

| **方法** | **请求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

请求标头

| **Header** | 类型 | **说明** |
| --- | --- | --- |
| 授权 | 字符串 | 必需。 格式为 `Bearer <token>` 的 Azure Active Directory (Azure AD) 访问令牌 |
| Content-Type | 字符串 | `Application/JSON` |
|||

**QueryParameter**

| **参数名称** | **类型** | **说明** |
| --- | --- | --- |
| `exportQuery` | string | 需要执行的报表查询字符串 |
| `queryId` | string | 有效的现有查询 ID |
|||

路径参数 

None

请求有效负载

无

**术语表**

无

**响应**

响应有效负载的结构如下所示：

响应代码：200、400、401、403、404、500

响应有效负载：执行查询的前 10 行
