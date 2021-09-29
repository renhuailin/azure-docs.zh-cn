---
title: include 文件
description: include 文件
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 09/22/2021
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 75a08a3aee9a337e422611fd238fece98b9d09e5
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129095935"
---
| 类别 | 数据存储 | 支持用作源 | 支持用作接收器 | 受 [Azure IR](../concepts-integration-runtime.md#azure-integration-runtime) 支持 | 受[自承载 IR](../concepts-integration-runtime.md#self-hosted-integration-runtime) 支持 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob 存储](../connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 认知搜索索引](../connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB (SQL API)](../connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB 的 API for MongoDB](../connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 数据资源管理器](../connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Azure Data Lake Storage Gen1](../connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Storage Gen2](../connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database for MariaDB](../connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure Database for MySQL](../connector-azure-database-for-mysql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database for PostgreSQL](../connector-azure-database-for-postgresql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Databricks Delta Lake](../connector-azure-databricks-delta-lake.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Azure 文件](../connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL 数据库](../connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL 托管实例](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Synapse Analytics](../connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 表存储](../connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **Database** |[Amazon RDS for Oracle](../connector-amazon-rds-for-oracle.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon RDS for SQL Server](../connector-amazon-rds-for-sql-server.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon Redshift](../connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill](../connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum](../connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Hive](../connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala](../connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../connector-informix.md) |✓ |✓ | |✓  |
| &nbsp; |[MariaDB](../connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../connector-microsoft-access.md) |✓ |✓ | |✓  |
| &nbsp; |[MySQL](../connector-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Netezza](../connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle](../connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[PostgreSQL](../connector-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Presto](../connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[通过 Open Hub 实现的 SAP Business Warehouse](../connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  |
| &nbsp; |[通过 MDX 实现的 SAP Business Warehouse](../connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[SAP 表](../connector-sap-table.md) |✓ | | |✓  |
| &nbsp; |[Snowflake](../connector-snowflake.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Spark](../connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../connector-teradata.md) |✓ | |✓ |✓  |
| &nbsp; |[Vertica](../connector-vertica.md) |✓ | |✓ |✓  |
| **NoSQL** |[Cassandra](../connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase（预览）](../connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[MongoDB](../connector-mongodb.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[MongoDB Atlas](../connector-mongodb-atlas.md) |✓ |✓ |✓ |✓  |
| **文件** |[Amazon S3](../connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon S3 兼容存储](../connector-amazon-s3-compatible-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[文件系统](../connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[FTP](../connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[Google Cloud Storage](../connector-google-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle 云存储](../connector-oracle-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../connector-sftp.md) |✓ |✓ |✓ |✓  |
| **通用协议** |[泛型 HTTP](../connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[泛型 OData](../connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[泛型 ODBC](../connector-odbc.md) |✓ |✓ | |✓  |
| &nbsp; |[泛型 REST](../connector-rest.md) |✓ | ✓ |✓ |✓  |
| **服务和应用** |[Amazon Marketplace Web Service](../connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Concur（预览）](../connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dataverse](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics 365](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics AX](../connector-dynamics-ax.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics CRM](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Google AdWords](../connector-google-adwords.md) |✓ | |✓ |✓  |
| &nbsp; |[HubSpot](../connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira](../connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento（预览）](../connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo（预览）](../connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft 365](../connector-office-365.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua（预览）](../connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Responsys（预览）](../connector-oracle-responsys.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle 服务云（预览）](../connector-oracle-service-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[PayPal（预览）](../connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks（预览）](../connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce 服务云](../connector-salesforce-service-cloud.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce Marketing Cloud](../connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../connector-servicenow.md) |✓ | |✓ |✓  |
|  |[SharePoint Online 列表](../connector-sharepoint-online-list.md) |✓ | |✓ |✓ |
| &nbsp; |[Shopify（预览）](../connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square（预览）](../connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[Web 表（HTML 表）](../connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero](../connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho（预览）](../connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> 如果连接器标记为“预览”，可以试用它并给我们反馈。 若要在解决方案中使用预览版连接器的依赖项，请联系 [Azure 支持](https://azure.microsoft.com/support/)。
