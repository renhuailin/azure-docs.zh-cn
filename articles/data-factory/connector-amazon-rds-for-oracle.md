---
title: 从 Amazon RDS for Oracle 复制数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用数据工厂或 Azure Synapse Analytics 管道将数据从 Amazon RDS for Oracle 复制到受支持的接收器存储。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: jianleishen
ms.openlocfilehash: c6f14b2bb5129907aa5efac042f23217791d84d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594335"
---
# <a name="copy-data-from-amazon-rds-for-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 从 Amazon RDS for Oracle 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何在 Azure 数据工厂中使用复制活动从 Amazon RDS for Oracle 数据库复制数据。 本文是在[复制活动概述](copy-activity-overview.md)的基础上编写的。

## <a name="supported-capabilities"></a>支持的功能

此 Amazon RDS for Oracle 连接器支持以下活动：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Amazon RDS for Oracle 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Amazon RDS for Oracle 连接器支持：

- 以下版本的 Amazon RDS for Oracle 数据库：
    - Amazon RDS for Oracle 19c R1 (19.1) 和更高版本
    - Amazon RDS for Oracle 18c R1 (18.1) 和更高版本
    - Amazon RDS for Oracle 12c R1 (12.1) 和更高版本
    - Amazon RDS for Oracle 11g R1 (11.1) 和更高版本
- 从 Amazon RDS for Oracle 源进行并行复制。 有关详细信息，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。

> [!Note]
> 不支持 Amazon RDS for Oracle 代理服务器。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

集成运行时提供内置的 Amazon RDS for Oracle 驱动程序。 因此，在从 Amazon RDS for Oracle 复制数据时不需要手动安装驱动程序。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-rds-for-oracle-using-ui"></a>使用 UI 创建到 Amazon RDS for Oracle 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Amazon RDS for Oracle 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="屏幕截图，显示如何使用 Azure Synapse UI 创建新的链接服务。":::

1. 搜索 Amazon RDS for Oracle 并选择 Amazon RDS for Oracle 连接器。

    :::image type="content" source="media/connector-amazon-rds-for-oracle/amazon-rds-for-oracle-connector.png" alt-text="Amazon RDS for Oracle 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-amazon-rds-for-oracle/configure-amazon-rds-for-oracle-linked-service.png" alt-text="Amazon RDS for Oracle 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Amazon RDS for Oracle 连接器的实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Amazon RDS for Oracle 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AmazonRdsForOracle。 | 是 |
| connectionString | 指定连接到 Amazon RDS for Oracle 数据库实例所需的信息。 <br/>还可以将密码放在 Azure Key Vault 中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 <br><br>支持的连接类型：可以使用“Amazon RDS for Oracle SID”或 “Amazon RDS for Oracle 服务名称”来标识数据库：<br>- 如果使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果使用服务名称：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>对于高级 Amazon RDS for Oracle 本机连接选项，可以选择在 Amazon RDS for Oracle 服务器上的 [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) 文件中添加条目，在 Amazon RDS for Oracle 链接服务中，可以选择使用“Amazon RDS for Oracle 服务名称”连接类型并配置相应的服务名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

如果故障转移方案中有多个 Amazon RDS for Oracle 实例，可以创建 Amazon RDS for Oracle 链接服务并填写主要主机、端口、用户名、密码等，并添加新的“其他连接属性”，其属性名称为 `AlternateServers`，值为 `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)`，请勿遗漏括号，并注意使用冒号 (`:`) 作为分隔符。 例如，以下备用服务器值定义用于连接故障转移的两台备用数据库服务器：`(HostName=AccountingAmazonRdsForOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`。

可以根据自己的情况在连接字符串中设置更多连接属性：

| 属性 | 说明 | 允许的值 |
|:--- |:--- |:--- |
| ArraySize |连接器在单个网络往返中可以提取的字节数。 例如，`ArraySize=‭10485760‬`。<br/><br/>较大的值可减少在网络中提取数据的次数，从而提高吞吐量。 较小的值会增加响应时间，因为等待服务器传输数据的延迟较小。 | 1 到 4294967296 (4 GB) 之间的一个整数。 默认值为 `60000`。 值 1 不定义字节数，而指示仅为一行数据分配空间。 |

若要在 Amazon RDS for Oracle 连接上启用加密，你有两种选择：

-   若要使用“三重 DES 加密 (3DES) 和高级加密标准 (AES)”，在 Amazon RDS for Oracle 服务器端，转到“Oracle 高级安全性 (OAS)”并配置加密设置。 有关详细信息，请参阅 [Oracle 文档](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)。 Amazon RDS for Oracle 应用程序开发框架 (ADF) 连接器会自动协商加密方法，以便在建立与 Amazon RDS for Oracle 的连接时使用在 OAS 中配置的加密方法。

