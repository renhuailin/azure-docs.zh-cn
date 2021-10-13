---
title: 基于工作区的 Azure Monitor Application Insights 资源架构
description: 了解适用于 Azure Monitor Application Insights 基于工作区的资源的新表结构和架构。
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 040f53836466dec446da6fcd312910ca7ad600e4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615498"
---
# <a name="workspace-based-resource-changes"></a>基于工作区的资源更改

在引入[基于工作区的 Application Insights 资源](create-workspace-resource.md)之前，Application Insights 数据与 Azure Monitor 中的其他日志数据是分开存储的。 这两者都基于 Azure 数据资源管理器并使用相同的 Kusto 查询语言 (KQL)。 [Azure Monitor 中的日志](../logs/data-platform-logs.md)中对此进行了介绍。

引入基于工作区的 Application Insights 资源后，相关数据与其他监视数据和应用程序数据一并存储在 Log Analytics 工作区中。 这使你能够更轻松地跨多个解决方案分析数据并利用工作区的功能，从而简化配置。

## <a name="table-structure"></a>表结构

| 旧表名称 | 新表名称 | 说明 |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  可用性测试中的摘要数据。|
| browserTimings | AppBrowserTimings | 有关客户端性能的数据，例如处理传入数据所用的时间。|
| 依赖项 | AppDependencies | 从应用程序到通过 TrackDependency() 记录的其他组件（包括外部组件）的调用 - 例如，对 REST API、数据库或文件系统的调用。  |
| customEvents | AppEvents | 应用程序创建的自定义事件。 |
| customMetrics | AppMetrics | 应用程序创建的自定义指标。 |
| pageViews | AppPageViews| 每个网站的浏览情况数据，以及浏览器信息。 |
| performanceCounters | AppPerformanceCounters | 支持应用程序的计算资源的性能度量，例如 Windows 性能计数器。 |
| 请求 | AppRequests | 应用程序收到的请求。 例如，为 Web 应用接收到的每个 HTTP 请求记录一条单独的请求记录。  |
| exceptions | AppExceptions | 应用程序运行时引发的异常捕获服务器端和客户端（浏览器）异常。 |
| traces | AppTraces | 通过 TrackTrace () 记录的应用程序代码/日志记录框架发出的详细日志（跟踪）。 |

## <a name="table-schemas"></a>表架构

以下部分展示经典属性名称和新的基于工作区的 Application Insights 属性名称之间的映射。  使用此信息可通过旧表转换任何查询。

大多数列具有相同名称，只是大小写不同。 由于 KQL 区分大小写，因此需要更改现有查询中的每个列名以及表名。 会突出显示有除了大小写之外其他更改的列。 仍可在 Application Insights 资源的“日志”窗格中使用经典 Application Insights 查询，即使该资源是基于工作区的资源。 当在 Log Analytics 工作区的上下文中查询时，需要提供新的属性名。

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Legacy table: availability

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|location|string|位置|string|
|message|string|消息|string|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|大小|real|大小|real|
|success|string|成功|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Legacy table: browserTimings

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appdependencies"></a>AppDependencies

Legacy table: dependencies

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|data|string|数据|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|success|string|成功|Bool|
|目标|string|目标|string|
|timestamp|datetime|TimeGenerated|datetime|
|type|字符串|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appevents"></a>AppEvents

Legacy table: customEvents

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appmetrics"></a>AppMetrics

Legacy table: customMetrics

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|（已删除）||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Legacy table: pageViews

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|URL|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Legacy table: performanceCounters

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|字符串|
|category|string|Category|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|（已删除）||
|customDimensions|dynamic|属性|动态|
|iKey|string|IKey|string|
|instance|string|实例|string|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|字符串|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|值|real|

### <a name="apprequests"></a>AppRequests

Legacy table: requests

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|动态|
|customMeasurements|dynamic|度量|动态|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|字符串|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|name|string|名称|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|字符串|
|resultCode|string|ResultCode|字符串|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|源|字符串|
|success|string|成功|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|URL|字符串|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="appsystemevents"></a>AppSystemEvents

Legacy table: exceptions

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|程序集 (assembly)|string|程序集|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|dynamic|
|customMeasurements|dynamic|度量|dynamic|
|详细信息|dynamic|详细信息|dynamic|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|message|string|消息|string|
|method|string|方法|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|type|字符串|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

### <a name="apptraces"></a>AppTraces

Legacy table: traces

|ApplicationInsights|类型|LogAnalytics|类型|
|:---|:---|:---|:---|
|appId|string|\_ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|stateorprovince|string|stateorprovince|string|
|client_type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|属性|dynamic|
|customMeasurements|dynamic|度量|dynamic|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|类型|字符串|
|message|string|消息|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## <a name="next-steps"></a>后续步骤

* [探索指标](../essentials/metrics-charts.md)
* [编写分析查询](../logs/log-query-overview.md)

