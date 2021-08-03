---
title: Apache Atlas Spark 连接器中的元数据和世系
description: 本文介绍如何使用 Atlas Spark 连接器从 Spark 提取数据世系。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/28/2021
ms.openlocfilehash: aebe227c8f2a278131d38143c43c38ee4687e701
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579046"
---
# <a name="how-to-use-apache-atlas-connector-to-collect-spark-lineage"></a>如何使用 Apache Atlas 连接器收集 Spark 世系

Apache Atlas Spark 连接器是一个挂钩，用于跟踪 Spark SQL/数据帧数据移动，并将元数据更改推送到 Purview Atlas 终结点。 

## <a name="supported-scenarios"></a>支持的方案

此连接器支持以下跟踪：
1.  SQL DDL，如“CREATE/ALTER DATABASE”、“CREATE/ALTER TABLE”。
2.  SQL DML，如“CREATE TABLE HelloWorld AS SELECT”、“”INSERT INTO...”、“LOAD DATA [LOCAL] INPATH”、“INSERT OVERWRITE [LOCAL] DIRECTORY”等。
3.  包含输入和输出的数据帧移动。

此连接器依赖查询侦听器来检索查询及检查影响。 其可关联 Hive、HDFS 等其他系统，以跟踪 Atlas 中数据的生命周期。
由于 Purview 支持 Atlas API 和 Atlas 本机挂钩，因此该连接器可以在完成 Spark 配置后将世系报告给 Purview。 可以为每项作业配置此连接器，或将其配置为群集默认设置。 

## <a name="configuration-requirement"></a>配置要求

此连接器需要使用 Spark 2.4.0+ 版本， 但不支持 Spark 3。 Spark 支持设置三种类型的必需侦听器：  

| 侦听器 |    Spark 最低版本|
| ------------------- | ------------------- | 
| spark.extraListeners | 1.3.0 |
| spark.sql.queryExecutionListeners | 2.3.0 |
| spark.sql.streaming.streamingQueryListeners | 2.4.0 |

如果 Spark 群集版本低于 2.4.0，系统将不会捕获流查询世系和大多数查询世系。

### <a name="step-1-prepare-spark-atlas-connector-package"></a>步骤 1。 准备 Spark Atlas 连接器包
以下记录的操作步骤是以 DataBricks 为例：

1.  生成包
    1. 从 GitHub 请求代码： https://github.com/hortonworks-spark/spark-atlas-connector
    2. [适用于 Windows] 注释禁止使用 spark-atlas-connector\pom.xml 中的 maven-enforcer，以删除 Unix 上的依赖关系。

    ```web
    <requireOS>
        <family>unix</family>
    </requireOS>
    ```
    
    c. 在要生成的项目根中运行 mvn DskipTests 命令。 
    
    d. 从 ~\spark-atlas-connector-assembly\target\spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar 获取 jar
    
    e. 将包置于 spark 群集可访问的位置。 对于 DataBricks 群集，包可以上传到 dbfs 文件夹，例如 /FileStore/jars。

