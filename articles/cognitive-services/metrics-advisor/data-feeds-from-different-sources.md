---
title: 如何将来自不同源的数据馈送添加到指标顾问
titleSuffix: Azure Cognitive Services
description: 将不同的数据馈送添加到指标顾问
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348613"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>将来自不同数据源的数据馈送添加到指标顾问

使用本文可找到将不同类型的数据源连接到指标顾问的设置和要求。 请务必阅读[如何加入数据](how-tos/onboard-your-data.md)，了解有关在指标顾问中使用数据的关键概念。 

## <a name="supported-authentication-types"></a>支持的身份验证类型

| 身份验证类型 | 说明 |
| ---------------------|-------------|
|**基本** | 需要提供基本参数才能访问数据源。 例如，连接字符串或密钥。 数据馈送管理员能够查看这些凭据。 |
| **AzureManagedIdentity** | Azure 资源的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)是 Azure Active Directory 的一项功能。 它为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识功能向支持 Azure AD 身份验证的任何服务进行身份验证。|
| **AzureSQLConnectionString**| 将 AzureSQL 连接字符串作为“凭据实体”存储在指标顾问中，并在每次加入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但允许获得授权的查看者创建数据馈送，无需知道凭据的详细信息。 |
| **DataLakeGen2SharedKey**| 将数据湖帐户密钥作为“凭据实体”存储在指标顾问中，并在每次加入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但允许获得授权的查看者创建数据馈送，无需知道凭据详细信息。|
| **服务主体**| 将服务主体作为“凭据实体”存储在指标顾问中，并在每次加入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但允许获得授权的查看者创建数据馈送，无需知道凭据详细信息。|
| **密钥保管库中的服务主体**|将密钥保管库中的服务主体作为“凭据实体”存储在指标顾问中，并在每次加入指标数据时直接使用它。 只有凭据实体的管理员才能查看这些凭据，但也允许查看者创建数据馈送，无需知道详细的凭据信息。 |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>支持的数据源和相应的身份验证类型


