---
title: 将数据移Azure API for FHIR到Azure Synapse Analytics
description: 本文介绍如何将 FHIR 数据移动到 Synapse
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: e548361f6cf8d638609dd9daa83a175fa49f3679
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110486160"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>将数据从 Azure API for FHIR 移动到 Azure Synapse Analytics

本文介绍几种将数据从 Azure API for FHIR 移动到 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)的方法，这是一项无限制的分析服务，它将数据集成、企业数据仓库和大数据分析汇集在一起。 

将数据从 FHIR 服务器移动到 Synapse 涉及使用 FHIR 操作导出数据，然后执行一系列步骤来转换数据，以及将数据 `$export` 加载到 Synapse。 本文将介绍这两种方法中的两种方法，这两种方法都将展示如何在将 FHIR 资源移动到 Synapse 时将其转换为表格格式。

* **使用 T-SQL 将导出的数据加载到 Synapse：** 使用 `$export` 操作将 FHIR 资源移动到 Azure Data Lake Gen 2 (**ADL Gen 2**) Blob `NDJSON` 存储。 使用 T-SQL 将数据从存储加载到 Synapse 中的无服务器 **或** 专用 SQL 池中。 使用 Synapse 管道 将这些步骤转换为 [可靠的数据移动管道](../../synapse-analytics/get-started-pipelines.md)。
* **使用 FHIR Analytics Pipelines OSS 存储库中的工具：**[FHIR 分析管道](https://github.com/microsoft/FHIR-Analytics-Pipelines)存储库包含的工具可以创建 **Azure 数据工厂 (ADF)** 管道，以将 **FHIR** 数据移动到 Common Data Model (CDM) 文件夹，以及从 CDM 文件夹移动到 Synapse。

## <a name="load-exported-data-to-synapse-using-t-sql"></a>使用 T-SQL 将导出的数据加载到 Synapse

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` 用于将 FHIR 数据移动到 Azure Data Lake Gen 2 存储

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="使用 Azure 存储到 Synapse $export":::

#### <a name="configure-your-fhir-server-to-support-export"></a>配置 FHIR 服务器以支持 `$export`

Azure API for FHIR FHIR 规范定义的操作，以格式导出所有或筛选的 `$export` FHIR `NDJSON` 数据子集。 此外，它还支持 [取消标识导出](./de-identified-export.md) ，以在导出过程中匿名 FHIR 数据。 如果使用 ， `$export` 则默认情况下会获得取消标识功能，该功能已在 中集成 `$export` 。

若要将 FHIR 数据导出到 Azure blob 存储，首先需要将 FHIR 服务器配置为将数据导出到存储帐户。 需要 (1) 启用托管标识， (2) 中转到 "存储帐户" 中的 "访问控制" 和 "添加角色分配"， (3) 为选择存储帐户 `$export` 。 可在 [此处](./configure-export-data.md)找到更多循序渐进。

你可以将服务器配置为将数据导出到任何类型的 Azure 存储帐户，但建议将其导出到 ADL 第2代，以便与 Synapse 最佳对齐。

#### <a name="using-export-command"></a>使用 `$export` 命令

配置 FHIR 服务器后，可以按照 [文档](./export-data.md#using-export-command) 将 FHIR 资源导出到系统、患者或组级别。 例如，可以使用以下命令导出与患者相关的所有 FHIR 数据 `Group` `$export` ，在该字段中，你可以在字段中指定 ADL 第2个 blob 存储名称 `{{BlobContainer}}` ：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

你还可以使用 `_type` 上述调用中的参数 `$export` 来限制要导出的资源。 例如，以下调用将仅导出 `Patient` 、 `MedicationRequest` 和 `Observation` 资源：

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

有关支持的不同参数的详细信息，请查看 `$export` 页面部分的 [查询参数](./export-data.md#settings-and-parameters)。

### <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

使用 Synapse 之前，你将需要一个 Synapse 工作区。 你将在 Azure 门户上创建 Azure Synapse Analytics 服务。 可 [在此处](../../synapse-analytics/get-started-create-workspace.md)找到更多循序渐进指南。 需要一个 `ADLSGEN2` 帐户来创建工作区。 Azure Synapse 工作区将使用此存储帐户来存储 Synapse 工作区数据。

创建工作区之后，可以通过登录到工作区 https://web.azuresynapse.net ，或在 Azure 门户中启动 Synapse studio，来查看 Synapse Studio 上的工作区。

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>在 Azure 存储和 Synapse 之间创建链接的服务

若要将数据移动到 Synapse，需要创建一个链接服务，用于将 Azure 存储帐户与 Synapse 连接起来。 可在 [此处](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)找到更多循序渐进。

1. 在Synapse Studio，导航到"管理 **"** 选项卡，并在"**外部连接"下** 选择"链接 **服务"。**
2. 选择 **"新建** "以添加新的链接服务。
3. 从 **Azure Data Lake Storage Gen2** 选择"项目"，然后选择"继续 **"。**
4. 输入你的身份验证凭据。 完成后，选择“创建”。

在 ADL Gen 2 存储和 Synapse 之间拥有链接服务后，即可使用 Synapse SQL 池来加载和分析 FHIR 数据。

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>在无服务器和专用 SQL 池之间做出决定

Azure Synapse Analytics提供两个不同的 SQL 池：无服务器 SQL 池和专用 SQL 池。 无服务器 SQL 池提供了使用无服务器 SQL 终结点直接查询 Blob 存储中数据的灵活性，而无需任何资源预配。 专用 SQL 池具有高性能和并发的处理能力，建议用于企业级数据仓库功能。 有关这两个 SQL 池的更多详细信息，请查看 SQL 体系结构上的 [Synapse](../../synapse-analytics/sql/overview-architecture.md) 文档页。

#### <a name="using-serverless-sql-pool"></a>使用无服务器 SQL 池

由于它是无服务器的，因此无需设置基础结构，也无需维护群集。 创建工作区后，Synapse Studio开始查询数据。

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

在以上查询中，函数访问 Azure 存储 中的文件，分析 JSON 文本，并返回 `OPENROWSET` `OPENJSON` JSON 输入属性作为行和列。 每次执行此查询时，无服务器 SQL 池都会从 Blob 存储读取文件、分析 JSON 并提取字段。

还可以将结果以 Parquet 格式在外部表中实现 [，](../../synapse-analytics/sql/develop-tables-external-tables.md) 以获得更好的查询性能，如下所示：

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

专用 SQL 池支持托管表和分层缓存，以提高性能。 可以使用简单的 T-SQL 查询导入大数据，然后使用分布式查询引擎强大的功能来运行高性能分析。

将数据从存储加载到专用 SQL 池的最简单、最快捷的方法是使用 **`COPY`** t-sql 中的命令，该命令可以读取 CSV、Parquet 和 ORC 文件。 如下面的示例查询中所示，使用 `COPY` 命令将 `NDJSON` 行加载到表格结构。 

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

在上表中获得 JSON 行后 `StagingPatient` ，可以使用函数创建不同的表格格式的数据，并将 `OPENJSON` 结果存储到表中。 下面是一个示例 SQL 查询，用于 `Patient` 通过从资源提取几个字段来创建表 `Patient` ：

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

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>使用 FHIR 分析管道 OSS 工具

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="使用 ADF 管道将数据移动到 CDM 文件夹，然后移动到 Synapse 中。":::

> [!Note]
> [FHIR Analytics 管道](https://github.com/microsoft/FHIR-Analytics-Pipelines) 是在 MIT 许可证下发布的开源工具，不适用于 Azure 服务的 Microsoft SLA。

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>用于将 FHIR 数据移入 CDM 文件夹中的 ADF 管道

通用数据模型 (CDM) 文件夹是 Data lake 中的一个文件夹，该文件夹符合定义完善且标准化的元数据结构和自描述数据。 这些文件夹使数据生成者和数据使用者之间的元数据互操作性得以简化。 将 FHIR 数据移入 CDM 文件夹之前，可将数据转换为表配置。

### <a name="generating-table-configuration"></a>正在生成表配置

克隆 te 存储库获取所有脚本和源代码。 用于 `npm install` 安装依赖项。 在文件夹中运行以下命令 `Configuration-Generator` ，以使用 YAML 格式指令生成表配置文件夹：

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

您可以使用示例 `YAML` 文件， `resourcesConfig.yml` 并在存储库 `propertiesGroupConfig.yml` 中提供。

### <a name="generating-adf-pipeline"></a>生成 ADF 管道

现在，你可以使用生成的表配置的内容和一些其他配置来生成 ADF 管道。 触发此 ADF 管道时，会使用 API 从 FHIR 服务器导出数据， `$export` 并将其写入 CDM 文件夹以及关联的 CDM 元数据。

1. 创建 Azure Active Directory (AD) 应用程序和服务主体。 ADF 管道使用 Azure 批处理服务执行转换，并且需要Azure AD服务的应用程序。 请 [Azure AD文档](../../active-directory/develop/howto-create-service-principal-portal.md)。
2. 向服务主体授予导出存储位置的访问权限。 在 `Access Control` 导出存储的 中，向 `Storage Blob Data Contributor` 应用程序Azure AD角色。
3. 部署出口管道。 使用模板 `fhirServiceToCdm.json` 在 Azure 上进行自定义部署。 此步骤将创建以下 Azure 资源：
    - 一个名称为 的 ADF 管道 `{pipelinename}-df` 。
    - 一个密钥保管库，其名称 `{pipelinename}-kv` 用于存储客户端机密。
    - 一个批处理帐户，其名称 `{pipelinename}batch` 用于运行转换。
    - 一个名称为 的存储帐户 `{pipelinename}storage` 。
4. 授予对 Azure 数据工厂 的访问权限。 在 FHIR 服务的访问控制面板中，向数据 `FHIR data exporter` `FHIR data reader` 工厂授予 和 角色 `{pipelinename}-df` 。
5. 将表配置文件夹的内容上传到配置容器。
6. 转到 `{pipelinename}-df` ，并触发管道。 应在存储帐户 的 CDM 文件夹中看到导出的数据 `{pipelinename}storage` 。 对于具有 CSV 文件的每个表，应会看到一个文件夹。

### <a name="from-cdm-folder-to-synapse"></a>从 CDM 文件夹到 Synapse

以 CDM 格式导出数据并存储在 ADL Gen 2 存储中后，现在可以将数据移到 CDM 文件夹中的 Synapse。

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

在上述配置文件中运行此脚本：

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

将 ADF 托管标识作为 SQL 用户添加到 SQL 数据库。 下面是一个用于创建用户和分配角色的示例 SQL 脚本：

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>后续步骤

本文介绍了两种不同的方法将 FHIR 数据移到 Synapse： (1) 使用将 `$export` 数据移动到 ADL 第2代 blob 存储，然后将数据加载到 SYNAPSE SQL 池中， (2) 使用 ADF 管道将 FHIR 数据移到 CDM 文件夹，然后再进入 Synapse。

接下来，可以在将数据移动到 Synapse 时了解匿名的 FHIR 数据，以确保医疗保健信息受到保护：
 
>[!div class="nextstepaction"]
>[导出已取消识别的数据](./de-identified-export.md)








