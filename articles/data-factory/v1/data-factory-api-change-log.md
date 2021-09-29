---
title: 数据工厂 - .NET API 更改日志
description: 描述 Azure 数据工厂的 .NET API 的特定版本中的中断性变更、新增功能、Bug 修复等。
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 18f77a9ed11d50083be4f0fe68cc0975b21d8997
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597581"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure 数据工厂 - .NET API 更改日志
> [!NOTE]
> 本文适用于数据工厂版本 1。 

本文提供有关特定版本的 Azure 数据工厂 SDK 中的更改信息。 可以在[此处](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)找到 Azure 数据工厂的最新 NuGet 包

## <a name="version-4110"></a>版本 4.11.0
新增功能：

* 已添加以下链接服务类型：
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* 已添加以下数据集类型：
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* 已添加以下复制源类型：
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>版本 4.10.0
* 已向 TextFormat 添加了以下可选属性：
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* 已添加以下链接服务类型：
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* 已添加以下数据集类型：
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* 已添加以下复制源类型：
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* 向 AzureMLBatchExecutionActivity 添加了 [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) 属性
  * 能够将多个 Web 服务输入传递到 Azure 机器学习试验

## <a name="version-491"></a>版本 4.9.1
### <a name="bug-fix"></a>Bug 修复
* 针对 [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)，已弃用基于 WebApi 的身份验证。

## <a name="version-490"></a>版本 4.9.0
### <a name="feature-additions"></a>新增功能
* 向 CopyActivity 添加了 [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) 和 [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) 属性。 有关此功能的详细信息，请参阅[暂存复制](data-factory-copy-activity-performance.md#staged-copy)。

### <a name="bug-fix"></a>Bug 修复
* 引入了 [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) 方法的重载，该方法采用 [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) 实例。
* 在 CopySink 中将 [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) 和 [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) 标记为可选。

## <a name="version-480"></a>版本 4.8.0
### <a name="feature-additions"></a>新增功能
* 已向复制活动类型添加了以下可选属性，以实现复制性能的优化：
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>版本 4.7.0
### <a name="feature-additions"></a>新增功能
* 添加了新的 StorageFormat 类型 [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) 类型，以优化的行纵栏表 (ORC) 格式复制文件。
* 向 SqlDWSink 添加了 [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) 和 PolyBaseSettings 属性。
  * 允许使用 PolyBase 将数据复制到 Azure Synapse Analytics 中。

## <a name="version-461"></a>版本 4.6.1
### <a name="bug-fixes"></a>Bug 修复
* 修复了列出活动窗口的 HTTP 请求。
  * 从请求负载中删除了资源组名称和数据工厂名称。

## <a name="version-460"></a>版本 4.6.0
### <a name="feature-additions"></a>新增功能
* 已向 [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties) 添加了以下属性：
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [数据集](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* 已向 [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo) 添加了以下属性：
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* 添加了新的 [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) 类型 [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) 类型以定义其数据为 JSON 格式的数据集。

## <a name="version-450"></a>版本 4.5.0
### <a name="feature-additions"></a>新增功能
* 添加了[活动窗口的操作列表](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)。
  * 添加了使用筛选器基于实体类型（即数据工厂、数据集、管道和活动）检索活动窗口的方法。
* 已添加以下链接服务类型：
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice)、[WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* 已添加以下数据集类型：
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset)、[WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* 已添加以下复制源类型：     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>版本 4.4.0
### <a name="feature-additions"></a>新增功能
* 已将下面的链接服务类型添加为复制活动的数据源和接收器：
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice)。 有关概念性信息和示例，请参阅 [Azure 存储 SAS 链接服务](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)。

## <a name="version-430"></a>版本 4.3.0
### <a name="feature-additions"></a>新增功能
* 已将下面的链接服务类型添加为复制活动的数据源：
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice)。 有关概念性信息和示例，请参阅[使用数据工厂从 HDFS 中移动数据](data-factory-hdfs-connector.md)。
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice)。 有关概念性信息和示例，请参阅[使用 Azure 数据工厂从 ODBC 数据存储中移动数据](data-factory-odbc-connector.md)。

## <a name="version-420"></a>版本 4.2.0
### <a name="feature-additions"></a>新增功能
* 已添加以下新的活动类型：[AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity)。 有关活动的详细信息，请参阅[使用更新资源活动更新 Azure ML 模型](data-factory-azure-ml-batch-execution-activity.md)。
* 一个新的可选属性 [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) 已被添加到 [AzureMLLinkedService 类](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)。
* [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 和 [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 属性已被添加到 [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 类。
* 允许对数据工厂服务的客户端调用配置超时。

## <a name="version-410"></a>版本 4.1.0
### <a name="feature-additions"></a>新增功能
* 已添加以下链接服务类型：
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* 已添加以下活动类型：
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* 已添加以下数据集类型：
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* 已针对复制活动添加以下源和接收器类型：
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>版本 4.0.1
### <a name="breaking-changes"></a>中断性变更
已重命名下面的类。 新名称是 4.0.0 版之前的类的原始名称。

| 4.0.0 版中的名称 | 4.0.1 版中的名称 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>版本 4.0.0
### <a name="breaking-changes"></a>中断性变更
* 已重命名下面的类/接口。

| 旧名称 | 新名称 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| 表 |数据集 |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* **列表** 方法现在返回分页的结果。 如果响应中包含非空 **NextLink** 属性，则客户端应用程序需要继续提取下一页直到返回所有页。  以下是示例：

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **列表** 管道 API 仅返回管道的摘要而不是完整的详细信息。 例如，管道摘要中的活动只包含名称和类型。

### <a name="feature-additions"></a>新增功能
* [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) 类支持两个新属性（SliceIdentifierColumnName 和 SqlWriterCleanupScript），以支持向 Azure Synapse Analytics 的幂等复制 。 有关这些属性的详细信息，请参阅 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) 一文。
* 当前支持针对 Azure SQL 数据库和 Azure Synapse Analytics 源作为复制活动的一部分运行存储过程。 [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) 和 [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) 类具有以下属性：**SqlReaderStoredProcedureName** 和 **StoredProcedureParameters**。 有关这些属性的详细信息，请参阅 Azure.com 上的 [Azure SQL 数据库](data-factory-azure-sql-connector.md#sqlsource)和 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 文章。