| 数据源 | 身份验证类型 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  基本 |
|[**Azure Blob 存储 (JSON)**](#blob) | 基本<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | 基本 |
|[**Azure 数据资源管理器(Kusto)**](#kusto) | 基本<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | 基本<br>DataLakeGen2SharedKey<br>服务主体<br>密钥保管库中的服务主体<br> |
|[**Azure SQL 数据库 / SQL Server**](#sql) | 基本<br>ManagedIdentity<br>服务主体<br>密钥保管库中的服务主体<br>AzureSQLConnectionString
|[**Azure 表存储**](#table) | 基本 | 
|[**ElasticSearch**](#es) | 基本 |
|[**Http 请求**](#http) | 基本 | 
|[**InfluxDB (InfluxQL)**](#influxdb) | 基本 |
|[**MongoDB**](#mongodb) | 基本 |
|[**MySQL**](#mysql) | 基本 |
|[**PostgreSQL**](#pgsql)| 基本|

创建“凭据实体”并使用它对数据源进行身份验证。 以下各节指定基本身份验证所需的参数。 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **应用程序 ID**：使用 Application Insights API 时，可以使用应用程序 ID 来标识此应用程序。 若要获取应用程序 ID，请执行以下操作：

    1. 在 Application Insights 资源中，单击“API 访问权限”  。

    2. 将生成的应用程序 ID 复制到指标顾问中的“应用程序 ID”字段。 
    
    有关详细信息，请参阅 [Azure 机器人服务文档](/azure/bot-service/bot-service-resources-app-insights-keys#application-id)。

* **API 密钥**：浏览器外的应用程序使用 API 密钥访问此资源。 若要获取 API 密钥，请执行以下操作：

    1. 在 Application Insights 资源中，单击“API 访问权限”  。

    2. 单击“创建 API 密钥”  。

    3. 输入简短说明，选中“读取遥测”选项，然后单击“生成密钥”按钮。

    4. 将 API 密钥复制到指标顾问中的“API 密钥”字段。

* **查询**：Azure Application Insights 日志在 Azure 数据资源管理器之上构建，Azure Monitor 日志查询使用同一 Kusto 查询语言的某个版本。 [Kusto 查询语言文档](/azure/data-explorer/kusto/query/)提供了该语言的完整详细信息，在针对 Application Insights 编写查询时，应将此文档用作主要参考资源。 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob 存储 (JSON)</span>

* **连接字符串**：有关检索此字符串的信息，请参阅 Azure Blob 存储 [连接字符串](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account)一文。

* **容器**：指标顾问期望将时序数据作为 Blob 文件存储在单个容器下（每个时间戳一个 Blob）。 这是容器名称字段。

* **Blob 模板**：这是 Blob 文件名的模板。 例如：`/%Y/%m/X_%Y-%m-%d-%h-%M.json`。 支持以下参数：
  * `%Y` 是格式为 `yyyy` 的年份
  * `%m` 是格式为 `MM` 的月份
  * `%d` 是格式为 `dd` 的日期
  * `%h` 是格式为 `HH` 的小时
  * `%M` 是格式为 `mm` 的分钟

* **JSON 格式版本**：定义 JSON 文件中的数据架构。 指标顾问目前支持两个版本：
  
  * v1（默认值）

      仅接受“名称”和“值”指标 。 例如： 。
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      还接受“维度”和“时间戳”指标 。 例如： 。
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

每个 JSON 文件只能有一个时间戳。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **连接字符串**：访问 Azure Cosmos DB 的连接字符串。 这可在 Cosmos DB 资源的“密钥”中找到。 
* **数据库**：要查询的数据库。 这可在“容器”部分下的“浏览”页中找到 。
* **集合 ID**：要查询的集合 ID。 这可在“容器”部分下的“浏览”页中找到 。
* **SQL 查询**：一个 SQL 查询，用于获取数据并将数据构建为多维时序数据。 可在查询中使用 `@StartTime` 和 `@EndTime` 变量。 它们应格式化为：`yyyy-MM-dd HH:mm:ss`。

    示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    从 2019/12/12 开始的数据切片的示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure 数据资源管理器(Kusto)</span>

* **连接字符串**：指标顾问支持使用 Azure AD 应用程序身份验证访问 Azure 数据资源管理器(Kusto)。 你将需要创建并注册一个 Azure AD 应用程序，然后授权其访问 Azure 数据资源管理器数据库。 若要获取连接字符串，请参阅 [Azure 数据资源管理器](/azure/data-explorer/provision-azure-ad-app)文档。

* **查询**：请参阅 [Kusto 查询语言](/azure/data-explorer/kusto/query)，了解如何获取数据并将其表述为多维时序数据。 可在查询中使用 `@StartTime` 和 `@EndTime` 变量。 它们应格式化为：`yyyy-MM-dd HH:mm:ss`。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **帐户名称**：Azure Data Lake Storage Gen2 的帐户名称。 这可在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源的“访问密钥”中找到。

* **帐户密钥**：请指定访问 Azure Data Lake Storage Gen2 的帐户名称。 这可在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源的“访问密钥”设置中找到。

* **文件系统名称（容器）** ：指标顾问将期望你的时序数据作为 Blob 文件存储在单个容器下（每个时间戳一个 Blob）。 这是容器名称字段。 这可在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 实例中找到，然后单击“Blob 服务”部分中的“容器”。

* **目录模板**：这是 Blob 文件的目录模板。 例如：/%Y/%m/%d。 支持以下参数：
  * `%Y` 是格式为 `yyyy` 的年份
  * `%m` 是格式为 `MM` 的月份
  * `%d` 是格式为 `dd` 的日期
  * `%h` 是格式为 `HH` 的小时
  * `%M` 是格式为 `mm` 的分钟

* **文件模板**：这是 Blob 文件的文件模板。 例如：X_%Y-%m-%d-%h-%M.json。 支持以下参数：
  * `%Y` 是格式为 `yyyy` 的年份
  * `%m` 是格式为 `MM` 的月份
  * `%d` 是格式为 `dd` 的日期
  * `%h` 是格式为 `HH` 的小时
  * `%M` 是格式为 `mm` 的分钟

目前，指标顾问支持 JSON 文件中的数据架构，如下所示。 例如： 。

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL 数据库 | SQL Server</span>

* **连接字符串**：指标顾问接受 [ADO.NET 样式连接字符串](/dotnet/framework/data/adonet/connection-string-syntax)用于 sql server 数据源。

    示例连接字符串：

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **查询**：一个 SQL 查询，用于获取数据并将数据构建为多维时序数据。 可在查询中使用 `@StartTime` 变量来帮助获取预期指标值。

  * `@StartTime`：格式为 `yyyy-MM-dd HH:mm:ss` 的日期/时间

    示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    为 2019/12/12 的数据切片执行的实际查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure 表存储</span>

* **连接字符串**：若要了解如何从 Azure 表存储中检索连接字符串，请参阅 [查看并复制连接字符串](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys)。

* **表名**：指定要查询的表。 这可在 Azure 存储帐户实例中找到。 单击“表服务”部分中的“表” 。

* **查询** 你可在查询中使用 `@StartTime`。 `@StartTime` 在脚本中替换为 yyyy-MM-ddTHH:mm:ss 格式字符串。

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **主机**：指定 Elasticsearch 群集的主控主机。
* **端口**：指定 Elasticsearch 群集的主端口。
* **授权标头**：指定 Elasticsearch 群集的授权标头值。
* **查询**：指定要获取数据的查询。 支持占位符 @StartTime。（例如，引入数据 2020-06-21T00:00:00Z 时，@StartTime = 2020-06-21T00:00:00）

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 请求</span>

* **请求 URL**：可返回 JSON 的 HTTP url。 支持占位符 %Y、%m、%d、%h、%M：%Y = yyyy 格式的年份，%m = MM 格式的月份，%d = dd 格式的日期，%h = HH 格式的小时，%M = mm 格式的分钟。 例如：`http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`。
* **请求 HTTP 方法**：使用 GET 或 POST。
* **请求标头**：可添加基本身份验证。 
* **请求有效负载**：仅支持 JSON 有效负载。 有效负载中支持 @StartTime 占位符。 响应应为以下 JSON 格式：[{"timestamp":"2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23}, {"timestamp":"2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}]。（例如，引入数据 2020-06-21T00:00:00Z 时，@StartTime = 2020-06-21T00:00:00.0000000+00:00）

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **连接字符串**：访问 InfluxDB 的连接字符串。
* **数据库**：要查询的数据库。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。
* **用户名**：这对于身份验证是可选的。 
* **密码**：这对于身份验证是可选的。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **连接字符串**：访问 MongoDB 的连接字符串。
* **数据库**：要查询的数据库。
* **命令**：一个命令，用于获取数据并将数据构建为多维时序数据以进行引入。

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **连接字符串**：访问 MySQL DB 的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **连接字符串**：访问 PostgreSQL DB 的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为多维时序数据以进行引入。

## <a name="next-steps"></a>后续步骤

* 在等待指标数据引入到系统中时，请阅读[如何管理数据馈送配置](how-tos/manage-data-feeds.md)。
* 如果指标数据已引入，可以[配置指标值并微调检测配置](how-tos/configure-metrics.md)。