-   若要使用 TLS  ，请执行以下操作：

    1.  获取 TLS/SSL 证书信息。 获取 TLS/SSL 证书的可辨别编码规则 (DER) 编码证书信息，并将输出 (----- Begin Certificate … End Certificate -----) 另存为文本文件。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **示例：** 从 DERcert.cer 提取证书信息；然后，将输出保存到 cert.txt。

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  生成 `keystore` 或 `truststore`。 以下命令使用或不使用 PKCS-12 格式的密码来创建 `truststore` 文件。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **示例：** 使用密码创建名为 MyTrustStoreFile 的 PKCS12 `truststore` 文件。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  将 `truststore` 文件放在自承载 IR 计算机上。 例如，将该文件放在 C:\MyTrustStoreFile。
    4.  在服务中，使用 `EncryptionMethod=1` 和相应的 `TrustStore`/`TrustStorePassword` 值配置 Amazon RDS for Oracle 连接字符串。 例如，`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`。

**示例：**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>数据集属性

本部分提供 Amazon RDS for Oracle 数据集支持的属性列表。 有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 

若要从 Amazon RDS for Oracle 复制数据，请将数据集的 type 属性设置为 `AmazonRdsForOracleTable`。 支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 `AmazonRdsForOracleTable`。 | 是 |
| schema | 架构的名称。 | 否 |
| 表 | 表/视图的名称。 | 否 |
| tableName | 具有架构的表/视图的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否 |

**示例：**

```json
{
    "name": "AmazonRdsForOracleDataset",
    "properties":
    {
        "type": "AmazonRdsForOracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Amazon RDS for Oracle 源支持的属性列表。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="amazon-rds-for-oracle-as-source"></a>Amazon RDS for Oracle 作为源

>[!TIP]
>若要使用数据分区从 Amazon RDS for Oracle 高效加载数据，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)了解详细信息。

要从 Amazon RDS for Oracle 复制数据，请将复制活动中的源类型设置为 `AmazonRdsForOracleSource`。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为 `AmazonRdsForOracleSource`。 | 是 |
| oracleReaderQuery | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何相应的内置分区参数。 例如，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。 | 否 |
| partitionOptions | 指定用于从 Amazon RDS for Oracle 加载数据的数据分区选项。 <br>允许值包括：None（默认值）、PhysicalPartitionsOfTable 和 DynamicRange  。<br>启用分区选项（即该选项不为 `None`）时，从 Amazon RDS for Oracle 数据库并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| partitionNames | 需要复制的物理分区的列表。 <br>当分区选项是 `PhysicalPartitionsOfTable` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfTabularPartitionName`。 例如，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。 | 否 |
| partitionColumnName | 指定并行复制范围分区使用的源列（**整数类型**）的名称。 如果未指定，系统会自动检测表的主键并将其用作分区列。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionColumnName`。 例如，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。 | 否 |
| partitionUpperBound | 要从中复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 例如，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。 | 否 |
| partitionLowerBound | 要从中复制数据的分区列的最小值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 例如，请参阅[从 Amazon RDS for Oracle 进行并行复制](#parallel-copy-from-amazon-rds-for-oracle)部分。 | 否 |

**示例：使用基本查询但不使用分区复制数据**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRdsForOracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-amazon-rds-for-oracle"></a>从 Amazon RDS for Oracle 进行并行复制

Amazon RDS for Oracle 连接器提供内置的数据分区，用于从 Amazon RDS for Oracle 并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

:::image type="content" source="./media/connector-amazon-rds-for-oracle/connector-amazon-rds-for-oracle-partition-options.png" alt-text="分区选项的屏幕截图。":::

启用分区复制后，服务将对 Amazon RDS for Oracle 源运行并行查询，按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Amazon RDS for Oracle 数据库检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Amazon RDS for Oracle 数据库加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。          | **分区选项**：表的物理分区。 <br><br/>在执行期间，该服务将自动检测物理分区并按分区复制数据。 |
| 从不包含物理分区但包含用于数据分区的整数列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**：指定用于对数据进行分区的列。 如果未指定，将使用主键列。 |
| 使用自定义查询从包含物理分区的表加载大量数据。 | **分区选项**：表的物理分区。<br>**查询**：`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**分区名称**：指定要从中复制数据的分区名称。 如果没有指定，服务将自动检测在 Amazon RDS for Oracle 数据集中指定的表的物理分区。<br><br>在执行期间，服务会将 `?AdfTabularPartitionName` 替换为实际分区名称，并发送到 Amazon RDS for Oracle。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以针对整数数据类型的列进行分区。<br>**分区上限** 和 **分区下限**：指定是否要对分区列进行筛选，以便仅检索介于下限和上限之间的数据。<br><br>在执行期间，服务会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound` 和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名称和值范围，并发送到 Amazon RDS for Oracle。 <br>例如，如果为分区列“ID”设置了下限 1、上限 80，并将并行复制设置为 4，则服务会按 4 个分区检索数据。 其 ID 分别介于 [1, 20]、[21, 40]、[41, 60] 和 [61, 80] 之间。 |

> [!TIP]
> 从非分区表复制数据时，可以使用“动态范围”分区选项针对整数列进行分区。 如果源数据没有这种类型的列，则可以利用源查询中的 [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) 函数来生成列，并将其用作分区列。

**示例：使用物理分区进行查询**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
