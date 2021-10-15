---
title: 将外部 Hive 元存储用于 Azure Synapse Spark 池
description: 了解如何为 Azure Synapse Spark 池设置外部 Hive 元存储。
keywords: 外部 Hive 元存储, 共享, Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 805987eb38df3979904fc8b9f3bebfc7fdb1fdca
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276371"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>将外部 Hive 元存储用于 Synapse Spark 池（预览版）

Azure Synapse Analytics 允许同一工作区中的 Apache Spark 池共享一个托管 HMS（Hive 元存储服务）兼容的元存储作为其目录。 如果客户需要在工作区外部保留 Hive 目录，并与工作区外部的其他计算引擎（例如 HDInsight 和 Azure Databricks）共享目录对象，他们可以连接到外部 Hive 元存储。 本文介绍了如何将 Synapse Spark 连接到外部 Apache Hive 元存储。 

## <a name="supported-hive-metastore-versions"></a>支持的 Hive 元存储版本

此功能对 Spark 2.4 和 Spark 3.0 都适用。 下表显示了每个 Spark 版本支持的 Hive 元存储服务 (HMS) 版本。


|Spark 版本|HMS 1.2.X|HMS 2.1.X|HMS 2.3.x|HMS 3.1.X|
|--|--|--|--|--|
|2.4|是|是|是|否|
|3|是|是|是|是|

## <a name="set-up-hive-metastore-linked-service"></a>设置 Hive 元存储链接服务

> [!NOTE]
> 仅支持 Azure SQL 数据库作为外部 Hive 元存储。

按照以下步骤在 Synapse 工作区中设置到外部 Hive 元存储的链接服务。

1. 打开 Synapse Studio，转到左侧的“管理”>“链接服务”，单击“新建”以创建新的链接服务。

   :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="设置 Hive 元存储链接服务" border="true":::

2. 选择“Azure SQL 数据库”，单击“继续”。

3. 提供链接服务的名称。 记录链接服务的名称，此信息将很快用于配置 Spark。

4. 可以从 Azure 订阅列表中为外部 Hive 元存储选择 Azure SQL 数据库，也可以手动输入信息。

5. 目前，我们仅支持 SQL 身份验证作为身份验证类型。 提供“用户名”和“密码”以设置连接。

6. 测试连接以验证用户名和密码。

7. 单击“创建”以创建链接服务。 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>测试连接并在笔记本中获取元存储版本
某些网络安全规则设置可能会阻止从 Spark 池访问外部 Hive 元存储数据库。 在配置 Spark 池之前，在任何 Spark 池笔记本中运行以下代码，测试与外部 Hive 元存储数据库的连接。 

此外，还可以从输出结果中获取 Hive 元存储版本。 Hive 元存储版本将用于 Spark 配置。

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>将 Spark 配置为使用外部 Hive 元存储
成功创建到外部 Hive 元存储的链接服务后，需要在 Spark 中设置一些配置以使用外部 Hive 元存储。 可以在 Spark 池级别或 Spark 会话级别设置配置。 

下面是配置和说明：

> [!NOTE]
> 默认 Hive 元存储版本为 2.3。 如果 Hive 元存储版本为 2.3，则无需设置 `spark.sql.hive.metastore.version` 和 `spark.sql.hive.metastore.jars`。 只需要 `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`。

