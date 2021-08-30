---
title: 将 Azure API for FHIR 中的数据移动到 Azure Synapse Analytics
description: 本文介绍如何将 FHIR 数据移动到 Synapse
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: e548361f6cf8d638609dd9daa83a175fa49f3679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778484"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>将数据从 Azure API for FHIR 移动到 Azure Synapse Analytics

本文介绍几种将数据从 Azure API for FHIR 移动到 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/) 的方法，后者是一项不受限制的分析服务，它将数据集成、企业数据存储和大数据分析汇集在一起。 

将数据从 FHIR 服务器移动到 Synapse 涉及使用 FHIR `$export` 操作导出数据，然后执行一系列步骤来转换数据以及将数据加载到 Synapse。 本文将介绍多种方法中的两种方法，这两种方法都将展示如何在将 FHIR 资源移动到 Synapse 时将其转换为表格格式。

* 使用 T-SQL 将导出的数据加载到 Synapse：使用 `$export` 操作以 `NDJSON` 格式将 FHIR 资源移动到 Azure Data Lake Gen 2 (ADL Gen 2) Blob 存储 。 使用 T-SQL 将数据从存储载入 Synapse 中的无服务器或专用 SQL 池。 使用 [Synapse 管道](../../synapse-analytics/get-started-pipelines.md)将这些步骤转换为可靠的数据移动管道。
* 使用 FHIR Analytics Pipelines OSS 存储库中的工具：[FHIR Analytics Pipeline](https://github.com/microsoft/FHIR-Analytics-Pipelines) 存储库包含可创建 Azure 数据工厂 (ADF) 管道的工具，用于将 FHIR 数据移动到 Common Data Model (CDM) 文件夹，以及从 CDM 文件夹移动到 Synapse  。

## <a name="load-exported-data-to-synapse-using-t-sql"></a>使用 T-SQL 将导出的数据加载到 Synapse

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` 用于将 FHIR 数据移动到 Azure Data Lake Gen 2 存储

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="使用 $export 将 Azure 存储移动到 Synapse":::

#### <a name="configure-your-fhir-server-to-support-export"></a>配置 FHIR 服务器以支持 `$export`

Azure API for FHIR 采用 FHIR 规范定义的 `$export` 操作，以 `NDJSON` 格式导出所有或筛选的 FHIR 数据子集。 此外，它还支持[取消标识导出](./de-identified-export.md)，以在导出过程中匿名处理 FHIR 数据。 如果使用 `$export`，则默认情况下会获得取消标识功能，该功能已在 `$export` 中集成。

若要将 FHIR 数据导出到 Azure Blob 存储，首先需要将 FHIR 服务器配置为将数据导出到存储帐户。 需要执行以下操作：(1) 启用托管标识，(2) 转到存储帐户中的“访问控制”并添加角色分配，(3) 为 `$export` 选择存储帐户。 可以在[此处](./configure-export-data.md)找到更多分步说明。

可以将服务器配置为将数据导出到任何类型的 Azure 存储帐户，但我们建议导出到 ADL Gen 2，以便与 Synapse 保持高度一致。

#### <a name="using-export-command"></a>使用 `$export` 命令

配置 FHIR 服务器后，可以按照[文档](./export-data.md#using-export-command)中的内容在系统、患者或组级别导出 FHIR 资源。 例如，可以使用以下 `$export` 命令导出与 `Group` 中患者相关的所有 FHIR 数据，其中可以在字段 `{{BlobContainer}}` 中指定 ADL Gen 2 Blob 存储名称：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

还可以在上面的 `$export` 调用中使用 `_type` 参数来限制要导出的资源。 例如，以下调用将仅导出 `Patient`、`MedicationRequest` 和 `Observation` 资源：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

有关支持的不同参数的详细信息，请查看有关[查询参数](./export-data.md#settings-and-parameters)的 `$export` 页面部分。

### <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

使用 Synapse 之前，需要具有 Synapse 工作区。 你将在 Azure 门户上创建 Azure Synapse Analytics 服务。 可以在[此处](../../synapse-analytics/get-started-create-workspace.md)更多的分步指南。 需要 `ADLSGEN2` 帐户才能创建工作区。 Azure Synapse 工作区将使用此存储帐户来存储 Synapse 工作区数据。

创建工作区后，可以通过在 https://web.azuresynapse.net 上登录工作区，或在 Azure 门户中启动 Synapse Studio 来查看工作区。

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>在 Azure 存储和 Synapse 之间创建链接服务

若要将数据移动到 Synapse，需要创建一个将 Azure 存储帐户与 Synapse 进行连接的链接服务。 可以在[此处](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)找到更多分步说明。

1. 在 Synapse Studio 中，导航到“管理”选项卡，并在“外部连接”下，选择“链接服务”  。
2. 选择“新建”以添加新的链接服务。
3. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后选择“继续” 。
4. 输入你的身份验证凭据。 完成后，选择“创建”。

在 ADL Gen 2 存储和 Synapse 之间拥有链接服务后，即可使用 Synapse SQL 池来加载和分析 FHIR 数据。

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>在无服务器池与专用 SQL 池之间做出决定

Azure Synapse Analytics 提供两个不同的 SQL 池：无服务器 SQL 池和专用 SQL 池。 无服务器 SQL 池提供了使用无服务器 SQL 终结点直接在 Blob 存储中查询数据的灵活性，而无需进行任何资源预配。 专用 SQL 池具有高性能和并发处理能力，建议用于企业规模数据仓储功能。 有关这两种 SQL 池的详细信息，请查看有关 SQL 体系结构的 [Synapse 文档页](../../synapse-analytics/sql/overview-architecture.md)。

#### <a name="using-serverless-sql-pool"></a>使用无服务器 SQL 池

由于它是无服务器服务，因此不需要设置基础结构，也不需要维护群集。 创建工作区后，即可从 Synapse Studio 查询数据。

例如，以下查询可用于将所选字段从 `Patient.ndjson` 转换为表格结构：

```sql
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson', 
FORMAT = 'csv', 
FIELDTERMINATOR ='0x0b', 
FIELDQUOTE = '0x0b')  
WITH (doc NVARCHAR(MAX)) AS rows     
CROSS APPLY OPENJSON(doc)     
WITH ( 
    ResourceId VARCHAR(64) '$.id', 
    Active VARCHAR(10) '$.active', 
    FullName VARCHAR(100) '$.name[0].text', 
    Gender VARCHAR(20) '$.gender', 
       ...
) 
```

在上面的查询中，`OPENROWSET` 函数访问 Azure 存储中的文件，并且 `OPENJSON` 分析 JSON 文本，并返回 JSON 输入属性作为行和列。 每次执行此查询时，无服务器 SQL 池都会从 Blob 存储中读取文件、分析 JSON 并提取字段。

还可以将结果以 Parquet 格式在[外部表](../../synapse-analytics/sql/develop-tables-external-tables.md)中具体化以获得更好的查询性能，如下所示：

```sql
-- Create External data source where the parquet file will be written 
CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH ( 
    LOCATION = 'https://{{youraccount}}.blob.core.windows.net/{{exttblcontainer}}' 
); 
GO 

