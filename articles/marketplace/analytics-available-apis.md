---
title: 用于访问商业市场分析数据的 API
description: 使用这些 API 以编程方式访问合作伙伴中心内的分析数据。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 07dae73901fca44200d6b4fb8c8149b69728d065
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748306"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>用于访问商业市场分析数据的 API

下面是用于访问商业市场分析数据的 API 及其相关功能的列表。

- [数据集拉取 API](#dataset-pull-apis)
- [查询管理 API](#query-management-apis)
- [报表管理 API](#report-management-apis)
- [报表执行拉取 API](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>数据集拉取 API

***表 1：数据集拉取 API***

| **API** | **功能** |
| --- | --- |
| [获取所有数据集](analytics-api-get-all-datasets.md) | 获取所有可用的数据集。 数据集列出了表、列、指标和时间范围。 |
|||

## <a name="query-management-apis"></a>查询管理 API

***表 2：查询管理 API***

| **API** | **功能** |
| --- | --- |
| [创建报表查询](analytics-programmatic-access.md#create-report-query-api) | 创建自定义查询，用于定义需要从中导出列和指标的数据集。 |
| [获取报表查询](analytics-api-get-report-queries.md) | 获取可在报表中使用的所有查询。 默认获取所有系统定义的查询和用户定义的查询。 |
| [删除报表查询](analytics-api-delete-report-queries.md) | 删除用户定义的查询。 |
|||

## <a name="report-management-apis"></a>报表管理 API

***表 3：报表管理 API***

| **API** | **功能** |
| --- | --- |
| [创建报表](analytics-programmatic-access.md#create-report-api) | 计划定期执行的查询。 |
| [尝试报表查询](analytics-api-try-report-queries.md) | 执行报表查询语句。 仅返回 10 条记录，合作伙伴可以使用这些记录来验证数据是否符合预期。 |
| [获取报表](analytics-api-get-report.md) | 获取已计划的所有报表。 |
| [更新报表](analytics-api-update-report.md) | 修改报表参数。 |
| [删除报表](analytics-api-delete-report.md) | 删除所有报表和报表执行记录。 |
| [暂停报表执行](analytics-api-pause-report-executions.md) | 暂停报表的按计划执行。 |
| [恢复报表执行](analytics-api-resume-report-executions.md) | 恢复已暂停报表的按计划执行。 |
|||

## <a name="report-execution-pull-apis"></a>报表执行拉取 API

***表 4：报表执行拉取 API***

| **API** | **功能** |
| --- | --- |
| [获取报表执行](analytics-programmatic-access.md#get-report-executions-api) | 获取给定报表发生的所有执行。 |
|||

## <a name="next-steps"></a>后续步骤

- 可以通过 [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) 试用 API。
