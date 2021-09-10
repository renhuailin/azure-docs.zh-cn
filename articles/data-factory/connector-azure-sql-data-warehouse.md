---
title: 在 Azure Synapse Analytics 中复制和转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用数据工厂向/从 Azure Synapse Analytics 复制数据，以及在 Azure Synapse Analytics 中转换数据。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 2131e74935ee831925dbe307a79c26909078e575
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313966"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory-or-synapse-pipelines"></a>使用 Azure 数据工厂或 Synapse 管道在 Azure Synapse Analytics 中复制和转换数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
>
> - [版本 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [当前版本](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂或 Synapse 管道中的复制活动从/向 Azure Synapse Analytics 复制数据，以及如何使用数据流转换 Azure Data Lake Storage Gen2 中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Synapse Analytics 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)表的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure Synapse Analytics 连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。 还可选择从 Azure Synapse Analytics 源进行并行复制。有关详细信息，请参阅[从 Azure Synapse Analytics 进行并行复制](#parallel-copy-from-azure-synapse-analytics)部分。
- 作为接收器，使用 [COPY 语句](#use-copy-statement) 或 [PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics) 或者批量插入加载数据。 我们建议使用 COPY 语句或 PolyBase 以获得更好的复制性能。 连接器还支持使用 DISTRIBUTION = ROUND_ROBIN 基于源架构自动创建目标表（如果不存在）。

> [!IMPORTANT]
> 如果使用 Azure Integration Runtime 复制数据，请配置[服务器级防火墙规则](../azure-sql/database/firewall-configure.md)，确保 Azure 服务可以访问[逻辑 SQL 服务器](../azure-sql/database/logical-servers.md)。
> 如果使用自承载集成运行时复制数据，请将防火墙配置为允许合适的 IP 范围。 此范围包括用于连接 Azure Synapse Analytics 的计算机的 IP。
## <a name="get-started"></a>入门

> [!TIP]
> 若要实现最佳性能，请使用 PolyBase 或 COPY 语句将数据载入 Azure Synapse Analytics。 [使用 PolyBase 将数据加载到 Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) 和[使用 COPY 语句将数据加载到 Azure Synapse Analytics](#use-copy-statement) 部分包含详细信息。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据载入 Azure Synapse Analytics](load-azure-sql-data-warehouse.md)。

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-synapse-analytics-linked-service-using-ui"></a>使用 UI 创建 Azure Synapse Analytics 链接服务

使用以下步骤在 Azure 门户 UI 中创建 Azure Synapse Analytics 链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 Synapse 并选择 Azure Synapse Analytics 连接器。

    :::image type="content" source="media/connector-azure-sql-data-warehouse/azure-sql-data-warehouse-connector.png" alt-text="Azure Synapse Analytics 连接器的屏幕截图。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-azure-sql-data-warehouse/configure-azure-sql-data-warehouse-linked-service.png" alt-text="Azure Synapse Analytics 链接服务的配置屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

以下部分提供了有关定义数据工厂和 Synapse 管道实体的属性的详细信息（特定于 Azure Synapse Analytics 连接器）。

## <a name="linked-service-properties"></a>链接服务属性

Azure Synapse Analytics 链接服务支持以下属性：

| 属性            | 说明                                                  | 必需                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | type 属性必须设置为 **AzureSqlDW**。             | 是                                                          |
| connectionString    | 为 **connectionString** 属性指定连接到 Azure Synapse Analytics 实例所需的信息。 <br/>将此字段标记为 SecureString 以安全存储它。 还可以将密码/服务主体密钥放在 Azure 密钥保管库中，如果是 SQL 身份验证，则从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅表下方的 JSON 示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是                                                          |
| servicePrincipalId  | 指定应用程序的客户端 ID。                         | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| tenant              | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| azureCloudType | 对于服务主体身份验证，请指定 Azure AD 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为 `AzurePublic`、`AzureChina`、`AzureUsGovernment` 和 `AzureGermany`。 默认情况下，使用数据工厂或 Synapse 管道的云环境。 | 否 |
| connectVia          | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure 集成运行时或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否                                                           |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- Azure AD 应用程序令牌身份验证：[服务主体](#service-principal-authentication)
- Azure AD 应用程序令牌身份验证：[Azure 资源的托管标识](#managed-identity)

>[!TIP]
>在用户界面中创建 Azure Synapse“无服务器”SQL 池的链接服务时，请选择“手动输入”，而不是从订阅浏览。

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。

### <a name="sql-authentication"></a>SQL 身份验证

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 身份验证的链接服务示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure 密钥保管库中的密码：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>服务主体身份验证

若要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. 从 Azure 门户 **[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** 。 记下应用程序名称，以及以下定义链接服务的值：

   - 应用程序 ID
   - 应用程序密钥
   - 租户 ID

2. 为 Azure 门户中的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果授予包含托管标识的组管理员角色，则可跳过步骤 3 和步骤 4。 管理员将拥有对数据库的完全访问权限。

3. 为服务主体 **[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** 。 使用 SSMS 等工具连接到要从中复制数据或要将数据复制到其中的数据仓库，其 Azure AD 标识至少具有 ALTER ANY USER 权限。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your_application_name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户或其他用户所做的那样 **向服务主体授予所需的权限**。 运行以下代码，或者参考[此处](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)的更多选项。 如果要使用 PolyBase 加载数据，请了解[所需的数据库权限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. 在 Azure 数据工厂或 Synapse 工作区中配置 Azure Synapse Analytics 链接服务。

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 资源的托管标识身份验证

可将数据工厂或 Synapse 工作区与表示资源的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可将此托管标识用于 Azure Synapse Analytics 身份验证。 指定资源可使用此标识从/向数据仓库访问和复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 为 Azure 门户上的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果授予包含托管标识的组管理员角色，则可跳过步骤 3 和步骤 4。 管理员将拥有对数据库的完全访问权限。

2. 为托管标识[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SSMS 等工具连接到要从中复制数据或要将数据复制到其中的数据仓库，其 Azure AD 标识至少具有 ALTER ANY USER 权限。 运行以下 T-SQL。
  
    ```sql
    CREATE USER [your_resource_name] FROM EXTERNAL PROVIDER;
    ```

3. 授予托管标识所需的权限，就像通常为 SQL 用户和其他用户所做的那样。 运行以下代码，或者参考[此处](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)的更多选项。 如果要使用 PolyBase 加载数据，请了解[所需的数据库权限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your_resource_name];
    ```

4. **配置 Azure Synapse Analytics 链接服务**。

**示例：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。

Azure Synapse Analytics 数据集支持以下属性：

| 属性  | 说明                                                  | 必需                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 数据集的 **type** 属性必须设置为 **AzureSqlDWTable**。 | 是                         |
| 架构 | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

### <a name="dataset-properties-example"></a>数据集属性示例

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Synapse Analytics 源和接收器支持的属性列表。

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics 用作源

>[!TIP]
>若要使用数据分区从 Azure Synapse Analytics 高效加载数据，请参阅[从 Azure Synapse Analytics 进行并行复制](#parallel-copy-from-azure-synapse-analytics)以了解详细信息。

若要从 Azure Synapse Analytics 复制数据，请将复制活动源中的 **type** 属性设置为 **SqlDWSource**。 复制活动 **source** 节支持以下属性：

| 属性                     | 说明                                                  | 必需 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 复制活动源的 **type** 属性必须设置为 **SqlDWSource**。 | 是      |
| sqlReaderQuery               | 使用自定义 SQL 查询读取数据。 示例：`select * from MyTable`。 | 否       |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否       |
| storedProcedureParameters    | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否       |
| isolationLevel | 指定 SQL 源的事务锁定行为。 允许的值为：ReadCommitted、ReadUncommitted、RepeatableRead、Serializable、Snapshot    。 如果未指定，则使用数据库的默认隔离级别。 有关详细信息，请参阅 [system.data.isolationlevel](/dotnet/api/system.data.isolationlevel)。 | 否 |
| partitionOptions | 指定用于从 Azure Synapse Analytics 加载数据的数据分区选项。 <br>允许值包括：None（默认值）、PhysicalPartitionsOfTable 和 DynamicRange  。<br>启用分区选项（且选项不是 `None`）时，用于从 Azure Synapse Analytics 并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| 在 `partitionSettings` 下： | | |
| partitionColumnName | 以整数类型、日期类型或日期/时间类型（`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2` 或 `datetimeoffset`）指定源列的名称，范围分区将使用它进行并行复制。 如果未指定，系统会自动检测表的索引或主键并将其用作分区列。<br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfDynamicRangePartitionCondition `。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-azure-synapse-analytics)部分。 | 否 |
| partitionUpperBound | 分区范围拆分的分区列的最大值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。  <br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-azure-synapse-analytics)部分。 | 否 |
| partitionLowerBound | 分区范围拆分的分区列的最小值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。<br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-azure-synapse-analytics)部分。 | 否 |

请注意以下几点：

- 在源中使用存储过程检索数据时，请注意，如果存储过程旨在当传入不同的参数值时返回不同的架构，则从 UI 导入架构时，或通过自动创建表的功能将数据复制到 SQL 数据库时，可能会遇到故障或出现意外的结果。

#### <a name="example-using-sql-query"></a>示例：使用 SQL 查询

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="example-using-stored-procedure"></a>示例：使用存储过程

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="sample-stored-procedure"></a>示例存储过程：

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics 用作接收器

Azure 数据工厂和 Synapse 管道支持三种将数据加载到 Azure Synapse Analytics 的方法。

- [使用 COPY 语句](#use-copy-statement)
- [使用 PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics)
- 使用批量插入

加载数据最快且最具扩展性的方法是通过 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql) 或 [PolyBase](/sql/relational-databases/polybase/polybase-guide)。

要将数据复制到 Azure Synapse Analytics，请将复制活动中的接收器类型设置为 SqlDWSink。 复制活动 **sink** 节支持以下属性：

| 属性          | 说明                                                  | 必需                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 复制活动接收器的 **type** 属性必须设置为 **SqlDWSink**。 | 是                                           |
| allowPolyBase     | 指示是否使用 PolyBase 将数据加载到 Azure Synapse Analytics。 `allowCopyCommand` 和 `allowPolyBase` 不能同时为 true。 <br/><br/>有关约束和详细信息，请参阅[使用 PolyBase 将数据加载到 Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) 部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。 | 不是。<br/>使用 PolyBase 时适用。     |
| polyBaseSettings  | `allowPolybase` 属性设置为 **true** 时可以指定的一组属性。 | 不是。<br/>使用 PolyBase 时适用。 |
| allowCopyCommand | 指示是否使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql)将数据加载到 Azure Synapse Analytics。 `allowCopyCommand` 和 `allowPolyBase` 不能同时为 true。 <br/><br/>有关约束和详细信息，请参阅[使用 COPY 语句将数据加载到 Azure Synapse Analytics](#use-copy-statement) 部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。 | 不是。<br>使用 COPY 时适用。 |
| copyCommandSettings | `allowCopyCommand` 属性设置为 TRUE 时可以指定的一组属性。 | 不是。<br/>使用 COPY 时适用。 |
| writeBatchSize    | **每批** 要插入到 SQL 表中的行数。<br/><br/>允许的值为 **integer**（行数）。 默认情况下，该服务根据行大小动态确定适当的批大小。 | 否。<br/>使用批量插入时适用。     |
| writeBatchTimeout | 超时前等待批量插入操作完成的时间。<br/><br/>允许的值为 **timespan**。 示例："00:30:00"（30 分钟）。 | 不是。<br/>使用批量插入时适用。        |
| preCopyScript     | 指定在每次运行中将数据写入到 Azure Synapse Analytics 之前要由复制活动运行的 SQL 查询。 使用此属性清理预加载的数据。 | 否                                            |
| tableOption | 指定是否根据源架构[自动创建接收器表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 允许的值为：`none`（默认值）、`autoCreate`。 |否 |
| disableMetricsCollection | 该服务收集指标（如 Azure Synapse Analytics DWU），用于进行复制性能优化和提供建议，从而引入了额外的主数据库访问权限。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

#### <a name="azure-synapse-analytics-sink-example"></a>Azure Synapse Analytics 接收器示例

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="parallel-copy-from-azure-synapse-analytics"></a>从 Azure Synapse Analytics 进行并行复制

复制活动中的 Azure Synapse Analytics 连接器提供内置的数据分区，用于并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

![分区选项的屏幕截图](./media/connector-sql-server/connector-sql-partition-options.png)

启用分区复制时，复制活动将对 Azure Synapse Analytics 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Azure Synapse Analytics 检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Azure Synapse Analytics 加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。        | **分区选项**：表的物理分区。 <br><br/>在执行期间，该服务将自动检测物理分区并按分区复制数据。 <br><br/>若要检查表是否有物理分区，可参考[此查询](#sample-query-to-check-physical-partition)。 |
| 从不包含物理分区但包含用于数据分区的整数或日期时间列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**（可选）：指定用于对数据进行分区的列。 如果未指定，将使用索引或主键列。<br/>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，表中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测这些值。<br><br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数或日期/日期时间列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。<br>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，查询结果中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测该值。<br><br>在执行期间，该服务会将 `?AdfRangePartitionColumnName` 替换为每个分区的实际列名称和值范围，并发送到 Azure Synapse Analytics。 <br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 <br><br>下面是针对不同场景的更多示例查询：<br> 1.查询整个表： <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.使用列选择和附加的 where 子句筛选器从表中查询： <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.使用子查询进行查询： <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.在子查询中使用分区查询： <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

使用分区选项加载数据的最佳做法：

1. 选择独特的列作为分区列（如主键或唯一键），以避免数据倾斜。 
2. 如果表具有内置分区，请使用名为“表的物理分区”分区选项来提升性能。
3. 如果使用 Azure Integration Runtime 复制数据，则可设置较大的“[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units)”(>4) 以利用更多计算资源。 检查此处适用的方案。
4. “[复制并行度](copy-activity-performance-features.md#parallel-copy)”可控制分区数量，将此数字设置得太大有时会损害性能，建议将此数字设置按以下公式计算的值：（DIU 或自承载 IR 节点数）*（2 到 4）。
5. 请注意：Azure Synapse Analytics 一次最多可执行 32 个查询，将“复制并行度”设置得太大可能会导致 Synapse 限制问题。

**示例：从包含物理分区的大型表进行完整加载**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>检查物理分区的示例查询

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

如果表具有物理分区，则会看到“HasPartition”为“是”。

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> 使用 COPY 语句将数据加载到 Azure Synapse Analytics

使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql)是一种简单且灵活的方法，以高吞吐量将数据加载到 Azure Synapse Analytics。 若要了解更多详细信息，请查看[使用 COPY 语句大容量加载数据](../synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql.md)


- 如果源数据位于 Azure Blob 或 Azure Data Lake Storage Gen2 中，并且格式与 COPY 语句兼容，则可以使用复制活动直接调用 COPY 语句，使 Azure Synapse Analytics 从源中拉取数据 。 有关详细信息，请参阅 **[使用 COPY 语句直接复制](#direct-copy-by-using-copy-statement)** 。
- 如果 COPY 语句最初不支持源数据存储和格式，请改用 **[使用 COPY 语句暂存复制](#staged-copy-by-using-copy-statement)** 功能。 暂存复制功能也能提供更高的吞吐量。 它自动将数据转换为与 COPY 语句兼容的格式，将数据存储在 Azure Blob 存储中，然后调用 COPY 语句将数据加载到 Azure Synapse Analytics。

>[!TIP]
>在 Azure Integration Runtime 中使用 COPY 语句时，有效的[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units) 数始终为 2。 优化 DIU 不会影响性能，因为从存储加载数据的功能由 Azure Synapse 引擎提供支持。

### <a name="direct-copy-by-using-copy-statement"></a>使用 COPY 语句直接复制

Azure Synapse Analytics COPY 语句直接支持 Azure Blob、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2。 如果源数据满足本部分所述的条件，请使用 COPY 语句从源数据存储直接复制到 Azure Synapse Analytics。 否则，请使用[使用 COPY 语句的暂存复制](#staged-copy-by-using-copy-statement)。 该服务会检查设置，如果不满足条件，复制活动运行将会失败。

1. **源链接服务和格式** 使用以下类型和身份验证方法：

    | 支持的源数据存储类型                             | 支持的格式           | 支持的源身份验证类型                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [带分隔符的文本](format-delimited-text.md)             | 帐户密钥身份验证、共享访问签名身份验证、服务主体身份验证、托管标识身份验证 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 帐户密钥身份验证、共享访问签名身份验证 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 帐户密钥身份验证、共享访问签名身份验证 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [带分隔符的文本](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | 帐户密钥身份验证、服务主体身份验证、托管标识身份验证 |

    >[!IMPORTANT]
    >- 对存储链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
    >- 如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。

2. 格式设置如下：

   1. 对于“Parquet”：`compression` 可以为“无压缩”、“Snappy”或 **``GZip``** 。
   2. 对于“ORC”：`compression` 可以是“无压缩”、“```zlib```”或“Snappy”。
   3. 对于带分隔符的文本：
      1. `rowDelimiter` 显式设置为 **单字符** 或“\r\n”，不支持默认值。
      2. `nullValue` 保留默认值或设置为 **空字符串** ("")。
      3. `encodingName` 保留默认值或设置为 **utf-8 或 utf-16**。
      4. `escapeChar` 必须与 `quoteChar` 相同，且不能为空。
      5. `skipLineCount` 保留默认值或设置为 0。
      6. `compression` 可以为“无压缩”或 **``GZip``** 。

3. 如果源是文件夹，则必须将复制活动中的 `recursive` 设置为 true，并且 `wildcardFilename` 需要为 `*` 或 `*.*`。 

4. `wildcardFolderPath`、`wildcardFilename`（`*` 或 `*.*` 除外）、`modifiedDateTimeStart`、`modifiedDateTimeEnd`、`prefix`、`enablePartitionDiscovery` 和 `additionalColumns` 均未指定。

复制活动中的 `allowCopyCommand` 下支持以下 COPY 语句设置：

| 属性          | 说明                                                  | 必需                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | 为 Azure Synapse Analytics 中的每个目标列指定默认值。  属性中的默认值将覆盖数据仓库中设置的 DEFAULT 约束，标识列不能有默认值。 | 否 |
| additionalOptions | 将直接在 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql)的“With”子句中传递给 Azure Synapse Analytics COPY 语句的其他选项。 根据需要将值括在引号中，以符合 COPY 语句要求。 | 否 |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

### <a name="staged-copy-by-using-copy-statement"></a>使用 COPY 语句的暂存复制

如果源数据不与 COPY 语句本机兼容，请通过临时暂存 Azure Blob 或 Azure Data Lake Storage Gen2（不能为 Azure 高级存储）来启用数据复制。 在这种情况下，该服务会自动转换数据，以满足 COPY 语句的数据格式要求。 然后，它会调用 COPY 语句，将数据加载到 Azure Synapse Analytics。 最后，它会从存储中清理临时数据。 若要详细了解如何通过暂存方式复制数据，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建 [Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2 链接服务](connector-azure-data-lake-storage.md#linked-service-properties)，使用帐户密钥或系统托管标识身份验证，Azure 存储帐户用作临时存储。

>[!IMPORTANT]
>- 对暂存链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
>- 如果临时 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。 

>[!IMPORTANT]
>如果你的暂存 Azure 存储配置了托管专用终结点并启用了存储防火墙，则必须使用托管标识身份验证并向 Synapse SQL Server 授予存储 Blob 数据读取器权限，以确保它可以在 COPY 语句加载期间访问暂存文件。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaCOPYstatement",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true
            },
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>使用 PolyBase 将数据加载到 Azure Synapse Analytics

使用 [PolyBase](/sql/relational-databases/polybase/polybase-guide) 是以高吞吐量将大量数据载入 Azure Synapse Analytics 的有效方法。 使用 PolyBase 而非默认 BULKINSERT 机制可以实现吞吐量的巨大增加。 若要查看带有用例的演练，请参阅[将 1 TB 的数据加载到 Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md)。

- 如果源数据位于 Azure Blob、Azure Data Lake Storage Gen1 或 Azure Data Lake Storage Gen2，并且格式与 PolyBase 兼容，则可以使用复制活动直接调用 PolyBase，使 Azure Synapse Analytics 从源中拉取数据 。 有关详细信息，请参阅 **[使用 PolyBase 直接复制](#direct-copy-by-using-polybase)** 。
- 如果 PolyBase 最初不支持源数据存储和格式，请改用 **[使用 PolyBase 的暂存复制](#staged-copy-by-using-polybase)** 功能。 暂存复制功能也能提供更高的吞吐量。 它自动将数据转换为与 PolyBase 兼容的格式，将数据存储在 Azure Blob 存储中，然后调用 PolyBase 将数据加载到 Azure Synapse Analytics。

> [!TIP]
> 详细了解[有关如何使用 PolyBase 的最佳做法](#best-practices-for-using-polybase)。 将 PolyBase 与 Azure Integration Runtime 一起使用时，Synapse 的直接或分段存储的有效[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units) 数始终为 2。 优化 DIU 不会影响性能，因为从存储加载数据的功能由 Synapse 引擎提供支持。

在复制活动中的 `polyBaseSettings` 下支持以下 PolyBase 设置：

| 属性          | 说明                                                  | 必需                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 指定在查询失败之前可以拒绝的行数或百分比。<br/><br/>有关 PolyBase 的拒绝选项的详细信息，请参阅 [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) 的“参数”部分。 <br/><br/>允许的值为 0（默认值）、1、2 等。 | 否                                            |
| rejectType        | 指定 **rejectValue** 选项是文本值还是百分比。<br/><br/>允许的值为 **Value**（默认值）和 **Percentage**。 | 否                                            |
| rejectSampleValue | 确定在 PolyBase 重新计算被拒绝行的百分比之前要检索的行数。<br/><br/>允许的值为 1、2 等。 | 如果 **rejectType** 是 **percentage**，则为“是” |
| useTypeDefault    | 指定在 PolyBase 从文本文件中检索数据时如何处理带分隔符的文本文件中的缺失值。<br/><br/>有关此属性的详细信息，请参阅[创建外部文件格式 (Transact SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql) 中的参数部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。<br><br> | 否                                            |

### <a name="direct-copy-by-using-polybase"></a>使用 PolyBase 直接复制

Azure Synapse Analytics PolyBase 直接支持 Azure Blob、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2。 如果源数据满足本部分所述的条件，请使用 PolyBase 从源数据存储直接复制到 Azure Synapse Analytics。 否则，请改用[使用 PolyBase 的暂存复制](#staged-copy-by-using-polybase)。

> [!TIP]
> 要将数据有效地复制到 Azure Synapse Analytics，请从[将 Data Lake Store 与 Azure Synapse Analytics 配合使用时，Azure 数据工厂能够更轻松且方便地观察数据](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse)中了解详细信息。

如果不满足要求，该服务会检查设置，并自动回退到 BULKINSERT 机制以进行数据移动。

1. **源链接的服务** 使用以下类型和身份验证方法：

    | 支持的源数据存储类型                             | 支持的源身份验证类型                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 帐户密钥身份验证、托管标识身份验证 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 服务主体身份验证                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 帐户密钥身份验证、托管标识身份验证 |

    >[!IMPORTANT]
    >- 对存储链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
    >- 如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。

2. 源数据格式为 Parquet、ORC 或带分隔符的文本，具有以下配置   ：

   1. 文件夹路径不包含通配符筛选器。
   2. 文件名为空或指向单个文件。 如果在复制活动中指定通配符文件名，该通配符只能是 `*` 或 `*.*`。
   3. `rowDelimiter` 为 **default**、 **\n**、 **\r\n** 或 **\r**。
   4. 将 `nullValue` 保留为默认值或设置为“空字符串”（“”），并将 `treatEmptyAsNull` 保留为默认值或设置为 true。
   5. `encodingName` 保留为默认值或设置为 **utf-8**。
   6. `quoteChar`、`escapeChar` 和 `skipLineCount` 未指定。 PolyBase 支持跳过标头行，可将其配置为 `firstRowAsHeader`。
   7. `compression` 可以为“无压缩”、 **``GZip``** 或“Deflate”。

3. 如果源是文件夹，则必须将复制活动中的 `recursive` 设置为 true。

4. 未指定 `wildcardFolderPath`、`wildcardFilename`、`modifiedDateTimeStart`、`modifiedDateTimeEnd`、`prefix`、`enablePartitionDiscovery` 和 `additionalColumns`。

>[!NOTE]
>如果源是一个文件夹，请注意，PolyBase 将从该文件夹及其所有子文件夹中检索文件，并且它不会从文件名以下划线 (_) 或句点 (.) 开头的文件中检索数据，如[此处 - LOCATION 参数](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2)所述。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>使用 PolyBase 的暂存复制

如果源数据不与 PolyBase 本机兼容，请通过临时暂存 Azure Blob 或 Azure Data Lake Storage Gen2（不能为 Azure 高级存储）来启用数据复制。 在这种情况下，该服务会自动转换数据，以满足 PolyBase 的数据格式要求。 然后，它调用 PolyBase 将数据加载到 Azure Synapse Analytics。 最后，它会从存储中清理临时数据。 若要详细了解如何通过暂存方式复制数据，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建 [Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)或 [Azure Data Lake Storage Gen2 链接服务](connector-azure-data-lake-storage.md#linked-service-properties)，这两个服务使用帐户密钥或托管标识身份验证来引用 Azure 存储帐户作为临时存储。

>[!IMPORTANT]
>- 对暂存链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
>- 如果临时 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。 

>[!IMPORTANT]
>如果你的暂存 Azure 存储配置了托管专用终结点并启用了存储防火墙，则必须使用托管标识身份验证并向 Synapse SQL Server 授予存储 Blob 数据读取器权限，以确保它可以在 PolyBase 加载期间访问暂存文件。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>有关使用 PolyBase 的最佳做法

除了 [Azure Synapse Analytics 的最佳做法](../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)中提到的做法以外，以下部分还提供了其他最佳做法。

#### <a name="required-database-permission"></a>所需数据库权限

若要使用 PolyBase，将数据加载到 Azure Synapse Analytics 的用户必须对目标数据库拥有[“CONTROL”权限](/sql/relational-databases/security/permissions-database-engine)。 一种实现方法是将该用户添加为 **db_owner** 角色的成员。 在 [Azure Synapse Analytics 概述](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)中了解如何执行此操作。

#### <a name="row-size-and-data-type-limits"></a>行大小和数据类型限制

PolyBase 负载限制为小于 1 MB 的行。 不能用它加载到 VARCHR(MAX)、NVARCHAR 或 VARBINARY(MAX)。 有关详细信息，请参阅 [Azure Synapse Analytics 服务容量限制](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果数据源中的行大于 1 MB，可能需要将源表垂直拆分为多个小型表。 确保每行的最大大小不超过该限制。 然后，可以使用 PolyBase 加载这些小型表，并在 Azure Synapse Analytics 中将它们合并在一起。

或者，对于具有此类较大列的数据，可以通过关闭“允许 PolyBase”设置，来使用非 PolyBase 加载数据。

#### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics 资源类

若要实现最佳吞吐量，请将更大的资源类分配给通过 PolyBase 将数据加载到 Azure Synapse Analytics 的用户。

#### <a name="polybase-troubleshooting"></a>排查 PolyBase 问题

#### <a name="loading-to-decimal-column"></a>加载到“小数”列

如果源数据为文本格式，或者位于其他与 PolyBase 不兼容的存储（使用暂存复制和 PolyBase）中，并且包含需加载到 Azure Synapse Analytics“小数”列中的空值，则可能出现以下错误：

```output
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

解决方法是在复制活动接收器 -> PolyBase 设置中取消选择“使用类型默认”选项（设为 false）。 “[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)”是一项 PolyBase 本地设置，它指定 PolyBase 从文本文件中检索数据时，如何处理带分隔符的文本文件中的缺失值。

#### <a name="check-the-tablename-property-in-azure-synapse-analytics"></a>查看 Azure Synapse Analytics 中的 tableName 属性

下表举例说明如何在 JSON 数据集中指定 **tableName** 属性。 其中显示了架构和表名称的多个组合。

| DB 架构 | 表名称 | **tableName** JSON 属性               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 或 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 或 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

如果看到以下错误，问题可能与为 **tableName** 属性指定的值有关。 有关为 **tableName** JSON 属性指定值的正确方法，请参阅上表。

```output
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

#### <a name="columns-with-default-values"></a>具有默认值的列

目前，PolyBase 功能只接受与目标表中数量相同的列。 例如，某个表包含四列，其中一列定义了默认值。 输入数据仍需包含四列。 包含三列的输入数据集生成类似于以下消息的错误：

```output
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是特殊形式的默认值。 如果列可为 null，则该列的 Blob 中的输入数据可能为空。 但输入数据集中不能缺少该数据。 PolyBase 在 Azure Synapse Analytics 中插入 NULL 来表示缺少的值。

#### <a name="external-file-access-failed"></a>外部文件访问失败

如果收到以下错误，请确保你使用的是托管标识身份验证，并已向 Azure Synapse 工作区的托管标识授予存储 Blob 数据读取者权限。

```output
Job failed due to reason: at Sink '[SinkName]': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External file access failed due to internal error: 'Error occurred while accessing HDFS: Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:\r\nHdfsBridge::isDirExist 
```

有关详细信息，请参阅[创建工作区后向托管标识授予权限](../synapse-analytics/security/how-to-grant-workspace-managed-identity-permissions.md#grant-permissions-to-managed-identity-after-workspace-creation)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Synapse Analytics 读取表和写入到表中。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

特定于 Azure Synapse Analytics 的设置可在源转换的“源选项”选项卡中找到。

**输入** 选择将源指向某个表（等效于 ```Select * from <table-name>```），还是输入自定义 SQL 查询。

**启用暂存** 强烈建议你在 Azure Synapse Analytics 源的生产工作负载中使用此选项。 使用管道中的 Azure Synapse Analytics 源执行[数据流活动](control-flow-execute-data-flow-activity.md)时，系统会提示输入暂存位置存储帐户，并将使用该帐户进行暂存数据加载。 它是从 Azure Synapse Analytics 加载数据的最快机制。

- 对存储链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
- 如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。
- 使用 Azure Synapse“无服务器”SQL 池作为源时，不支持启用暂存。

**查询**：如果在“输入”字段中选择“查询”，请为源输入 SQL 查询。 此设置会替代在数据集中选择的任何表。 此处不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中可返回表的 UDF。 此查询将生成可以在数据流中使用的源表。 使用查询也是减少进行测试或查找的行的好方法。

SQL 示例：```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批大小**：输入批大小，以将大型数据分成多个读取操作。 在数据流中，会使用此设置来设置 Spark 分栏式缓存。 这是选项字段，如果留空，它将使用 Spark 默认值。

**隔离级别：** 映射数据流中 SQL 源的默认设置为“读取未提交的内容”。 你可以将此处的隔离级别更改为以下值之一：

- 读取已提交的内容
- 读取未提交的内容
- 可重复的读取
- 可序列化
- 无（忽略隔离级别）

![隔离级别](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>接收器转换

特定于 Azure Synapse Analytics 的设置可在接收器转换的“设置”选项卡中找到。

**更新方法：** 确定数据库目标上允许哪些操作。 默认设置为仅允许插入。 若要更新、更新插入或删除行，需要进行 alter-row 转换才能标记这些操作的行。 对于更新、更新插入和删除操作，必须设置一个或多个键列，以确定要更改的行。

**表操作：** 确定在写入之前是否从目标表重新创建或删除所有行。

- 无：不会对表进行任何操作。
- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。
- 截断：将删除目标表中的所有行。

**启用暂存：** 这样，就可以使用 copy 命令加载到 Azure Synapse Analytics SQL 池。建议对大多数 Synpase 接收器使用此功能。 暂存存储是在[执行数据流活动](control-flow-execute-data-flow-activity.md)中配置的。 

- 对存储链接服务使用托管标识身份验证时，请分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 所需的配置。
- 如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。

**批大小**：控制每个 Bucket 中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。

**预处理和后处理 SQL 脚本**：输入将在数据写入接收器数据库之前（预处理）和之后（后处理）执行的多行 SQL 脚本

![预处理和后处理 SQL 脚本](media/data-flow/prepost1.png "SQL 处理脚本")

### <a name="error-row-handling"></a>行处理时出错

写入到 Azure Synapse Analytics 时，某些数据行可能会由于目标设置的约束而出错。 一些常见错误包括：

*    字符串或二进制数据在表中会被截断
*    无法在列中插入 NULL 值
*    在将值转换成数据类型时失败

默认情况下，遇到第一个错误时，数据流运行会失败。 你可以选择“出错时继续”，确保即使各行存在错误，也可以完成数据流。 该服务提供了不同的选项来处理这些错误行。

事务提交：选择是在单个事务中写入数据，还是分批写入数据。 单个事务将提供更好的性能，且事务完成之前，其他人将看不到任何写入的数据。 批处理事务的性能较差，但适用于大型数据集。

输出已拒绝的数据：如果已启用，则可将错误行输出到 Azure Blob 存储或所选 Azure Data Lake Storage Gen2 帐户中的 csv 文件。 这会写入包含三个附加列的错误行：SQL 操作（例如插入或更新）、数据流错误代码，以及有关行的错误消息。

出错时报告成功：如果已启用，则即使发现了错误行，也会将数据流标记为成功。 

:::image type="content" source="media/data-flow/sql-error-row-handling.png" alt-text="显示行处理出错的屏幕截图" border="false":::

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics 的数据类型映射

从/向 Azure Synapse Analytics 复制数据时，以下映射用于从 Azure Synapse Analytics 数据类型映射到 Azure 数据工厂临时数据类型。 在使用 Synapse 管道从 Azure Synapse Analytics 复制数据或向 Azure Synapse Analytics 复制数据时，也会使用这些映射，因为管道还在 Azure Synapse 中实现了 Azure 数据工厂。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

>[!TIP]
>请参阅 [Azure Synapse Analytics 中的表数据类型](../synapse-analytics/sql/develop-tables-data-types.md)一文，了解 Azure Synapse Analytics 支持的数据类型以及针对不支持的数据类型的解决方法。

| Azure Synapse Analytics 数据类型    | 数据工厂临时数据类型 |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | 布尔                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| 小数                               | 小数                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | 小数                        |
| nchar                                 | String, Char[]                 |
| numeric                               | 小数                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | 小数                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