-- Create External File Format 
CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH ( 
    FORMAT_TYPE = PARQUET, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec' 
); 
GO 

CREATE EXTERNAL TABLE [dbo].[Patient] WITH ( 
        LOCATION = 'PatientParquet/', 
        DATA_SOURCE = [MyDataSource], 
        FILE_FORMAT = [ParquetFF] 
) AS 
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
-- Use rest of the SQL statement from the previous example --
```

#### <a name="using-dedicated-sql-pool"></a>使用专用 SQL 池

专用 SQL 池支持托管表和分层缓存，能够提高内存中性能。 可以使用简单的 T-SQL 查询导入大数据，然后利用分布式查询引擎的功能运行高性能分析。

将数据从存储加载到专用 SQL 池的最简单快捷的方法是使用 T-SQL 中的 `COPY` 命令，该命令可以读取 CSV、Parquet 和 ORC 文件。 如下面的示例查询所示，使用 `COPY` 命令将 `NDJSON` 行加载到表格结构中。 

```sql
-- Create table with HEAP, which is not indexed and does not have a column width limitation of NVARCHAR(4000) 
CREATE TABLE StagingPatient ( 
Resource NVARCHAR(MAX) 
) WITH (HEAP) 
COPY INTO StagingPatient 
FROM 'https://{{yourblobaccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
WITH ( 
FILE_TYPE = 'CSV', 
ROWTERMINATOR='0x0a', 
FIELDQUOTE = '', 
FIELDTERMINATOR = '0x00' 
) 
GO
```

在上面的 `StagingPatient` 表中具有 JSON 行后，可以使用 `OPENJSON` 函数创建数据的不同表格式并将结果存储到表中。 下面是通过从 `Patient` 资源中提取几个字段来创建 `Patient` 表的示例 SQL 查询：

```sql
SELECT RES.* 
INTO Patient 
FROM StagingPatient
CROSS APPLY OPENJSON(Resource)   
WITH (
    ResourceId VARCHAR(64) '$.id',
    FullName VARCHAR(100) '$.name[0].text',
    FamilyName VARCHAR(50) '$.name[0].family',
    GivenName VARCHAR(50) '$.name[0].given[0]',
    Gender VARCHAR(20) '$.gender',
    DOB DATETIME2 '$.birthDate',
    MaritalStatus VARCHAR(20) '$.maritalStatus.coding[0].display',
    LanguageOfCommunication VARCHAR(20) '$.communication[0].language.text'
) AS RES 
GO

