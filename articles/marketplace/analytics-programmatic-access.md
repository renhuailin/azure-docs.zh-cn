---
title: 编程式访问范例
description: 了解编程分析的 API 调用模式的概要流程。 本文还介绍了用于访问 Microsoft 商业市场中的分析报表的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 6ed2be363a9e50184d79c4f9870942030fd485eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748277"
---
# <a name="programmatic-access-paradigm"></a>编程式访问范例

下图演示了用于创建新的报表模板、计划自定义报表和检索失败数据的 API 调用模式。

[![演示用于创建新的报表模板、计划自定义报表和检索失败数据的 API 调用模式。](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox)
***图 1：API 调用模式的概要流程***

此列表提供了有关图 1 的更多详细信息。

1. 客户端应用程序可以通过调用[“创建报表查询”API](#create-report-query-api) 来定义自定义的报表架构/模板。 或者，你可以使用[系统查询列表](analytics-system-queries.md)中的报表模板 (`QueryId`)。
1. 成功后，“创建报表模板”API 将返回 `QueryId`。
1. 然后，客户端应用程序使用 `QueryID` 并结合报表开始日期、重复间隔、重复周期和可选的回调 URI 来调用[“创建报表”API](#create-report-api)。
1. 成功后，[“创建报表”API](#create-report-api) 将返回 `ReportID`。
1. 只要报表数据可供下载，就会立即通过回调 URI 向客户端应用程序发出通知。
1. 然后，客户端应用程序使用[“获取报表执行”API](#get-report-executions-api) 并结合 `Report ID` 和日期范围来查询报表的状态。
1. 成功时，会返回报告下载链接，并且应用程序可以启动数据下载。

## <a name="report-query-language-specification"></a>报表查询语言规范

尽管我们提供了可用于创建报表的[系统查询](analytics-system-queries.md)，但你也可以根据业务需求创建自己的查询。 若要详细了解自定义查询，请参阅[自定义查询规范](analytics-custom-query-specification.md)。

## <a name="create-report-query-api"></a>“创建报表查询”API

此 API 可帮助创建自定义查询，用于定义需要从中导出列和指标的数据集。 该 API 可让你灵活地根据业务需求创建新的报告模板。

你也可以使用我们提供的[系统查询](analytics-system-queries.md)。 不需要自定义报表模板时，可以直接使用我们提供的系统查询的 [QueryId](analytics-system-queries.md) 来调用[“创建报表”API](#create-report-api)。

以下示例演示如何创建自定义查询，以从上个月的 [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) 数据集中获取 _付费 SKU 的规范化使用量和预估财务费用_。

*请求语法*

| 方法 | 请求 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

请求标头

| 标头 | 类型 | 说明 |
| ------------- | ------------- | ------------- |
| 授权 | 字符串 | 必需。 Azure Active Directory (Azure AD) 访问令牌。 格式为 `Bearer <token>`。 |
| Content-Type | `string` | `application/JSON` |
||||

*路径参数*

无

*查询参数*

无

*请求有效负载示例*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*术语表*

此表提供了请求有效负载中元素的关键定义。

| 参数 | 必需 | 说明 | 允许的值 |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | 是 | 查询的易记名称 | string |
| `Description` | 否 | 查询返回的内容的说明 | 字符串 |
| `Query` | 是 | 报表查询字符串 | 数据类型：字符串<br>基于业务需求的[自定义查询](analytics-sample-queries.md) |
|||||

> [!NOTE]
> 有关自定义查询示例，请参阅[示例查询](analytics-sample-queries.md)。

*示例响应*

响应有效负载的结构如下：

响应代码：200、400、401、403、500

响应有效负载示例：

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*术语表*

此表提供了响应中元素的关键定义。

| 参数 | 说明 |
| ------------ | ------------- |
| `QueryId` | 创建的查询的全局唯一标识符 (UUID) |
| `Name` | 为请求有效负载中的查询提供的易记名称 |
| `Description` | 创建查询期间提供的说明 |
| `Query` | 创建查询期间作为输入传递的报表查询 |
| `Type` | 设置为 `userDefined` |
| `User` | 用于创建查询的用户 ID |
| `CreatedTime` | 创建查询时的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | 值数组中的数据集数目 |
| `StatusCode` | 结果代码<br>可能的值为 200、400、401、403、500 |
| `message` | 执行 API 后显示的状态消息 |
|||

## <a name="create-report-api"></a>“创建报表”API

如果成功创建自定义报表模板后收到了作为[创建报表查询](#create-report-query-api)响应的一部分的 `QueryID`，可以调用此 API 来计划一个定期执行的查询。 可以针对要传送的报表设置频率和计划。 对于我们提供的系统查询，还可以使用 [QueryId](analytics-sample-queries.md) 调用“创建报表”API。

*请求语法*

| 方法 | 请求 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

请求标头

| 标头 | 类型 | 说明 |
| ------ | ---- | ----------- |
| 授权 | 字符串 | 必需。 Azure Active Directory (Azure AD) 访问令牌。 格式为 `Bearer <token>`。 |
| 内容类型 | string | `application/JSON` |
||||

*路径参数*

无

*查询参数*

无

*请求有效负载示例*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*术语表*

此表提供了请求有效负载中元素的关键定义。

| 参数 | 必需 | 说明 | 允许的值 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | 是 | 分配给报表的名称 | string |
| `Description` | 否 | 创建的报表的说明 | 字符串 |
| `QueryId` | 是 | 报表查询 ID | 字符串 |
| `StartTime` | 是 | 开始生成报表时的 UTC 时间戳。<br>格式应为：yyyy-MM-ddTHH:mm:ssZ | 字符串 |
| `RecurrenceInterval` | 是 | 报表生成频率，以小时为单位。<br>最小值为 4，最大值为 90。 | integer |
| `RecurrenceCount` | 否 | 要生成的报表数。 | integer |
| `Format` | 否 | 导出的文件的文件格式。<br>默认格式为 .CSV。 | CSV/TSV |
| `CallbackUrl` | 否 | 可以选择性地配置为回调目标的可公开访问的 URL。 | 字符串 (http URL) |
| `ExecuteNow` | 否 | 应使用此参数创建仅执行一次的报表。 如果此参数设置为 `true`，将忽略 `StartTime`、`RecurrenceInterval` 和 `RecurrenceCount`。 报表立即以异步方式执行。 | true/false |
| `QueryStartTime` | 否 | （可选）指定用于提取数据的查询的开始时间。 此参数仅适用于 `ExecuteNow` 设置为 `true` 的一次性执行报表。 格式应为 yyyy-MM-ddTHH:mm:ssZ | 字符串形式的时间戳 |
| `QueryEndTime` | 否 | （可选）指定用于提取数据的查询的结束时间。 此参数仅适用于 `ExecuteNow` 设置为 `true` 的一次性执行报表。 格式应为 yyyy-MM-ddTHH:mm:ssZ | 字符串形式的时间戳 |
|||||

*示例响应*

响应有效负载的结构如下：

响应代码：200、400、401、403、404、500

响应有效负载：

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*术语表*

此表提供了响应中元素的关键定义。

| 参数 | 说明 |
| ------------ | ------------- |
| `ReportId` | 创建的报表的全局唯一标识符 (UUID) |
| `ReportName` | 在请求有效负载中为报表提供的名称 |
| `Description` | 创建报表期间提供的说明 |
| `QueryId` | 在创建报表时传递的查询 ID |
| `Query` | 将对此报表执行的查询文本 |
| `User` | 用于创建报表的用户 ID |
| `CreatedTime` | 创建报表时的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | 上次修改报表时的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | 报表执行开始时的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | 报表执行的状态。 可能的值为 **Paused**、**Active** 和 **Inactive**。 |
| `RecurrenceInterval` | 创建报表期间提供的重复间隔 |
| `RecurrenceCount` | 在报表创建期间提供的定期执行计数 |
| `CallbackUrl` | 在请求中提供的回调 URL |
| `Format` | 报表文件的格式。 可能的值为 CSV 或 TSV。 |
| `TotalCount` | 值数组中的数据集数目 |
| `StatusCode` | 结果代码<br>可能的值为 200、400、401、403、500 |
| `message` | 执行 API 后显示的状态消息 |
|||

## <a name="get-report-executions-api"></a>“获取报表执行”API

可以使用此方法并结合从[“创建报表”API](#create-report-api) 收到的 `ReportId` 来查询报表执行的状态。 如果报表可供下载，该方法将返回报表下载链接。 否则，该方法返回状态。 还可以使用此 API 来获取给定报表发生的所有执行。

> [!IMPORTANT]
> 此 API 设置的默认查询参数适用于 `executionStatus=Completed` 且 `getLatestExecution=true` 的情况。 因此，在首次成功执行报表之前调用该 API 会返回404。 可以通过设置 `executionStatus=Pending` 来获取挂起的执行。

*请求语法*

| 方法 | 请求 URI |
| ------------ | ------------- |
| Get | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

请求标头

| 标头 | 类型 | 说明 |
| ------ | ------ | ------ |
| 授权 | 字符串 | 必需。 Azure Active Directory (Azure AD) 访问令牌。 格式为 `Bearer <token>`。 |
| 内容类型 | 字符串 | `application/json` |
||||

*路径参数*

无

*查询参数*

| 参数名称 | 必需 | 类型 | 说明 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 是 | 字符串 | 通过筛选来仅获取具有此参数中指定的 `reportId` 的报表的执行详细信息。 可以指定多个 `reportIds`，用分号“;”分隔即可。 |
| `executionId` | 否 | 字符串 | 通过筛选来仅获取具有此参数中指定的 `executionId` 的报表的详细信息。 可以指定多个 `executionIds`，用分号“;”分隔即可。 |
| `executionStatus` | 否 | 字符串/枚举 | 通过筛选来仅获取具有此参数中指定的 `executionStatus` 的报表的详细信息。<br>有效值为 `Pending`、`Running`、`Paused` 和 `Completed` <br>默认值为 `Completed`。 可以指定多个状态，用分号“;”分隔即可。 |
| `getLatestExecution` | 否 | boolean | 该 API 返回最新报表执行的详细信息。<br>默认情况下，此参数设置为 `true`。 如果选择将此参数的值作为 `false` 传递，该 API 将返回过去 90 天的执行实例。 |
|||||

*请求有效负载*

无

*示例响应*

响应有效负载的结构如下：

响应代码：200、400、401、403、404、500

响应有效负载示例：

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

报表执行完成后，将显示执行状态 `Completed`。 可以通过在 `reportAccessSecureLink` 中选择 URL 来下载报表。

*术语表*

响应中元素的关键定义。

| 参数 | 说明 |
| ------------ | ------------- |
| `ExecutionId` | 执行实例的全局唯一标识符 (UUID) |
| `ReportId` | 与执行实例关联的报表 ID |
| `RecurrenceInterval` | 创建报表期间提供的重复间隔 |
| `RecurrenceCount` | 创建报表期间提供的重复次数 |
| `CallbackUrl` | 与执行实例关联的回调 URL |
| `Format` | 执行结束时生成的文件的格式 |
| `ExecutionStatus` | 报表执行实例的状态。<br>有效值为 `Pending`、`Running`、`Paused` 和 `Completed` |
| `ReportAccessSecureLink` | 可用于安全地访问报表的链接 |
| `ReportExpiryTime` | 报表链接过期的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `ReportGeneratedTime` | 生成报表时的 UTC 时间，格式如下：yyyy-MM-ddTHH:mm:ssZ |
| `TotalCount` | 值数组中的数据集数目 |
| `StatusCode` | 结果代码 <br>可能的值为 200、400、401、403、404 和 500 |
| `message` | 执行 API 后显示的状态消息 |
|||

## <a name="next-steps"></a>后续步骤
- 可以通过 [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) 试用 API
- [开始以编程方式访问分析数据](analytics-get-started.md)
