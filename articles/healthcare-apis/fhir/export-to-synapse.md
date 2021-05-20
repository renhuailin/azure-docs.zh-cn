---
title: 将 Azure API 中的数据导出到 Azure Synapse Analytics FHIR
description: 本文介绍了如何将 FHIR 数据移入 Synapse
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: 1fc264109b0cc2d6534f0c8b213dfc250c700d15
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116766"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>将数据从 Azure API for FHIR 移动到 Azure Synapse Analytics

在本文中，你将学习多种方法，将数据从用于 FHIR 的 Azure API 迁移到 [Azure Synapse analytics](https://azure.microsoft.com/services/synapse-analytics/)，这是一项无限制的分析服务，可将数据集成、企业数据仓库和大数据分析集中在一起。 

将数据从 FHIR 服务器移动到 Synapse 涉及使用 FHIR 操作导出数据， `$export` 然后执行一系列步骤来转换数据并将数据加载到 Synapse。 本文将指导你完成多种方法，这两种方法都将显示如何在将 FHIR 资源转换为表格格式时将其转换为表格格式。

* **使用 t-sql 将导出的数据加载到 Synapse：** 使用 `$export` 操作将 FHIR 的资源移到 **Azure Data Lake 第2代 (ADL 第2代) blob 存储** 中的 `NDJSON` 格式。 使用 T-sql 将数据从存储加载到 Synapse 中的 **无服务器或专用的 SQL 池中** 。 使用 [Synapse 管道](../../synapse-analytics/get-started-pipelines.md)将这些步骤转换为可靠的数据移动管道。
* **使用 FHIR 分析管道 OSS 存储库中的工具：**[FHIR Analytics 管道](https://github.com/microsoft/FHIR-Analytics-Pipelines)存储库包含的工具可用于创建 **Azure 数据工厂 (ADF) 管道** 将 FHIR 数据移入 **通用数据模型 (CDM) 文件夹**，以及从 CDM 文件夹移动到 Synapse。

## <a name="load-exported-data-to-synapse-using-t-sql"></a>使用 T-sql 将导出的数据加载到 Synapse

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` 用于将 FHIR 数据移入 Azure Data Lake 第2代存储

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="使用 $export 的 Azure 存储到 Synapse":::

#### <a name="configure-your-fhir-server-to-support-export"></a>配置 FHIR 服务器以支持 `$export`

用于 FHIR 的 Azure API 实现了 `$export` FHIR 规范定义的操作，以导出格式为 FHIR 数据的所有或已筛选子集 `NDJSON` 。 此外，它还支持在导出过程中 [取消识别](./de-identified-export.md) 到匿名 FHIR 数据的导出。 如果使用 ， `$export` 则默认情况下会获得取消标识功能，该功能已在 中集成 `$export` 。

若要将 FHIR 数据导出到 Azure Blob 存储，首先需要将 FHIR 服务器配置为将数据导出到存储帐户。 需要启用 (1) ， (2) 转到存储帐户中的"访问控制"并添加角色分配， (3) 选择存储帐户 `$export` 。 可在此处找到更多分 [步说明](./configure-export-data.md)。

可以将服务器配置为将数据导出到任何类型的 Azure 存储帐户，但我们建议导出到 ADL Gen 2，以与 Synapse 进行最佳对齐。

#### <a name="using-export-command"></a>使用 `$export` 命令

配置 FHIR 服务器后，可以按照文档在[](./export-data.md#using-export-command)系统、患者或组级别导出 FHIR 资源。 例如，可以使用以下命令在 中导出与患者相关的所有 FHIR 数据，可在字段中指定 `Group` `$export` ADL Gen 2 Blob 存储名称 `{{BlobContainer}}` ：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

还可以在 `_type` 以上调用 `$export` 中使用 参数来限制要导出的资源。 例如，以下调用将仅导出 `Patient` 、 `MedicationRequest` 和资源 `Observation` ：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

有关支持的不同参数详细信息，请查看有关查询参数 的页面 `$export` [部分](./export-data.md#settings-and-parameters)。

### <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

在使用 Synapse 之前，需要 Synapse 工作区。 你将在 Azure Synapse Analytics 上创建Azure 门户。 可在此处找到更多分步 [指南](../../synapse-analytics/get-started-create-workspace.md)。 需要一 `ADLSGEN2` 个帐户来创建工作区。 你的Azure Synapse工作区将使用此存储帐户来存储 Synapse 工作区数据。

创建工作区后，可以通过登录 Synapse Studio 上的工作区或在工作区中启动Synapse Studio来查看 https://web.azuresynapse.net Azure 门户。

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>在 Azure 存储和 Synapse 之间创建链接服务

若要将数据移动到 Synapse，需要创建一个链接服务，用于将 Azure 存储帐户与 Synapse 连接。 可在 [此处](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)找到更多循序渐进。

1. 在 Synapse Studio 中，导航到 " **管理** " 选项卡，在 " **外部连接**" 下，选择 " **链接服务**"。
2. 选择 " **新建** " 以添加新的链接服务。
3. 从列表中选择 " **Azure Data Lake Storage Gen2** "，然后选择 " **继续**"。
4. 输入你的身份验证凭据。 完成后，选择“创建”。

现在，你已在 ADL 第2代存储和 Synapse 之间建立了链接服务，接下来可以使用 Synapse SQL 池来加载和分析 FHIR 数据。

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>在无服务器和专用 SQL 池之间确定

Azure Synapse Analytics 提供两个不同的 SQL 池：无服务器 SQL 池和专用 SQL 池。 无服务器 SQL 池使你可以灵活地使用无服务器的 SQL 终结点直接在 blob 存储中查询数据，无需任何资源设置。 专用 SQL 池具有处理能力以实现高性能和并发性，建议用于企业级的数据仓库功能。 有关这两个 SQL 池的详细信息，请参阅 SQL 体系结构上的 [Synapse 文档页](../../synapse-analytics/sql/overview-architecture.md) 。

#### <a name="using-serverless-sql-pool"></a>使用无服务器 SQL 池

由于它是无服务器的，因此没有要维护的基础结构。 一旦创建了工作区，就可以开始从 Synapse Studio 查询数据。

例如，以下查询可用于将所选字段从转换 `Patient.ndjson` 为表格结构：

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

在上面的查询中，该 `OPENROWSET` 函数将访问 Azure 存储中的文件，并 `OPENJSON` 分析 json 文本，并以行和列的形式返回 json 输入属性。 每次执行此查询时，无服务器 SQL 池将从 blob 存储读取文件，分析 JSON，然后提取字段。

你还可以将结果具体化为 [外部表](../../synapse-analytics/sql/develop-tables-external-tables.md) 中的 Parquet 格式，以获得更好的查询性能，如下所示：

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

专用 SQL 池支持托管表和层次结构缓存来实现内存中性能。 可以使用简单的 T-SQL 查询导入大数据，然后使用分布式查询引擎强大的功能来运行高性能分析。

将数据从存储加载到专用 SQL 池的最简单且最快的方法就是使用 T-SQL 中的 命令，该命令可以 **`COPY`** 读取 CSV、Parquet 和 ORC 文件。 与下面的示例查询一样，使用 `COPY` 命令将 `NDJSON` 行加载到表格结构中。 

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

在上表中具有 JSON 行后，可以使用 函数创建不同的表格格式的数据，将结果 `StagingPatient` `OPENJSON` 存储到表中。 下面是通过从资源中提取几个字段来创建表的示例 SQL `Patient` `Patient` 查询：

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
> [FHIR Analytics 管道](https://github.com/microsoft/FHIR-Analytics-Pipelines) 是 MIT 许可证下发布的开源工具，未涵盖在 Azure 服务的 Microsoft SLA 中。

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>用于将 FHIR 数据移动到 CDM 文件夹的 ADF 管道

Common Data Model (CDM) 文件夹是数据湖中的一个文件夹，符合明确定义和标准化的元数据结构和自描述数据。 这些文件夹有助于数据生成者与数据使用者之间的元数据互操作性。 在将 FHIR 数据移动到 CDM 文件夹之前，可以将数据转换为表配置。

### <a name="generating-table-configuration"></a>生成表配置

克隆存储库获取所有脚本和源代码。 使用 `npm install` 安装依赖项。 从 文件夹中运行以下 `Configuration-Generator` 命令，以使用 YAML 格式说明生成表配置文件夹：

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

可以使用示例 `YAML` 文件， `resourcesConfig.yml` 这些文件在 `propertiesGroupConfig.yml` 存储库提供。

### <a name="generating-adf-pipeline"></a>生成 ADF 管道

现在，可以使用生成的表配置的内容和一些其他配置来生成 ADF 管道。 此 ADF 管道在触发时，使用 API 从 FHIR 服务器导出数据，并写入 CDM 文件夹 `$export` 以及关联的 CDM 元数据。

1. 创建 Azure Active Directory (AD) 应用程序和服务主体。 ADF 管道使用 Azure batch 服务进行转换，并需要用于批处理服务的 Azure AD 应用程序。 按照 [Azure AD 文档](../../active-directory/develop/howto-create-service-principal-portal.md)操作。
2. 向服务主体授予对导出存储位置的访问权限。 在 `Access Control` 导出存储的中，向 `Storage Blob Data Contributor` Azure AD 应用程序授予角色。
3. 部署出口管道。 `fhirServiceToCdm.json`在 Azure 上使用自定义部署的模板。 此步骤将创建以下 Azure 资源：
    - 名称为的 ADF 管道 `{pipelinename}-df` 。
    - 具有存储客户端机密名称的密钥保管库 `{pipelinename}-kv` 。
    - 具有要运行转换的名称的批处理帐户 `{pipelinename}batch` 。
    - 名称为的存储帐户 `{pipelinename}storage` 。
4. 向 Azure 数据工厂授予访问权限。 在 FHIR 服务的 "访问控制" 面板中，将 `FHIR data exporter` 和 `FHIR data reader` 角色授予数据工厂 `{pipelinename}-df` 。
5. 将表配置文件夹的内容上传到配置容器。
6. 中转到 `{pipelinename}-df` ，并触发管道。 你应在存储帐户上的 CDM 文件夹中看到导出的数据 `{pipelinename}storage` 。 对于包含 CSV 文件的每个表，你应该会看到一个文件夹。

### <a name="from-cdm-folder-to-synapse"></a>从 CDM 文件夹到 Synapse

将数据以 CDM 格式导出并存储在 ADL 第2代存储中后，即可将 CDM 文件夹中的数据移动到 Synapse。

可以使用配置文件创建 CDM 到 Synapse 管道，如下所示：

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

将 ADF 托管标识作为 SQL 用户添加到 SQL 数据库中。 下面是用于创建用户和分配角色的示例 SQL 脚本：

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>后续步骤

本文介绍了将 FHIR 数据移动到 Synapse 的两种不同方法： (1) 使用 将数据移动到 ADL Gen 2 Blob 存储，然后将数据加载到 Synapse SQL 池中; (2) 使用 ADF 管道将数据移动到 CDM 文件夹，然后移动到 `$export` Synapse。

接下来，可以在将数据移动到 Synapse 时了解 FHIR 数据的匿名化，以确保医疗保健信息受到保护：
 
>[!div class="nextstepaction"]
>[导出已取消标识的数据](./de-identified-export.md)








