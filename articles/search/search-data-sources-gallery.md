---
title: 数据源库
titleSuffix: Azure Cognitive Search
description: 列出所有支持导入 Azure 认知搜索索引的数据源。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: e48c464ffa1aa5077f04ea1a7580e0d15e0fd7ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732603"
---
# <a name="data-sources-gallery"></a>数据源库

查找 Microsoft 或合作伙伴提供的数据连接器，以简化在搜索索引中引入数据的过程。 本文包含以下各节：

+ [认知搜索支持的正式发布数据源](#ga)
+ [认知搜索支持的预览版数据源](#preview)
+ [Power Query 连接器（预览版）](#powerquery)
+ [合作伙伴提供的数据源](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>认知搜索支持的正式发布数据源

使用索引器和以下数据源连接器从其他 Azure 服务拉取内容。 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Azure Blob 存储

由[认知搜索](search-what-is-azure-search.md)提供

提取 blob 元数据和内容，将其序列化为 JSON 文档，并将其作为搜索文档导入到搜索索引。 在数据源和索引器定义中设置属性，以针对各种 blob 内容类型进行优化。 支持自动检测更改。

[更多详细信息](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

由[认知搜索](search-what-is-azure-search.md)提供

通过 SQL API 连接到 Cosmos DB，以从容器中提取项目，序列化为 JSON 文档，并作为搜索文档导入搜索索引。 配置更改跟踪，以使用数据库中的最新更改刷新搜索索引。

[更多详细信息](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL 数据库

由[认知搜索](search-what-is-azure-search.md)提供

从单个表或视图中提取字段值，将其序列化为 JSON 文档，并将其作为搜索文档导入到搜索索引。 配置更改跟踪，以使用数据库中的最新更改刷新搜索索引。

[更多详细信息](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Azure 表存储

由[认知搜索](search-what-is-azure-search.md)提供

从 Azure 表中提取行，将其序列化为 JSON 文档，并将其作为搜索文档导入到搜索索引。 

[更多详细信息](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

由[认知搜索](search-what-is-azure-search.md)提供

通过 Azure Data Laker Storage Gen2 连接到 Azure 存储，以从目录和嵌套子目录的层次结构中提取内容。

[更多详细信息](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>认知搜索支持的预览版数据源

新数据源作为预览功能发布。 [登录](https://aka.ms/azure-cognitive-search/indexer-preview)以开始使用。

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB (Gremlin API)

由[认知搜索](search-what-is-azure-search.md)提供

通过 Gremlin API 连接到 Cosmos DB，以从容器中提取项目，序列化为 JSON 文档，并作为搜索文档导入搜索索引。 配置更改跟踪，以使用数据库中的最新更改刷新搜索索引。

[更多详细信息](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB (Mongo API)

由[认知搜索](search-what-is-azure-search.md)提供

通过 SQL API 连接到 Mongo DB，以从容器中提取项目，序列化为 JSON 文档，并作为搜索文档导入搜索索引。 配置更改跟踪，以使用数据库中的最新更改刷新搜索索引。

[更多详细信息](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

由[认知搜索](search-what-is-azure-search.md)提供

连接到 SharePoint Online 站点，并对一个或多个文档库中的文档编制索引，以用于同一个租户中的帐户和搜索服务。 默认情况下，将提取文本和规范化图像。 或者，您可以配置技能组以进行更多内容转换和扩充，或配置更改跟踪以使用 SharePoint 中的新内容或更改内容刷新搜索索引。

[更多详细信息](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

由[认知搜索](search-what-is-azure-search.md)提供

连接到 Azure 上的 MySQL 数据库以提取表中的行，序列化为 JSON 文档，并作为搜索文档导入搜索索引。 在后续运行中，此索引器将获取 MySQL 数据库的所有更改、上传和删除，并在搜索索引中反映这些更改。

[更多详细信息](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>Power Query 连接器（预览版）

连接到使用索引器和 Power Query 连接器作为数据源的其他云平台上的数据。 [登录](https://aka.ms/azure-cognitive-search/indexer-preview)以开始使用。

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到 [Amazon Redshift](https://aws.amazon.com/redshift/) 并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到云中的 [Elasticsearch](https://www.elastic.co/elasticsearch) 并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到云中的 [PostgreSQL](https://www.postgresql.org/) 数据库并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Salesforce 对象

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到 Salesforce Objects 并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Salesforce 报表

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到 Salesforce Reports 并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

连接到 Smartsheet 并提取可搜索内容以在认知搜索中编制索引。

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

由 [Power Query](/power-query/power-query-what-is-power-query) 提供技术支持

从 Snowflake 数据库中提取可搜索数据和元数据，并根据索引和数据源之间的字段到字段映射填充索引。 

[更多详细信息](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>合作伙伴提供的数据源

Microsoft 的第三方合作伙伴也会提供数据源连接器。 在使用数据源之前，请参阅我们的[使用条款声明](search-data-sources-terms-of-use.md)并了解合作伙伴许可和使用说明。

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

由 [BA Insight](https://www.bainsight.com/) 提供

Aderant 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

由 [Accenture](https://www.accenture.com) 提供

允许你的公司对 Adobe Experience Manager 服务器的内容进行爬网，同时提供连接限制和预期值或模式筛选。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

由 [BA Insight](https://www.bainsight.com/) 提供

Adobe Experience Manager 连接器支持对 Adobe Experience Manager (AEM) 平台管理的内容编制索引，并支持完整和增量爬网以确保索刷新。

[更多详细信息](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Adobe Active Experience Manager (AEM) 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Adobe AEM 中的页面、附件和其他生成的文档类型编制索引。 连接器完全支持 Adobe AEM 的权限模型、内置用户和组管理，也完全支持基于 Active Directory 或其他目录服务的 AEM 安装。

[更多详细信息](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

由 [BA Insight](https://www.bainsight.com/) 提供

Alfresco 连接器是建立在 BAI 连接器框架的基础之上，这是一个平台，用于构建所有连接器并提供与企业系统的安全连接。

[更多详细信息](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Alfresco One 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 它会以近乎实时的方式对 Alfresco One 中的文件、文件夹和用户配置文件编制索引。 连接器完全支持 Alfresco One 的权限模型、其内置用户和组管理，也完全支持基于 Active Directory 或其他目录服务的 Alfresco One 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

由 [BA Insight](https://www.bainsight.com/) 提供

Amazon Aurora 连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Oracle、MySQL 和 IBM DB2 等其他系统的数据库。

[更多详细信息](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

由 [BA Insight](https://www.bainsight.com/) 提供

Amazon Aurora 连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Oracle、MySQL 和 IBM DB2 等其他系统的数据库。

[更多详细信息](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

由 [BA Insight](https://www.bainsight.com/) 提供

Amazon S3 连接器适用于 S3 中存储的所有内容。 你的组织可以使用连接器安全地连接到 S3，并且能够对 S3 存储桶中的内容编制索引。 利用强大的筛选功能，你的组织可以控制应为 S3 中的哪些内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

由 [Accenture](https://www.accenture.com) 提供

Aspider 连接器可以实现很多功能，包括从网站抓取内容，使用 HTTP 身份验证、增量抓取、连接限制、分布式和 HTTPS 抓取等等。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence（云）

由 [BA Insight](https://www.bainsight.com/) 提供

我们的 Confluence（Cloud 版本）连接器是一个企业级索引连接器，可用于对 Confluence 中存储的内容进行爬网和编制索引。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

由 [BA Insight](https://www.bainsight.com) 提供

通过 BA Insight Azure Active Directory 连接器，可以将 Azure Active Directory 租户中的内容与其他存储库中的内容一起合并到单个整合搜索索引中，从而实现员工查找或专业知识定位器等搜索功能。

[更多详细信息](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Microsoft Azure Active Directory (Azure AD) 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器通过 Microsoft Graph API 来对 Azure AD 中的对象编制索引。 连接器可用于以近乎实时方式将主体引入到认知搜索中，以实现专家搜索、设备搜索和位置搜索等用例，或与自定义数据源结合使用来提供早期绑定安全修整。 连接器支持根据 Microsoft 365 进行联合身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Azure Blob

由 [Accenture](https://www.accenture.com) 提供

能够对 Azure Blob 容器中的内容爬网，支持增量爬网、文档级安全以及对文件夹和子文件夹的访问。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

由 [Accenture](https://www.accenture.com) 提供

Azure Data Lake 连接器将通过增量爬网、提取对象 ACL、OAuth 2 身份验证等功能，在根目录或者指定路径对 Azure Data Lake Store 云中的内容进行爬网。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure 事件中心

由 [Accenture](https://www.accenture.com) 提供

将对 Azure 事件中心中的内容进行爬网，支持对任何类型的事件或属性进行增量爬网和检索。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL 数据库

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 Azure SQL 数据库连接器遵循源数据库的安全性，并提供完整爬网和增量爬网，使用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

由 [BA Insight](https://www.bainsight.com/) 提供

利用 BAI Bentley AssetWise 连接器，可以将 AssetWise 中的内容与来自其他存储库的内容一起合并到单个整合搜索索引中。

[更多详细信息](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>Box

由 [Accenture](https://www.accenture.com) 提供

Box 连接器将对 Box 存储库中的内容进行爬网。 连接器将使用 RESTful API 检索支持的元素，提供完整或增量爬网、元数据提取、获取 ACL 等。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

由 [BA Insight](https://www.bainsight.com/) 提供

利用 Box 连接器，可以在 SharePoint 和其他门户中显示来自 Box 的内容，使用户能够从 SharePoint 和 Box 获得集成的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Box 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Box 中的文件、文件夹、评论、用户、组和任务编制索引。 该连接器完全支持 Box 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

由 [Accenture](https://www.accenture.com) 提供

Confluence 连接器将对任何 Confluence 内容存储库中的内容进行爬网。 该连接器将通过 REST API 来检索空间、页面、博客、附件和注释，允许增量爬网、提取 ACL，并且支持 HTTP 和 HTTPS，还包括其他功能。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

由 [BA Insight](https://www.bainsight.com/) 提供

Confluence 连接器是一个企业级索引连接器，可用于对 Confluence 中存储的内容进行爬网和编制索引。 这将允许 SharePoint 或任何其他门户充当单一入口，可供用户从多个内容源搜索和检索所需内容。

[更多详细信息](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Atlassian Confluence 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器它会以近乎实时的方式，可靠地为本地 Confluence 实例中的页面、博客文章、附件、评论、空间、简档和标记的中心站点编制索引。 该连接器完全支持 Atlassian Confluence 的内置用户和组管理，也完全支持基于 Active Directory 和其他目录服务的 Confluence 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Atlassian Confluence Cloud 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器它会以近乎实时的方式，可靠地为 Confluence Cloud 实例中的页面、博客文章、附件、评论、空间、简档和标记的中心站点编制索引。 该连接器完全支持 Atlassian Confluence Cloud 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

由 [BA Insight](https://www.bainsight.com/) 提供

CuadraSTAR 连接器将对 CuadraSTAR 中的内容进行爬网，并创建单个索引，这样你就能够使用 Azure 认知搜索在 CuadraSTAR 中以及超过 70 个其他支持的存储库中查找相关信息，无需执行单独的搜索。

[更多详细信息](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>数据库

由 [Accenture](https://www.accenture.com) 提供

数据库服务器连接器将对关系数据库服务器的内容进行爬网，扫描服务器上的所有数据库，并提取行和表信息。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

由 [BA Insight](https://www.bainsight.com/) 提供

Deltek Vision Connector 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。 该连接器将在 Azure、SharePoint Online 或 SharePoint 2016/2013 中编制 Deltek Vision 中的内容的索引，通过 BA Insight 的 SmartHub 显示索引，为用户提供集成的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

由 [Accenture](https://www.accenture.com) 提供

Aspire Documentum DQL 连接器将对 Documentum 中的内容进行爬网，允许基于用户定义的 DQL SELECT 语句进行胖娃、增量爬网、获取 ACL、嵌套权限的组扩展等。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 Documentum 连接器以安全方式在 Azure 认知搜索中对 Documentum 对象的全文和元数据编制索引，从而实现跨多个存储库的内容的单一可搜索结果集。 与其他一些连接器不同，该连接器使用 Azure 认知搜索一次一个地显示 Documentum 记录，以便于流程管理。

[更多详细信息](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 OpenText Documentum 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Documentum 中的存储库、文件夹和文件及其元数据和属性编制索引。 该连接器完全支持 OpenText Documentum 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>Egnyte

由 [BA Insight](https://www.bainsight.com/) 提供

Egnyte 连接器支持完全爬网和增量爬网和索引，吞吐量极高。

[更多详细信息](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

由 [Accenture](https://www.accenture.com) 提供

Elasticsearch 连接器将对 Elasticsearch 索引中的内容进行爬网，允许对多个索引进行爬网、支持切片、使用 Get of MGet 方法获取内容，还支持连接限制。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite / E3

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 Elite 连接器为律师提供单点访问，以使用 Azure 认知搜索访问与 Elite 内容一致的公司内容和知识。

[更多详细信息](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

由 [BA Insight](https://www.bainsight.com/) 提供

eRoom 连接器建立到 eRoom 应用程序的安全连接，并将内容（包括元数据和附件）从 eRoom 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

由 [BA Insight](https://www.bainsight.com/) 提供

使用 Facebook 的 Workplace 的组织现在可以通过 BA Insight Workplace by Facebook 连接器将此数据的覆盖范围扩展到其现有的搜索索引。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Facebook Workplace 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时方式，可靠地为 Facebook Workplace 中的项目组、对话和共享文档编制索引。 该连接器完全支持 Facebook Workplace 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>文件共享

由 [BA Insight](https://www.bainsight.com/) 提供

通过文件共享连接器，可以将文件共享（Windows 和 SMB/CIFS）中的内容以及其他存储库中的内容合并到单个整合搜索索引中。

[更多详细信息](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>文件系统

由 [Accenture](https://www.accenture.com) 提供

文件系统连接器将对文件系统位置中的内容进行爬网，支持增量爬网、元数据提取、按路径筛选文档，而且支持 Windows/Linux/MacOS 文件系统。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>文件系统

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对本地安装的文件系统中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对文件系统中的文件和文件夹编制索引。

[更多详细信息](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 e-Spirit FirstSpirit 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 FirstSpirit 中的页面、附件和其他生成的文档类型编制索引。 该连接器完全支持 e-Spirit FirstSpirit 的内置用户、组和权限管理，还支持基于 Active Directory 和其他目录服务的 FirstSpirit 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 GitLab 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 GitLab 中的项目、文件、文件夹、提交消息、问题和 wiki 页面编制索引。 该连接器完全支持 GitLab 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

由 [BA Insight](https://www.bainsight.com/) 提供

Google Cloud SQL 连接器可将 Google Cloud SQL 中的内容索引到 Azure 认知搜索索引中，通过 BA Insight 的 SmartHub 显示索引，为用户提供集成的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

由 [BA Insight](https://www.bainsight.com/) 提供

利用 BAI Google Drive 连接器，可以将 Google Drive 中的内容与其他存储库中的内容一起合并到一个整合的搜索索引中，后者引用 Google Drive 内容。

[更多详细信息](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Google Drive 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Google Drive 中个人驱动器和团队驱动器中的文件、文件夹和备注编制索引。 该连接器完全支持 Google Drive 的内置权限模型，也完全支持 Google Admin Directory 的用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive (EAS)

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 HP Consolidated Archive Connector 将档案中文档的全文和元数据安全地索引到各种搜索引擎中，包括 SharePoint 搜索和 Azure 搜索。 这样可以对来自多个存储库的内容构建单个可搜索结果集。 通过该连接器，组织可以利用 Consolidated Archive、SharePoint 和其他存储库中的大量可访问信息，使用户可以通过搜索立即对这些数据进行操作。

[更多详细信息](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

由 [Accenture](https://www.accenture.com) 提供

IBM Connections 连接器将对 IBM Connections 服务器中的内容进行爬网，具有增量抓取、元数据提取、ACL 获取、按正则表达式模式筛选文档等功能。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

由 [BA Insight](https://www.bainsight.com/) 提供

IBM Connections 连接器是专为 IBM Connections 开发的，可以建立到 Connections 应用程序的安全连接并将内容（包括元数据和附件）从 Connections 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 IBM Connections 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对本地 Connections 实例的公共和个人文件、博客、维基、论坛、社区、书签、配置文件和状态更编制索引。 该连接器完全支持 IBM Connections 的内置用户和组管理，也完全支持基于 Active Directory 和其他目录服务的 IBM Connections 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 IBM Connections Cloud 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对本地 Connections Cloud 中的公共和个人文件、博客、维基、论坛、社区、配置文件和状态更编制索引。 该连接器完全支持 IBM Connections Cloud 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

由 [BA Insight](https://www.bainsight.com/) 提供

IBM Content Manager 连接器会遵循源应用程序的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM Db2

由 [BA Insight](https://www.bainsight.com/) 提供

通过 Db2 连接器，组织可以利用存储在 DB2 数据库和应用程序中的大量数据，并使这些数据可通过搜索立即操作。

[更多详细信息](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

由 [BA Insight](https://www.bainsight.com/) 提供

通过 IBM FileNet Content Manager 连接器，SharePoint 用户以及其他门户用户可以安全地搜索存储在 FileNet 存储库中的内容。 对内容的访问取决于 FileNet 中规定的安全性，可确保在通过任何门户访问内容时都是安全的，就像是直接在 FileNet 中访问一样。

[更多详细信息](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

由 [BA Insight](https://www.bainsight.com/) 提供

通过 BA Insight 的 IBM Notes 电子邮件连接器，用户可以直接从 SharePoint 或其他门户搜索 Lotus Notes 电子邮件。 IBM Notes 中定义的安全性会自动反映在搜索体验中，因此用户将看到来自他们自己的邮箱、公共邮箱以及他们已被授予访问权限的其他邮箱的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 WebSphere 连接器以安全方式在 Microsoft 搜索引擎中对 WebSphere 对象的全文和元数据编制索引，从而实现跨多个存储库的内容的单一可搜索结果集。 这使组织能够利用 Microsoft 平台中大量的可访问信息，使用户可以通过搜索立即对这些数据进行操作。

[更多详细信息](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 iManage Cloud Connector 将 Work 工作区中文档的全文和元数据安全地索引到搜索引擎中。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

由 [BA Insight](https://www.bainsight.com/) 提供

iManage Work 连接器提供全面的安全性并以高吞吐量运行，可最大限度地减少爬网时间，同时对 Work 的性能影响很低。 该连接器只需要读取权限，无需在任何 iManage 服务器上安装客户端软件。 这样便可以实现对 iManage Work 中存储的所有内容的无缝且同步访问。

[更多详细信息](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

由 [BA Insight](https://www.bainsight.com/) 提供

Jira 连接器使用户能够对所有 Jira 对象执行搜索，无需直接转到 Jira。

[更多详细信息](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Atlassian Jira 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对本地 Jira 实例中的项目、事务、附件、评论、工作日志、事务历史、链接和配置文件编制索引。 该连接器完全支持 Atlassian Jira 的内置用户和组管理，也完全支持基于 Active Directory 和其他目录服务的 Jira 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

由 [BA Insight](https://www.bainsight.com/) 提供

Jira（云版本）连接器可以直接对所有 Jira 对象执行搜索，无需导航到 Jira。

[更多详细信息](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

由 [Raytion](https://www.raytion.com/contact) 提供

安全的企业搜索连接器，能够可靠地对 Atlassian Jira Cloud 中的内容编制索引，并使用 Azure 认知搜索智能搜索。 该连接器以近乎实时的方式，可靠地对 Jira Cloud 中的项目、事务、附件、评论、工作日志、事务历史、链接和配置文件编制索引。 该连接器完全支持 Atlassian Jira Cloud 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

由 [BA Insight](https://www.bainsight.com/) 提供

Jive 连接器是专为 Jive 开发的，可建立到 Jive 应用程序的安全连接并将内容（包括元数据和附件）从 Jive 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Jive 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的速度，可靠地对本地和云托管的 Jive 实例中的讨论、投票、文件、博客、空间、组、项目、任务、视频、消息、想法、配置文件和状态更新编制索引。 该连接器完全支持 Jive 的内置用户和组管理，并支持 Jive 的原生身份验证模型、OAuth 和基本身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

由 [BA Insight](https://www.bainsight.com/) 提供

Kaltura 连接器不仅能够对视频编制索引，还能对其他各种类型的信息编制索引，包括类别、数据、文档等。

[更多详细信息](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

由 [BA Insight](https://www.bainsight.com/) 提供 

LDAP 连接器使组织能够连接到任何符合 LDAP 要求的目录，并对目录中的任何记录编制索引。 组织可以筛选到目录的特定子集并仅检索特定字段，从而可以轻松搜索在目录中任何位置存储的用户、联系人或组。

[更多详细信息](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

由 [Raytion](https://www.raytion.com/contact) 提供

安全的企业搜索连接器，能够可靠地对兼容轻量级目录访问协议 (LDAP) 的目录服务中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以几乎实时的方式，可靠地对 Active Directory、Novell E-Directory 和其他 LDAP 兼容的目录服务中的 LDAP 对象编制索引。 该连接器可用于将主体引入到 Google Cloud Search 中，以用于专家、设备和位置搜索等用例，或用于实施自定义数据源的安全修整。 该连接器支持基于 SSL 的 LDAP。

[更多详细信息](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

由 [BA Insight](https://www.bainsight.com/) 提供 

BA Insight 的 OpenText LegalKEY 连接器将 LegalKEY 中的客户和案件记录的全文和元数据安全地索引到 Microsoft 搜索引擎中，从而能够在单一可搜索结果集中搜索多个存储库中的内容。 通过该连接器，组织可以利用 LegalKEY、SharePoint 和其他存储库中的大量可访问信息，使用户可以通过搜索立即对这些数据进行操作。

[更多详细信息](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

由 [BA Insight](https://www.bainsight.com/) 提供

通过 LexisNexis InterAction 连接器，整个组织的律师和其他公司员工可以更轻松找到存储在 InterAction 中的重要信息，而无需登录并执行单独搜索。

[更多详细信息](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Lotus Notes 数据库

由 [BA Insight](https://www.bainsight.com/) 提供 

借助 IBM Notes 数据库连接器，用户可以使用 Azure 认知搜索查找 Notes 数据库中存储的内容。 IBM Notes 中定义的安全性会自动反映在搜索体验中，这可确保用户只看到他们有权访问的内容。 最终，用户可以在单一位置找到他们需要的一切内容。

[更多详细信息](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

由 [BA Insight](https://www.bainsight.com/) 提供

M-Files 连接器支持对 M-Files 平台管理的内容编制索引，并支持完整爬网和增量爬网以确保及时刷新索引。

[更多详细信息](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 MediaPlatform PrimeTime 索引连接器，可以让用户组织的企业搜索平台来访问内容，同时将连接器与 BA Insight 的 SmartHub 相结合。 BA Insight MediaPlatform PrimeTime 连接器从 MediaPlatform PrimeTime 检索有关频道和视频的信息，然后通过 Azure 认知搜索对这些信息编制索引。

[更多详细信息](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 MediaWiki 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地为 MediaWiki 实例中的页面、讨论页和附件编制索引。 连接器完全支持 MediaWiki 的内置权限模型，还支持基于 Active Directory 和其他目录服务的 MediaWiki 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager (HPE Records Manager/HP TRIM)

由 [BA Insight](https://www.bainsight.com/) 提供

HP TRIM 连接器是专为 HP Records Manager 开发的，可建立到 Jive 应用程序的安全连接并将内容（包括元数据和附件）从 TRIM 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

由 [BA Insight](https://www.bainsight.com/) 提供

我们的 Microsoft Dynamics 365 CRM 连接器支持本地 CRM 安装和 Dynamics CRM Online。

[更多详细信息](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365 (Cloud)

由 [BA Insight](https://www.bainsight.com/) 提供

我们的 Microsoft Dynamics 365（云版）CRM 连接器与 CRM 应用程序建立安全连接，并将内容从 CRM 架构映射到搜索引擎架构。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

由 [BA Insight](https://www.bainsight.com/) 提供

使用 BA Insight Microsoft Exchange Online 连接器，用户可以通过各种搜索平台 Exchange Online 中检索内容。

[更多详细信息](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Microsoft Exchange Public Folders

由 [BA Insight](https://www.bainsight.com/) 提供

使用 BA Insight Microsoft Exchange Online 连接器，用户可以通过各种搜索平台从 Exchange 中检索内容。

[更多详细信息](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

由 [BA Insight](https://www.bainsight.com/) 提供

使用 BA Insight Microsoft Exchange 连接器，用户可以通过各种搜索引擎从 Exchange 中检索内容。

[更多详细信息](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

由 [BA Insight](https://www.bainsight.com/) 提供

数据库连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Oracle、MySQL 和 IBM DB2 等其他系统的数据库。 该连接器会遵循源数据库的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight Microsoft Teams Connector 对 Microsoft Teams 中的内容以及其他企业系统中的内容编制索引，以提供统一结果。

[更多详细信息](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Microsoft Windows File Server

由 [Raytion](https://www.raytion.com/contact) 提供

安全的企业搜索连接器，能够可靠地对 Microsoft Windows 文件服务器（包括其分布式文件系统 (DFS)）的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Windows File Server 中的文件和文件夹编制索引。 该连接器完全支持 Microsoft Windows 文件服务器的文档级安全性以及最新版本的 SMB2 和 SMB3 协议。

[更多详细信息](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

由 [BA Insight](https://www.bainsight.com/) 提供

MySQL 连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Oracle、MySQL 和 IBM DB2 等其他系统的数据库。 该连接器会遵循源数据库的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

由 [BA Insight](https://www.bainsight.com/) 提供

NetDocuments 连接器对 NetDocs 中存储的内容编制索引，以便用户可以直接从他们的门户中搜索和检索 NetDocuments 内容。 该连接器自动将 NetDocs 中的文档安全性应用于 Azure 认知搜索，因此用户信息仍然是安全的。 NetDocuments 中存储的元数据可以映射到等效的术语，这样用户便可以获得无缝搜索体验。

[更多详细信息](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>Neudesic The Firm Directory

由 [BA Insight](https://www.bainsight.com/) 提供

The Firm Directory 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>说明

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 IBM Notes（以前的 Lotus Note）中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对一组可配置的 Notes 数据库中的记录编制索引。 该连接器完全支持 IBM Notes 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

由 [BA Insight](https://www.bainsight.com/) 提供

Nuxeo 连接器允许组织将他们的 Nuxeo 内容（包括安全信息以及内容上设置的标准和自定义元数据）与 Office 365 中存在的内容一起索引到 Azure 认知搜索中。 最终，用户可以在单一位置找到他们需要的一切内容。

[更多详细信息](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>目标

由 [BA Insight](https://www.bainsight.com/) 提供

Objective 连接器身份认证器专为 Objective 开发的，能够与 Objective 建立安全连接，并将内容（包括元数据）从 Objective 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

由 [Accenture](https://www.accenture.com) 提供

OneDrive 连接器将对 Microsoft OneDrive 中的内容爬网，能够进行增量爬网，包括和排除基于正则表达式模式的项目、元数据提取、检索多种类型的文档。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive for Business

由 [BA Insight](https://www.bainsight.com/) 提供

使用 BA Insight OneDrive 连接器，可以将 OneDrive 中的内容索引到各种搜索平台，为用户提供来自多个来源的集成搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText 内容服务器

由 [BA Insight](https://www.bainsight.com/) 提供

连接器以与本机门户内容大致相同的方式为 Content Server 内容编制索引，支持完全爬网和增量爬网。 Content Server 中定义的安全性会自动反映在搜索体验中，这可确保用户只看到他们有权访问的内容。

[更多详细信息](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText 内容服务器

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 OpenText Content Server 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地为 Content Server 实例中的文件、文件夹、虚拟文件夹、复合文档、新闻、电子邮件、卷、集合、分类和更多对象编制索引。 该连接器完全支持 OpenText Content Server 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum (Cloud)

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 OpenText Documentum Cloud 连接器以安全方式在将 Documentum 对象的全文和元数据索引到搜索引擎中，从而实现跨多个存储库的内容的单一可搜索结果集。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 OpenText Documentum eRoom 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Documentum eRoom 中的存储库、文件夹和文件及其元数据和属性编制索引。 该连接器完全支持 OpenText Documentum eRoom 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

由 [BA Insight](https://www.bainsight.com/) 提供

OpenText eDOCS DM 连接器的用户可以直接从 Azure 认知搜索中搜索 eDOCS 存储库中的内容，无需执行多次搜索来定位所需的内容。 在 eDOCS 中建立的安全性由连接器维护，以确保内容只有被授予访问权限的人才能看到。

[更多详细信息](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Oracle 数据库

由 [BA Insight](https://www.bainsight.com/) 提供

Oracle Database 连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Microsoft SQL Server、MySQL 和 IBM DB2 等其他系统的数据库。

[更多详细信息](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

由 [BA Insight](https://www.bainsight.com/) 提供

WebCenter 连接器将 WebCenter 与 Azure 认知搜索集成在一起，使整个组织的用户可以更轻松地查找存储在 WebCenter 中的重要信息，无需直接登录并进行单独搜索。

[更多详细信息](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

由 [Raytion](https://www.raytion.com/contact) 提供

安全的企业搜索连接器，能够可靠地对 Oracle Knowledge Advanced (KA) Cloud 中的内容编制索引，并使用 Azure 认知搜索智能搜索。 该连接器以近乎实时，可靠地对 Oracle KA Cloud 中的页面和附件编制索引。 该连接器完全支持 Oracle KA Cloud 的内置用户和组管理。 特别是，该连接器可处理 Oracle KA Cloud 页面中基于片段的权限。

[更多详细信息](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content (UCM/Stellent)

由 [BA Insight](https://www.bainsight.com/) 提供

WebCenter Content 连接器完全支持 Azure 认知搜索可用的所有内容的底层安全性，并通过计划的爬网使这些内容保持最新，确保用户在进行搜索时获得最新的更新。

[更多详细信息](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 pirobase CMS 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 pirobase CMS 中的页面、附件和其他生成的文档类型编制索引。 该连接器完全支持 pirobase CMS 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 PostgreSQL 连接器会遵循源数据库的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。 该连接器将 PostgreSQL 中的内容索引到 Azure 认知搜索，并通过 BA Insight 的 SmartHub 呈现内容，为用户提供集成的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 Practical Law 连接器使用户能够对 Practical Law 数据库执行搜索，无需直接导航到 Practical Law。

[更多详细信息](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

由 [BA Insight](https://www.bainsight.com/) 提供

适用于 Pro Law 的 BA Insight 连接器可将任何门户连接到 ProLaw，能够显示 ProLaw 中的信息，同时遵循 ProLaw 中的用户特权。

[更多详细信息](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RDB via Snapshots

由 [Accenture](https://www.accenture.com) 提供

RDB via Snapshots 连接器将对可以使用 JDBC 访问的关系数据库的内容进行爬网，并且使用快照数据库执行增量爬网。 该连接器直接基于语句提取 SQL 数据。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

由 [Accenture](https://www.accenture.com) 提供

RDB via Tables 连接器将对可以使用 JDBC 访问的任何关系数据库中的内容进行爬网，并且将执行增量爬网，从而使用表（其中保存更新内容的标识符）来提取更新。 该连接器直接基于语句提取 SQL 数据。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

由 [Accenture](https://www.accenture.com) 提供

Amazon S3 连接器对任何 Amazon Simple Storage Service 中的内容进行爬网。 该连接器将执行增量爬网，按照 S3 文档级别安全性提取对象 ACL，并将包括存储在存储桶、文件夹和子文件夹中的文档。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

由 [Accenture](https://www.accenture.com) 提供

Salesforce 连接器将对 Salesforce 存储库中的内容进行爬网。 该连接器支持 Salesforce 知识库和 Chatter、元数据和自定义元数据检索、执行完整或增量爬网、提取 ACL、可选择的元素类型和组扩展。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

由 [BA Insight](https://www.bainsight.com/) 提供

Salesforce 连接器将 Salesforce 的服务、销售和市场营销云与 Azure 认知搜索集成，这样所有员工均可通过此门户来访问 Salesforce 中的所有内容。

[更多详细信息](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Salesforce 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Salesforce 中的客户、聊天消息、个人资料、潜在客户、案例和所有其他记录对象编制索引。 该连接器完全支持 Salesforce 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>SAP ERP

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 SAP ERP 连接器用途是将 SAP 中的项引入到搜索索引中。 这反过来会点亮 UI，可以对 SAP、SharePoint 和其他系统中的信息进行统一查看。

[更多详细信息](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP (Cloud)

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 SAP ERP（Cloud 版本）连接器用途是将 SAP 中的项引入到搜索索引中。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

由 [BA Insight](https://www.bainsight.com/) 提供

SAP HANA 连接器会遵循源数据库的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。 该连接器将 SAP HANA 中的内容索引到 Azure 认知搜索，并通过 BA Insight 的 SmartHub 呈现内容，为用户提供集成的搜索结果。

[更多详细信息](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA (Cloud)

由 [BA Insight](https://www.bainsight.com/) 提供

SAP HANA（Cloud 版本）连接器遵循源数据库的安全性，并提供完整爬网和增量爬网，使用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver 门户

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 SAP NetWeaver Portal (NWP) 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 SAP NWP、其知识和管理协作 (KMC)和门户内容目录 (PCD) 区域中的页面、附件和其他文档类型编制索引。 该连接器完全支持 SAP NetWeaver Portal 的内置用户和组管理，也完全支持基于 Active Directory 和其他目录服务的 SAP NWP 安装。

[更多详细信息](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 SAP PLM DMS 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 SAP PLM DMS 中的文档、附件和其他记录编制索引。

[更多详细信息](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

由 [Accenture](https://www.accenture.com) 提供

Selenium 连接器将使用 Internet 浏览器从网站中的内容进行爬网，以检索多种类型的文档，如网页、站点地图、二进制文档等。 该连接器可避免框架（如 Angular 和 React）的兼容性问题。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

由 [Accenture](https://www.accenture.com) 提供

ServiceNow 连接器从 ServiceNow 存储库中检索多种类型的文档，例如知识文章、目录项和附件。 该连接器还会检索安全 ACL 并执行组扩展。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

由 [BA Insight](https://www.bainsight.com/) 提供

 ServiceNow 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 ServiceNow 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 ServiceNow 中的问题、任务、附件、知识管理文章、页面等内容编制索引。 该连接器支持 ServiceNow 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Microsoft SharePoint 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 SharePoint 本地实例中的站点、Web、现代页面（SharePoint 2016 及更高版本）和经典页面、维基页面、OneNote 文档、列表项、任务、日历项、附件和文件编制索引。 该连接器完全支持 Microsoft SharePoint 内置的用户和组管理，还支持 Active Directory 以及 SiteMinder 和 Okta 等 OAuth 提供程序。 该连接器原生支持基本、NTLM 和 Kerberos 身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 SharePoint 2010 连接器，你可以连接到 SharePoint 2010，从任何站点、文档库或列表提取数据；并安全地为此内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

由 [Accenture](https://www.accenture.com) 提供

2013 SharePoint 连接器将对任何 2013 SharePoint 站点集 URL 中的内容进行爬网。 该连接器使用 SharePoint 的更改日志时间戳或快照数据库执行增量爬网，可获取 ACL，而且支持 NTLM 和 HTTPS、BCS 外部列表，并且无需在 SharePoint 上安装任何软件即可运行。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 SharePoint 2013 连接器，你可以连接到 SharePoint 2013，从任何站点、文档库或列表提取数据；并安全地为此内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

由 [Accenture](https://www.accenture.com) 提供

2016 SharePoint 连接器将对任何 2016 SharePoint 站点集 URL 中的内容进行爬网。 该连接器使用 SharePoint 的更改日志时间戳或快照数据库执行增量爬网，可获取 ACL，而且支持 NTLM 和 HTTPS、BCS 外部列表，并且无需在 SharePoint 上安装任何软件即可运行。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 SharePoint 连接器，你可以连接到 SharePoint 2016，从任何站点、文档库或列表提取数据；并安全地为此内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 SharePoint 连接器，你可以连接到 SharePoint 2019，从任何站点、文档库或列表提取数据；并安全地为此内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

由 [Accenture](https://www.accenture.com) 提供

SharePoint Online 连接器将对任何 SharePoint Online 站点集合 URL 中的内容进行爬网。 连接器将检索站点、列表、文件夹、列表项和附件，以及其他页面（.aspx 格式）。 支持在 Microsoft O365 产品中运行的 SharePoint。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

由 [BA Insight](https://www.bainsight.com/) 提供

借助 BA Insight 的 SharePoint Online 连接器，你可以连接到 SharePoint Online，从任何站点、文档库或列表提取数据；并安全地为此内容编制索引。

[更多详细信息](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

由 [BA Insight](https://www.bainsight.com/) 提供

Sitecore 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Sitecore 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对页面、附件和其他生成的文档类型编制索引。 连接器完全支持 Sitecore 的权限模型以及关联 Active Directory 中的用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Slack 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Slack 的所有公共渠道中的消息、线程和共享文件编制索引。

[更多详细信息](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

由 [Accenture](https://www.accenture.com) 提供

SMB 连接器跨共享驱动器检索文件和目录。 该连接器支持分布式文件系统，可以检索安全信息，并且可以在不更改上次访问日期的情况下访问文档以编制索引。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>SMB 文件共享

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 SMB 文件共享中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对文件共享中的文件和文件夹编制索引。 该连接器完全支持 SMB 的文档级安全性以及最新版本的 SMB2 和 SMB3 协议。

[更多详细信息](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>SQL 数据库

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 SQL 数据库（如 Microsoft SQL Server 或 Oracle）中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 SQL 数据库中的记录和字段（包括二进制文档）编制索引。 该连接器支持实施自定义文档级别安全模型。

[更多详细信息](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>任何基于 SQL 的 CRM 系统

由 [BA Insight](https://www.bainsight.com/) 提供

SQL Server 连接器是基于行业标准数据库访问方法构建的，因此它同样支持 Oracle、MySQL 和 IBM DB2 等其他系统的数据库。 该连接器会遵循源数据库的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Symantec Enterprise Vault 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Enterprise Vault 中的电子邮件、附件、文件、日历项目和联系人编制索引。 该连接器完全支持 Symantec Enterprise Vault 的身份验证模型，包括基本、NTLM 和 Kerberos 身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

由 [Accenture](https://www.accenture.com) 提供

Twitter 连接器将对任何 Twitter 帐户中的内容进行爬网。 该连接器执行完整爬网和增量爬网，并支持使用 Twitter 用户、使用者密钥和使用者机密进行身份验证。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight 的 Veeva Vault 连接器将 Veeva Vault 对象的全文和元数据安全地索引到 Azure 认知搜索。 这使用户能够检索 Veeva Vault 和 Microsoft 365 中内容的单个结果集。

[更多详细信息](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault (Symantec eVault)

由 [BA Insight](https://www.bainsight.com/) 提供

Veritas Enterprise Vault 连接器会遵循源系统的安全性，同时提供完整爬网和增量爬网，因此用户可随时获得最新的信息。

[更多详细信息](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Veritas Enterprise Vault 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Enterprise Vault 中的电子邮件、附件、文件、日历项目和联系人编制索引。 该连接器完全支持 Veritas Enterprise Vault 的身份验证模型，包括基本、NTLM 和 Kerberos 身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>网站爬网程序

由 [BA Insight](https://www.bainsight.com/) 提供

通过 BA Insight 网站爬网程序连接器，可以将任何网站中的内容以及其他存储库中的内容合并到单个整合搜索索引中。

[更多详细信息](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

由 [BA Insight](https://www.bainsight.com/) 提供

BA Insight West km 连接器支持跨交易和诉讼文档进行搜索，包括创建自定义搜索结果页。

[更多详细信息](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 windream ECM-System 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地为通过 windream ECM-System 关联的全面元数据编制索引。 连接器完全支持 windream ECM-System 的权限模型以及关联 Active Directory 中的用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

由 [BA Insight](https://www.bainsight.com/) 提供

直接从 Azure 认知搜索中搜索 Docushare 存储库中的内容，无需执行多次搜索来定位所需的内容。

[更多详细信息](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Xerox DocuShare 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 DocuShare 中的数据存储库、文件夹、配置文件、组和文件编制索引。 该连接器完全支持 Xerox DocuShare 的内置用户和组管理。

[更多详细信息](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

由 [Accenture](https://www.accenture.com) 提供

Yammer 连接器将对 Yammer 消息内容进行爬网。 该连接器将按组、会话或主题检索消息，并将获取用户、组和线程详细信息。

[更多详细信息](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

由 [BA Insight](https://www.bainsight.com/) 提供

Yammer 连接器建立到 Yammer 应用程序的安全连接，并将内容（包括元数据和附件）从 Yammer 架构映射到搜索引擎架构。 该连接器随后将提取内容，并将其馈送给进程中名为爬网的搜索引擎。

[更多详细信息](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

由 [Raytion](https://www.raytion.com/contact) 提供

安全企业搜索连接器，用于可靠地对 Microsoft Yammer 中的内容编制索引，并使用 Azure 认知搜索对其进行智能搜索。 该连接器以近乎实时的方式，可靠地对 Yammer 中的频道、帖子、回复、附件、投票和公告编制索引。 该连接器完全支持 Microsoft Yammer 的内置用户和组管理，特别是基于 Microsoft 365 的联合身份验证。

[更多详细信息](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::
