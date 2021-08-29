---
title: Azure SQL 和 SQL Server
description: 本文介绍如何使用连接器在 Azure MS SQL 与无服务器 Apache Spark 池之间移动数据。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 05/19/2020
ms.custom: has-adal-ref
ms.openlocfilehash: da64e27b5ae4675b313007c9b185db9194607f2c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428825"
---
# <a name="azure-sql-database-and-sql-server-connector-for-apache-spark"></a>适用于 Apache Spark 的 Azure SQL 数据库和 SQL Server 连接器
借助适用于 Azure SQL 数据库和 SQL Server 的 Apache Spark 连接器，这些数据库可以充当 Apache Spark 作业的输入数据源和输出数据接收器。 由此，可在大数据分析中使用实时事务数据，并保留临时查询或报告的结果。

与内置 JDBC 连接器相比，此连接器能够将数据批量插入 SQL 数据库。 它的性能可以比逐行插入快 10 倍到 20 倍。 适用于 SQL Server 和 Azure SQL 数据库的 Spark 连接器还支持 Azure Active Directory (Azure AD) [身份验证](/sql/connect/spark/connector#azure-active-directory-authentication)，让你可以从 Azure Synapse Analytics 安全地连接到 Azure SQL 数据库。 

本文介绍如何使用 DataFrame API 连接到使用 MS SQL 连接器的 SQL 数据库。 本文提供了使用 PySpark API 的详细示例。 有关使用 MS SQL 连接器连接到 SQL 数据库的所有受支持的参数和示例，请参阅 [Azure 数据 SQL 示例](https://github.com/microsoft/sql-server-samples#azure-data-sql-samples-repository)。


  
## <a name="connection-details"></a>连接详细信息
本示例将使用 Microsoft Spark 实用工具来帮助从预配置的 Key Vault 获取机密。 若要了解有关 Microsoft Spark 实用工具的详细信息，请访问 [Microsoft Spark 实用工具简介](../microsoft-spark-utilities.md)。

```python
servername = "<< server name >>"
dbname = "<< database name >>"
url = servername + ";" + "databaseName=" + dbname + ";"
dbtable = "<< table name >> "
user = "<< username >>" 
password = mssparkutils.credentials.getSecret('azure key vault name','secret name')
```

> [!NOTE]
> 目前，Azure SQL 连接器没有链接服务或 AAD 直通支持。

## <a name="use-the-azure-sql-and-sql-server-connector"></a>使用 Azure SQL 和 SQL Server 连接器

### <a name="read-data"></a>读取数据
```python
#Read from SQL table using MS SQL Connector
print("read data from SQL server table  ")
jdbcDF = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("user", user) \
        .option("password", password).load()

jdbcDF.show(5)
```

### <a name="write-data"></a>写入数据
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("overwrite") \
    .option("url", url) \
    .option("dbtable", dbtable) \
    .option("user", user) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("MSSQL Connector write failed", error)

print("MSSQL Connector write(overwrite) succeeded  ")
```
### <a name="append-data"></a>追加数据
```python
try:
  df.write \
    .format("com.microsoft.sqlserver.jdbc.spark") \
    .mode("append") \
    .option("url", url) \
    .option("dbtable", table_name) \
    .option("user", username) \
    .option("password", password) \
    .save()
except ValueError as error :
    print("Connector write failed", error)
```

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

### <a name="python-example-with-service-principal"></a>使用服务主体的 Python 示例
```python
import adal

# Located in App Registrations from Azure Portal
tenant_id = "<< tenant id >> "

# Located in App Registrations from Azure Portal
resource_app_id_url = "https://database.windows.net/"

# Authority
authority = "https://login.windows.net/" + tenant_id

context = adal.AuthenticationContext(authority)
token = context.acquire_token_with_client_credentials(resource_app_id_url, service_principal_id, service_principal_secret)
access_token = token["accessToken"]

jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", dbtable) \
        .option("accessToken", access_token) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

### <a name="python-example-with-active-directory-password"></a>使用 Active Directory 密码的 Python 示例
```python
jdbc_df = spark.read \
        .format("com.microsoft.sqlserver.jdbc.spark") \
        .option("url", url) \
        .option("dbtable", table_name) \
        .option("authentication", "ActiveDirectoryPassword") \
        .option("user", user_name) \
        .option("password", password) \
        .option("encrypt", "true") \
        .option("hostNameInCertificate", "*.database.windows.net") \
        .load()
```

## <a name="next-steps"></a>后续步骤
- [详细了解 SQL Server 和 Azure SQL 连接器](/sql/connect/spark/connector)
- [查看 Azure 数据 SQL 示例](https://github.com/microsoft/sql-server-samples)