|Spark 配置|说明|
|--|--|
|`spark.sql.hive.metastore.version`|支持的版本： <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> 确保使用前 2 个部分，而不使用第 3 部分|
|`spark.sql.hive.metastore.jars`|<ul><li>版本 1.2：`/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>版本 2.1：`/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>版本 3.1：`/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|已创建到 Azure SQL 数据库的链接服务的名称。|

### <a name="configure-spark-pool"></a>配置 Spark 池 
创建 Spark 池时，在“其他设置”选项卡下，将以下配置放入文本文件中，然后将其上传到“Apache Spark 配置”部分。 还可以使用现有 Spark 池的上下文菜单，选择“Apache Spark 配置”以添加这些配置。

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="配置 Spark 池":::

更新元存储版本和链接服务名称，并将以下配置保存在文本文件中以用于 Spark 池配置：

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

以下是元存储版本 2.1 的示例，其中为链接服务提供的名称是 HiveCatalog21：

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>配置 Spark 会话
如果你不需要配置 Spark 池，也可以使用 %%configure magic 命令在笔记本中配置 Spark 会话。 代码如下。 也可以将相同的配置应用于 Spark 批处理作业。 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>运行查询以验证连接
完成所有这些设置后，尝试在 Spark 笔记本中运行以下查询来列出目录对象，以检查与外部 Hive 元存储的连接。
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>设置存储连接
到 Hive 元存储数据库的链接服务仅提供对 Hive 目录元数据的访问。 要查询现有表，还需要设置与存储 Hive 表的基础数据的存储帐户的连接。

### <a name="set-up-connection-to-adls-gen-2"></a>设置与 ADLS Gen 2 的连接
#### <a name="workspace-primary-storage-account"></a>工作区主存储帐户
如果 Hive 表的基础数据存储在工作区主存储帐户中，则无需进行额外的设置。 只要你在工作区创建过程中遵循存储设置说明，它就会正常工作。

#### <a name="other-adls-gen-2-account"></a>其他 ADLS Gen 2 帐户
如果 Hive 目录的基础数据是存储在另一个 ADLS Gen 2 帐户中，则需要确保运行 Spark 查询的用户在 ADLS Gen2 存储帐户上具有“存储 Blob 数据参与者”角色。 

### <a name="set-up-connection-to-blob-storage"></a>设置与 Blob 存储的连接
如果 Hive 表的基础数据是存储在 Azure Blob 存储帐户中，请按照以下步骤设置连接：

1. 打开 Synapse Studio，转到“数据”>“链接”选项卡 >“添加”按钮 >“连接到外部数据”。

   :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="连接到存储帐户" border="true":::

2. 选择“Azure Blob 存储”，然后单击“继续”。
3. 提供链接服务的名称。 记录链接服务的名称，此信息将很快用于 Spark 会话配置。
4. 选择 Azure Blob 存储帐户。 确保身份验证方法是“帐户密钥”。 目前，Spark 池只能通过帐户密钥访问 Blob 存储帐户。
5. 测试连接，然后单击“创建”。
6. 创建到 Blob 存储帐户的链接服务后，当你运行 Spark 查询时，请确保在笔记本中运行以下 Spark 代码，以获取对 Spark 会话的 Blob 存储帐户的访问权限。 若要详细了解需要这样做的原因，请参阅[此处](./apache-spark-secure-credentials-with-tokenlibrary.md)。

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

设置存储连接后，可以查询 Hive 元存储中的现有表。

## <a name="known-limitations"></a>已知的限制

- Synapse Studio 对象资源管理器将继续显示托管的 Synapse 元存储（而不是外部 HMS）中的对象，我们正在改进这方面的体验。
- 使用外部 HMS 时，[SQL <-> Spark 同步](../sql/develop-storage-files-spark-tables.md)不起作用。  
- 仅支持 Azure SQL 数据库作为外部 Hive 元存储数据库。 仅支持 SQL 身份验证。
- 目前，Spark 只适用于外部 Hive 表和非过渡/非 ACID 托管的 Hive 表。 它目前不支持 Hive ACID/事务表。
- 目前还不支持 Apache Ranger 集成。

## <a name="troubleshooting"></a>疑难解答
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>使用存储在 Blob 存储中的数据查询 Hive 表时，看到以下错误
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

通过链接服务对存储帐户使用密钥身份验证时，需要执行额外的步骤来获取 Spark 会话的令牌。 在运行查询之前，运行以下代码来配置 Spark 会话。 在此处详细了解需要这样做的原因。

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>查询存储在 ADLS Gen2 帐户中的表时，看到以下错误
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

这可能是因为运行 Spark 查询的用户没有足够的权限来访问基础存储帐户。 确保运行 Spark 查询的用户在 ADLS Gen2 存储帐户中具有“存储 Blob 数据参与者”角色。 这一步可以在创建链接服务后完成。

### <a name="hms-schema-related-settings"></a>HMS 架构相关设置 
为了避免更改 HMS 后端架构/版本，系统默认设置以下 Hive 配置： 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

如果 HMS 版本为 1.2.1 或 1.2.2，则 Hive 中存在一个问题，如果将 spark.hadoop.hive.metastore.schema.verification 变成 true，则声明只需要 1.2.0。 我们的建议是，可以将 HMS 版本修改为 1.2.0，或者通过覆盖以下两个配置来解决问题：

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

如果需要迁移 HMS 版本，建议使用 [Hive 架构工具](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool)。 如果 HMS 已经被 HDInsight 集群使用，那么我们建议使用 [HDI 提供的版本](../../hdinsight/interactive-query/apache-hive-migrate-workloads.md)。 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>与 HDInsight 4.0 Spark 群集共享元存储时，我看不到表
如果要与 HDInsight 4.0 中的 Spark 群集共享 Hive 目录，请确保 Synapse Spark 中的属性 `spark.hadoop.metastore.catalog.default` 与 HDInsight Spark 中的值一致。 默认值为 `Spark`。

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>与 HDInsight 4.0 Hive 群集共享 Hive 元存储时，我可以成功列出表，但在查询表时只获得空结果
如限制中所述，Synapse Spark 池只支持外部 Hive 表和非事务/ACID 托管表，它目前不支持 Hive ACID/事务表。 默认情况下，在 HDInsight 4.0 Hive 群集中，所有托管表都默认被创建为 ACID/事务表，这就是你在查询这些表时获得空结果的原因。 