2. 准备连接器配置
    1. 从 Purview 帐户的 Azure 门户中获取 Kafka 终结点和凭据
        1. 为你的帐户提供“Purview 数据策展员”权限 

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png" alt-text="显示分配数据策展员角色的屏幕截图" lightbox="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png":::

        1. 终结点：从 Atlas Kafka 终结点主连接字符串获取。 终结点部件
        1. 凭据：整个 Atlas Kafka 终结点主连接字符串
        
        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png" alt-text="显示 atlas kafka 终结点的屏幕截图" lightbox="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png":::        
        
    1. 准备 atlas-application.properties 文件，替换 atlas.kafka.sasl.jaas.config 中的 atlas.kafka.bootstrap.servers 和密码值 

    ```script
    atlas.client.type=kafka
    atlas.kafka.sasl.mechanism=PLAIN
    atlas.kafka.security.protocol=SASL_SSL
    atlas.kafka.bootstrap.servers= atlas-46c097e6-899a-44aa-9a30-6ccd0b2a2a91.servicebus.windows.net:9093
    atlas.kafka.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="<connection string got from your Purview account>";
    ```
    
    c.  请确保 atlas 配置文件位于在[上面“步骤 1：生成包”部分](../purview/how-to-lineage-spark-atlas-connector.md#step-1-prepare-spark-atlas-connector-package)生成的驱动程序类路径中。 在群集模式下，将此配置文件传送到远程驱动器 --files atlas-application.properties


### <a name="step-2-prepare-your-purview-account"></a>步骤 2。 准备 Purview 帐户
成功创建 Atlas Spark 模型定义后，请按照以下步骤操作
1. 从 GitHub (https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json ) 获取 spark 类型定义

2. 分配角色：
    1. 导航到你的 Purview 帐户，然后选择“访问控制 (IAM)” 
    1. 添加用户并授予服务主体“Purview 数据源管理员”角色
3. 获取身份验证令牌：
    1. 打开 postman 或类似工具 
    1. 使用在上一步中使用的服务主体获取持有者令牌：
        * 终结点：https://login.windows.net/microsoft.com/oauth2/token
        * grant_type: client_credentials
        * client_id: {service principal ID}
        * client_secret: {service principal key}
        * 资源：`https://purview.azure.net`

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-examples.png" alt-text="显示 postman 示例的屏幕截图" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-examples.png":::      

4. 将 Spark Atlas 模型定义发布到 Purview 帐户：
    1.  从 Azure 门户的“属性”部分获取 Purview 帐户的 Atlas 终结点。
    1. 将 Spark 类型定义发布到 Purview 帐户：
       * 发布：{{endpoint}}/api/atlas/v2/types/typedefs
       * 使用生成的访问令牌 
       * 正文：从 GitHub (https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json ) 选中原始内容并复制所有内容

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png" alt-text="显示 postman 示例中类型定义的屏幕截图" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png":::

### <a name="step-3-prepare-spark-job"></a>步骤 3. 准备 Spark 作业
1. 正常编写 Spark 作业
2. 在 Spark 作业的源代码中添加连接器设置。 在下方所示代码中设置“atlas.conf”系统属性值，确保可以找到 atlas-application.properties 文件。

    System.setProperty("atlas.conf", "/dbfs/FileStore/jars/")

3. 生成 spark 作业源代码，以生成 jar 文件。 
4. 将 Spark 应用程序 jar 文件置于群集可以访问的位置。 例如，将 jar 文件存放在 "/dbfs/FileStore/jars/"DataBricks 中 

### <a name="step-4-prepare-to-run-job"></a>步骤 4. 准备运行作业
 
1. 以下说明适用于每个作业设置：若要捕获特定作业的世系，请使用 spark-submit 来启动包含其参数的作业。 

    在作业参数集中：
* 连接器 Jar 文件的路径。 
* 将 extraListeners、queryExecutionListeners、streamingQueryListeners 这三个侦听器用作连接器。 

| 侦听器 | 详细信息 |
| ------------------- | ------------------- | 
| spark.extraListeners  | com.hortonworks.spark.atlas.SparkAtlasEventTracker|
| spark.sql.queryExecutionListeners | com.hortonworks.spark.atlas.SparkAtlasEventTracker
| spark.sql.streaming.streamingQueryListeners | com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker |

* Spark 作业应用程序 Jar 文件的路径。

设置 Databricks 作业：关键在于，要使用 spark-submit 来运行已正确设置侦听器的作业。 在任务参数中设置侦听器信息。   

下面是 spark 作业的示例参数。

```script
["--jars","dbfs:/FileStore/jars/spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar ","--conf","spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker","--class","com.microsoft.SparkAtlasTest","dbfs:/FileStore/jars/08cde51d_34d8_4913_a930_46f376606d7f-sparkatlas_1_6_SNAPSHOT-17452.jar"]
```

下面是从命令行使用 spark-submit 的示例：

```script
spark-submit --class com.microsoft.SparkAtlasTest --master yarn --deploy-mode --files /data/atlas-application.properties --jars /data/ spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar 
--conf spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker
/data/worked/sparkApplication.jar
```

2. 以下说明适用于群集设置：应将连接器 jar 和侦听器设置置于 Spark 群集的 conf/spark-defaults.conf 中。 Spark-submit 将读取 Spark-submit  中的选项，并将其传递给应用程序。 
 
### <a name="step-5-run-and-check-lineage-in-purview-account"></a>步骤 5。 在 Purview 帐户中运行和检查世系
启动 Spark 作业，并在 Purview 帐户中检查世系信息。 

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png" alt-text="显示 purview 和 spark 世系的屏幕截图" lightbox="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png":::

## <a name="known-limitations-with-the-connector-for-spark-lineage"></a>Spark 世系用连接器的已知限制
1. 支持 SQL/数据帧 API（换言之，不支持 RDD）。 此连接器依赖查询侦听器来检索查询及检查影响。
    
2. 多个查询中的所有“inputs”和“outputs”将合并为单个“spark_process”实体。
    
    “spark_process”映射到 Spark 中的“applicationId”。 这将允许管理员跟踪在应用程序中发生的所有更改， 但也会导致“spark_process”中的世系/关系图更加复杂，且重要性降低。
3. 只跟踪流查询中的部分输入。

* Kafka 源支持通过模式订阅，此连接器不会枚举所有现有匹配主题，甚至所有可能的主题 
 
* “executed plan”提供包含（微）批读取和处理作业的实际主题。 因此，只有参与（微）批处理的输入会作为“spark_process”实体的“inputs”包含在其中。
    
4. 此连接器不支持列级世系。

5. 其不跟踪已删除的表（Spark 模型）。

    Spark 中的“drop table”事件仅提供 db 和表名称，因此你还需要更多信息才能创建识别该表的唯一密钥。

    此连接器依赖于通过读取 Spark 目录来获取表信息。 当此连接器注意到要删除表时，其实 Spark 已经删除了该表，因此删除表的操作不会产生任何结果。


## <a name="next-steps"></a>后续步骤

- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)