```

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>使用 FHIR Analytics Pipelines OSS 工具

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="使用 ADF 管道将数据移动到 CDM 文件夹中，然后移动到 Synapse":::

> [!Note]
> [FHIR Analytics 管道](https://github.com/microsoft/FHIR-Analytics-Pipelines)是根据 MIT 许可证发布的开放源代码工具，未涵盖在适用于 Azure 服务的 Microsoft SLA 中。

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>用于将 FHIR 数据移动到 CDM 文件夹的 ADF 管道

Common Data Model (CDM) 文件夹是数据湖中的一个文件夹，它符合明确定义且标准化的元数据结构和自描述数据。 这些文件夹有助于在数据生成者与数据使用者之间实现元数据互操作性。 在将 FHIR 数据移动到 CDM 文件夹之前，可以将数据转换为表配置。

### <a name="generating-table-configuration"></a>生成表配置

克隆存储库以获取所有脚本和源代码。 使用 `npm install` 来安装依赖项。 从 `Configuration-Generator` 文件夹运行以下命令，以使用 YAML 格式说明生成表配置文件夹：

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

可以使用存储库中提供的示例 `YAML` 文件 `resourcesConfig.yml` 和 `propertiesGroupConfig.yml`。

### <a name="generating-adf-pipeline"></a>生成 ADF 管道

现在，可以使用生成的表配置和一些其他配置的内容来生成 ADF 管道。 此 ADF 管道在触发时，会使用 `$export` API 从 FHIR 服务器导出数据，并写入 CDM 文件夹以及关联的 CDM 元数据。

1. 创建 Azure Active Directory (AD) 应用程序和服务主体。 ADF 管道使用 Azure 批处理服务执行转换，并且需要 Azure AD 应用程序来提供批处理服务。 请按照 [Azure AD 文档](../../active-directory/develop/howto-create-service-principal-portal.md)中的说明操作。
2. 向服务主体授予导出存储位置的访问权限。 在导出存储的 `Access Control` 中，向 Azure AD 应用程序授予 `Storage Blob Data Contributor` 角色。
3. 部署流出管道。 使用模板 `fhirServiceToCdm.json` 在 Azure 上进行自定义部署。 此步骤将创建以下 Azure 资源：
    - 一个名为 `{pipelinename}-df` 的 ADF 管道。
    - 一个名为 `{pipelinename}-kv` 的密钥保管库，用于存储客户端机密。
    - 一个名为 `{pipelinename}batch` 的批处理帐户，用于运行转换。
    - 一个名为 `{pipelinename}storage` 的存储帐户。
4. 向 Azure 数据工厂授予访问权限。 在 FHIR 服务的“访问控制”面板中，向数据工厂 `{pipelinename}-df` 授予 `FHIR data exporter` 和 `FHIR data reader` 角色。
5. 将表配置文件夹的内容上传到配置容器。
6. 转到 `{pipelinename}-df` 并触发管道。 应在存储帐户 `{pipelinename}storage` 的 CDM 文件夹中看到导出的数据。 对于具有 CSV 文件的每个表，应会看到一个文件夹。

### <a name="from-cdm-folder-to-synapse"></a>从 CDM 文件夹到 Synapse

以 CDM 格式导出数据并存储在 ADL Gen 2 存储中后，便可以将 CDM 文件夹中的数据移到 Synapse。

可以使用配置文件创建从 CDM 到 Synapse 的管道，如下所示：

```json
{
  "ResourceGroup": "",
  "TemplateFilePath": "../Templates/cdmToSynapse.json",
  "TemplateParameters": {
    "DataFactoryName": "",
    "SynapseWorkspace": "",
    "DedicatedSqlPool": "",
    "AdlsAccountForCdm": "",
    "CdmRootLocation": "cdm",
    "StagingContainer": "adfstaging",
    "Entities": ["LocalPatient", "LocalPatientAddress"]
  }
}
```

使用上述配置文件运行此脚本：

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

将 ADF 托管标识作为 SQL 用户添加到 SQL 数据库中。 下面是用于创建用户和分配角色的 SQL 脚本示例：

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>后续步骤

本文介绍了将 FHIR 数据移动到 Synapse 的两种方法：(1) 使用 `$export` 将数据移动到 ADL Gen 2 Blob 存储中，然后将数据载入 Synapse SQL 池，以及 (2) 使用 ADF 管道将 FHIR 数据移到 CDM 文件夹，然后再移动到 Synapse。

接下来，可以在将数据移动到 Synapse 时了解 FHIR 数据的匿名处理方式，以确保医疗保健信息受到保护：
 
>[!div class="nextstepaction"]
>[导出取消标识的数据](./de-identified-export.md)








