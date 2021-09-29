---
title: 在 Azure SQL 数据库中复制和转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂或 Azure Synapse Analytics 管道向/从 Azure SQL 数据库复制数据，以及如何在 Azure SQL 数据库中转换数据。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: ce4d1030999978ba5814d7978238c6f70026b34c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785109"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 复制和转换 Azure SQL 数据库中的数据

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
>
> - [版本 1](v1/data-factory-azure-sql-connector.md)
> - [当前版本](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂或 Azure Synapse 管道中的“复制活动”功能从/向 Azure SQL 数据库复制数据，并使用数据流转换 Azure SQL 数据库中的数据。 有关详细信息，请阅读 [Azure 数据工厂](introduction.md)或 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 的简介文章。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure SQL 数据库连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)表的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure SQL 数据库连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。 你还可以选择从 Azure SQL 数据库源进行并行复制。有关详细信息，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-database)部分。
- 作为接收器，根据源架构自动创建目标表（如果不存在）；在复制过程中，将数据追加到表或使用自定义逻辑调用存储过程。

如果使用 Azure SQL 数据库[无服务器层](../azure-sql/database/serverless-tier-overview.md)，请注意，当服务器暂停时，活动运行将失败，而不是等待自动恢复就绪。 可以添加活动重试或链接其他活动，以确保服务器在实际执行时处于活动状态。


> [!IMPORTANT]
> 如果使用 Azure 集成运行时复制数据，请配置[服务器级防火墙规则](../azure-sql/database/firewall-configure.md)，以便 Azure 服务可以访问服务器。
> 如果使用自承载集成运行时复制数据，请将防火墙配置为允许合适的 IP 范围。 此范围包括用于连接 Azure SQL 数据库的计算机的 IP。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-sql-database-linked-service-using-ui"></a>使用 UI 创建 Azure SQL 数据库链接服务

使用以下步骤在 Azure 门户 UI 中创建 Azure SQL 数据库链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

   # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

   :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

   # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

   :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

   

2. 搜索 SQL 并选择 Azure SQL 数据库连接器。

    :::image type="content" source="media/connector-azure-sql-database/azure-sql-database-connector.png" alt-text="选择“Azure SQL 数据库连接器”。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-azure-sql-database/configure-azure-sql-database-linked-service.png" alt-text="Azure SQL 数据库链接服务的配置屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

对于特定于 Azure SQL 数据库连接器的 Azure 数据工厂或 Synapse 管道实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureSqlDatabase 。 | 是 |
| connectionString | 为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 <br/>还可以将密码或服务主体密钥放在 Azure Key Vault 中。 如果使用 SQL 身份验证，请从连接字符串中提取 `password` 配置。 有关详细信息，请参阅表格后面的 JSON 示例，以及[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure 密钥保管库中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| tenant | 指定应用程序所在的租户的信息（例如域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| azureCloudType | 对于服务主体身份验证，请指定 Azure AD 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为 AzurePublic、AzureChina、AzureUsGovernment 和 AzureGermany   。 默认情况下，使用数据工厂或 Synapse 管道的云环境。 | 否 |
| alwaysEncryptedSettings | 指定所需的 alwaysencryptedsettings 信息来启用 Always Encrypted，以使用托管标识或服务主体保护 SQL Server 中存储的敏感数据。 有关详细信息，请参阅表格后面的 JSON 示例以及[使用 Always Encrypted](#using-always-encrypted) 部分。 如果不指定此属性，将禁用默认的 Always Encrypted 设置。 |否 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否 |

> [!NOTE]
> 数据流中不支持 Azure SQL 数据库 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true)。 

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。 对于“SHIR(自承载集成运行时)”类型的链接服务设置，也建议添加 `Pooling=false`。 可以在链接服务创建窗体的“其他连接属性”部分中将池和其他连接参数添加为新参数名称和值。

### <a name="sql-authentication"></a>SQL 身份验证

**示例：使用 SQL 身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：Azure Key Vault 中的密码**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

示例：使用 Always Encrypted

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "alwaysEncryptedSettings": {
            "alwaysEncryptedAkvAuthType": "ServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
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

要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 为 Azure 门户上的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员必须是 Azure AD 用户或 Azure AD 组，但不能是服务主体。 执行此步骤后，在下一步骤中便可使用 Azure AD 标识来为服务主体创建包含的数据库用户。

3. 为服务主体[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户或其他用户所做的那样向服务主体授予所需的权限。 运行以下代码。 有关更多选项，请参阅[此文档](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. 在 Azure 数据工厂或 Synapse 工作区中配置 Azure SQL 数据库链接服务。

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
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

数据工厂或 Synapse 工作区可以与 [Azure 资源的托管标识](data-factory-service-identity.md)相关联，该标识在对 Azure 中的其他资源进行身份验证时代表服务。 可将此托管标识用于 Azure SQL 数据库身份验证。 指定的工厂或 Synapse 工作区可以使用此标识访问数据库数据或从/向数据库复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 为 Azure 门户上的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员可以是 Azure AD 用户，也可以是 Azure AD 组。 如果授予包含托管标识的组管理员角色，则可跳过步骤 3 和步骤 4。 管理员拥有对数据库的完全访问权限。

2. 为托管标识[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your_resource_name] FROM EXTERNAL PROVIDER;
    ```

3. 授予托管标识所需的权限，就像通常为 SQL 用户和其他用户所做的那样。 运行以下代码。 有关更多选项，请参阅[此文档](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your_resource_name];
    ```

4. 配置 Azure SQL 数据库链接服务。

**示例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个节和属性的完整列表，请参阅[数据集](./concepts-datasets-linked-services.md)。

Azure SQL 数据库数据集支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlTable 。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

### <a name="dataset-properties-example"></a>数据集属性示例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure SQL 数据库源和接收器支持的属性列表。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL 数据库作为源

>[!TIP]
>若要详细了解如何使用数据分区从 Azure SQL 数据库高效加载数据，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-database)。

若要从 Azure SQL 数据库复制数据，复制活动的 **source** 节需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **AzureSqlSource**。 为了向后兼容，仍然支持“SqlSource”类型。 | 是 |
| sqlReaderQuery | 此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否 |
| storedProcedureParameters | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| isolationLevel | 指定 SQL 源的事务锁定行为。 允许的值为：ReadCommitted、ReadUncommitted、RepeatableRead、Serializable、Snapshot    。 如果未指定，则使用数据库的默认隔离级别。 请参阅[此文档](/dotnet/api/system.data.isolationlevel)了解更多详细信息。 | 否 |
| partitionOptions | 指定用于从 Azure SQL 数据库加载数据的数据分区选项。 <br>允许值包括：None（默认值）、PhysicalPartitionsOfTable 和 DynamicRange  。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Azure SQL 数据库并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| 在 `partitionSettings` 下： | | |
| partitionColumnName | 以整数类型、日期类型或日期/时间类型（`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2` 或 `datetimeoffset`）指定源列的名称，范围分区将使用它进行并行复制。 如果未指定，系统会自动检测表的索引或主键并将其用作分区列。<br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfDynamicRangePartitionCondition `。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-database)部分。 | 否 |
| partitionUpperBound | 分区范围拆分的分区列的最大值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。  <br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-database)部分。 | 否 |
| partitionLowerBound | 分区范围拆分的分区列的最小值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。<br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-database)部分。 | 否 |

**请注意以下几点：**

- 如果为 **AzureSqlSource** 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据库源运行此查询可获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数 。
- 在源中使用存储过程检索数据时，请注意，如果存储过程旨在当传入不同的参数值时返回不同的架构，则从 UI 导入架构时，或通过自动创建表的功能将数据复制到 SQL 数据库时，可能会遇到故障或出现意外的结果。

#### <a name="sql-query-example"></a>SQL 查询示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>存储过程示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>存储过程定义

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL 数据库作为接收器

> [!TIP]
> 若要详细了解支持的写入行为、配置和最佳做法，请参阅[有关将数据载入 Azure SQL 数据库的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

将数据复制到 Azure SQL 数据库时，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **AzureSqlSink**。 为了向后兼容，仍然支持“SqlSink”类型。 | 是 |
| preCopyScript | 将数据写入到 Azure SQL 数据库之前，指定复制活动要运行的 SQL 查询。 每次运行复制仅调用该查询一次。 使用此属性清理预加载的数据。 | 否 |
| tableOption | 指定是否根据源架构[自动创建接收器表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 <br>接收器指定存储过程时不支持自动创建表。 <br>允许的值为：`none`（默认值）、`autoCreate`。 | 否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。<br>请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)中的示例。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| writeBatchSize | 每批要插入到 SQL 表中的行数。<br/> 允许的值为 **integer**（行数）。 默认情况下，该服务根据行大小动态确定适当的批大小。 | 否 |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/> 允许的值为 **timespan**。 例如“00:30:00”（30 分钟）。 | 否 |
| disableMetricsCollection | 该服务收集指标（如 Azure SQL 数据库 DTU），以获取复制性能优化和建议，从而引入额外的主数据库访问权限。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**示例 2：在复制过程中调用存储过程**

请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)，了解更多详细信息。

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>从 SQL 数据库进行并行复制

复制活动中的 Azure SQL 数据库连接器提供内置的数据分区，用于并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

:::image type="content" source="./media/connector-sql-server/connector-sql-partition-options.png" alt-text="分区选项的屏幕截图":::

启用分区复制时，复制活动将对 Azure SQL 数据库源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Azure SQL 数据库检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Azure SQL 数据库加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。        | **分区选项**：表的物理分区。 <br><br/>在执行期间，该服务将自动检测物理分区并按分区复制数据。 <br><br/>若要检查表是否有物理分区，可参考[此查询](#sample-query-to-check-physical-partition)。 |
| 从不包含物理分区但包含用于数据分区的整数或日期时间列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**（可选）：指定用于对数据进行分区的列。 如果未指定，将使用索引或主键列。<br/>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，表中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测这些值。<br><br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数或日期/日期时间列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。<br>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，查询结果中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测该值。<br><br>在执行期间，该服务会将 `?AdfRangePartitionColumnName` 替换为每个分区的实际列名称和值范围，并发送到 Azure SQL 数据库。 <br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 <br><br>下面是针对不同场景的更多示例查询：<br> 1.查询整个表： <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.使用列选择和附加的 where 子句筛选器从表中查询： <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.使用子查询进行查询： <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.在子查询中使用分区查询： <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

使用分区选项加载数据的最佳做法：

1. 选择独特的列作为分区列（如主键或唯一键），以避免数据倾斜。 
2. 如果表具有内置分区，请使用名为“表的物理分区”分区选项来提升性能。    
3. 如果使用 Azure Integration Runtime 复制数据，则可设置较大的“[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units)”(>4) 以利用更多计算资源。 检查此处适用的方案。
4. “[复制并行度](copy-activity-performance-features.md#parallel-copy)”可控制分区数量，将此数字设置得太大有时会损害性能，建议将此数字设置按以下公式计算的值：（DIU 或自承载 IR 节点数）*（2 到 4）。

**示例：从包含物理分区的大型表进行完整加载**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "AzureSqlSource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

如果表具有物理分区，你可看到“HasPartition”为“是”，如下所示。

:::image type="content" source="./media/connector-azure-sql-database/sql-query-result.png" alt-text="SQL 查询结果":::

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>有关将数据载入 Azure SQL 数据库的最佳做法

将数据复制到 Azure SQL 数据库时，可能需要不同的写入行为：

- [追加](#append-data)：我的源数据只包含新记录。
- [更新插入](#upsert-data)：我的源数据包含插入和更新内容。
- [覆盖](#overwrite-the-entire-table)：我需要每次都重新加载整个维度表。
- [使用自定义逻辑进行写入](#write-data-with-custom-logic)：在将数据最终插入目标表之前，我需要额外的处理。

有关如何在该服务中进行配置和最佳做法，请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 Azure SQL 数据库接收器连接器的默认行为。 该服务执行批量插入，以有效地在表中写入数据。 可以相应地在复制活动中配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

选项 1：当需要复制大量数据时，可以使用复制活动将所有记录大容量加载到一个临时表中，然后运行存储过程活动来一次性应用 [MERGE](/sql/t-sql/statements/merge-transact-sql) 或 INSERT/UPDATE 语句。 

复制活动当前并非原生支持将数据加载到数据库临时表中。 有一种结合多种活动进行设置的高级方法，请参阅[优化 Azure SQL 数据库批量更新插入方案](https://github.com/scoriani/azuresqlbulkupsert)。 下面显示了使用永久表作为暂存的示例。

例如，可以创建一个管道，其中“复制活动”与“存储过程活动”相链接。 前者将数据从源存储复制到数据集中的 Azure SQL 数据库临时表（例如，表名为“UpsertStagingTable”的表）。 然后，后者调用一个存储过程，以将临时表中的源数据合并到目标表中，并清理临时表。

:::image type="content" source="./media/connector-azure-sql-database/azure-sql-database-upsert.png" alt-text="Upsert":::

在数据库中使用 MERGE 逻辑定义一个存储过程（如以下示例所示），以便从上述存储过程活动指向该过程。 假设目标是包含三个列的 **Marketing** 表：**ProfileID**、**State** 和 **Category**。 根据 **ProfileID** 列执行更新插入。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

选项 2：可选择[在复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 这种方法运行源表中的每个批（由 `writeBatchSize` 属性控制），而不是在复制活动中使用批量插入作为默认方法。

选项 3：可以使用[映射数据流](#sink-transformation)，它提供了内置的插入/更新插入/更新方法。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置 **preCopyScript** 属性。 在这种情况下，对于运行的每个复制活动，该服务首先运行脚本。 然后，运行复制来插入数据。 例如，若要使用最新数据覆盖整个表，请指定一个脚本，以先删除所有记录，然后从源批量加载新数据。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

使用自定义逻辑写入数据的步骤与[更新插入数据](#upsert-data)部分中的描述类似。 如果在将源数据最终插入目标表之前需要应用额外的处理，则可先将数据加载到临时表，然后再调用存储过程活动，或者在复制活动接收器中调用存储过程来应用数据，或者使用映射数据流。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库中时，还可以通过对每批源表使用附加参数来配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](/dotnet/framework/data/adonet/sql/table-valued-parameters)。

当内置复制机制无法使用时，还可使用存储过程。 例如，在将源数据最终插入目标表之前应用额外的处理。 额外处理的示例包括合并列、查找其他值以及将数据插入多个表。

以下示例演示如何使用存储过程在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入数据和接收器 **Marketing** 表各有三列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行更新插入，并仅将其应用于名为“ProductA”的特定类别。

1. 在数据库中，使用与 **sqlWriterTableType** 相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，使用与 **sqlWriterStoredProcedureName** 相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称与数据集中定义的 **tableName** 相同。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. 在 Azure 数据工厂或 Synapse 管道中，在复制活动中定义 SQL 接收器部分，如下所示：

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流的过程中转换数据时，可以在 Azure SQL 数据库中读取表以及将数据写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

特定于 Azure SQL 数据库的设置可在源转换的“源选项”选项卡中找到。

**输入：** 选择将源指向某个表（等效于 ```Select * from <table-name>```），还是输入自定义 SQL 查询。

**查询**：如果在“输入”字段中选择“查询”，请为源输入 SQL 查询。 此设置会替代在数据集中选择的任何表。 此处不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中可返回表的 UDF。 此查询将生成可以在数据流中使用的源表。 使用查询也是减少进行测试或查找的行的好方法。

**存储过程**：如果希望从源数据库执行的存储过程生成投影和源数据，请选择此选项。 可以键入架构、过程名称和参数，或者单击“刷新”以要求该服务发现架构和过程名称。 然后，可以单击“导入”以使用格式 ``@paraName`` 导入所有过程参数。

:::image type="content" source="media/data-flow/stored-procedure-2.png" alt-text="存储过程":::

- SQL 示例：```Select * from MyTable where customerId > 1000 and customerId < 2000```
- 参数化 SQL 示例：``"select * from {$tablename} where orderyear > {$year}"``

**批大小**：输入批大小，以将大型数据分成多个读取操作。

**隔离级别：** 映射数据流中 SQL 源的默认设置为“读取未提交的内容”。 你可以将此处的隔离级别更改为以下值之一：

- 读取已提交的内容
- 读取未提交的内容
- 可重复的读取
- 可序列化
- 无（忽略隔离级别）

:::image type="content" source="media/data-flow/isolationlevel.png" alt-text="隔离级别":::

### <a name="sink-transformation"></a>接收器转换

特定于 Azure SQL 数据库的设置可在接收器转换的“设置”选项卡中找到。

**更新方法：** 确定数据库目标上允许哪些操作。 默认设置为仅允许插入。 若要更新、更新插入或删除行，需要进行 alter-row 转换才能标记这些操作的行。 对于更新、更新插入和删除操作，必须设置一个或多个键列，以确定要更改的行。

:::image type="content" source="media/data-flow/keycolumn.png" alt-text="键列":::

该服务在后续的更新、更新插入和删除中会使用你在此处将其选取为密钥的列名称。 因此，你必须选取存在于接收器映射中的列。 如果你不希望将值写入此键列，请单击“跳过写入键列”。

你可以将此处用于更新目标 Azure SQL 数据库表的键列参数化。 如果你有多个用于组合键的列，单击“自定义表达式”，你将能够使用数据流[表达式语言](control-flow-expression-language-functions.md)添加动态内容，其中可以包含一个字符串数组，该数组包含一个组合键的列名。

**表操作：** 确定在写入之前是否从目标表重新创建或删除所有行。

- 无：不会对表进行任何操作。
- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。
- 截断：将删除目标表中的所有行。

**批大小**：控制每个 Bucket 中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。

**使用 TempDB**：默认情况下，作为加载过程的一部分，该服务将使用全局临时表来存储数据。 你也可以取消选中“使用 TempDB”选项，改为要求该服务将临时保存表存储在用于此接收器的数据库中的用户数据库中。

:::image type="content" source="media/data-flow/tempdb.png" alt-text="使用临时数据库":::

**预处理和后处理 SQL 脚本**：输入将在数据写入接收器数据库之前（预处理）和之后（后处理）执行的多行 SQL 脚本

:::image type="content" source="media/data-flow/prepost1.png" alt-text="预处理和后处理 SQL 脚本":::

### <a name="error-row-handling"></a>行处理时出错

写入到 Azure SQL DB 时，某些数据行可能会由于目标设置的约束而发生故障。 一些常见错误包括：

*    字符串或二进制数据在表中会被截断
*    无法在列中插入 NULL 值
*    INSERT 语句与 CHECK 约束冲突

默认情况下，遇到第一个错误时，数据流运行会失败。 你可以选择“出错时继续”，确保即使各行存在错误，也可以完成数据流。 该服务提供了不同的选项来处理这些错误行。

事务提交：选择是在单个事务中写入数据，还是分批写入数据。 单个事务将提供较差的性能，但在事务完成之前，其他人将看不到任何写入的数据。  

输出已拒绝的数据：如果已启用，则可将错误行输出到 Azure Blob 存储或所选 Azure Data Lake Storage Gen2 帐户中的 csv 文件。 这会写入包含三个附加列的错误行：SQL 操作（例如插入或更新）、数据流错误代码，以及有关行的错误消息。

出错时报告成功：如果已启用，则即使发现了错误行，也会将数据流标记为成功。 

:::image type="content" source="media/data-flow/sql-error-row-handling.png" alt-text="行处理时出错":::


## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从/向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型映射到 Azure 数据工厂临时数据类型。 Synapse 管道功能使用相同的映射，它直接实现 Azure 数据工厂。  若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| date |DateTime |
| datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前复制活动支持的最大精度为 28。 如果有精度大于 28 的数据，请考虑在 SQL 查询中将其转换为字符串。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>使用 Always Encrypted

使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 从/向 SQL Server 复制数据时，请执行以下步骤： 

1. 将[列主密钥 (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) 存储在 [Azure 密钥保管库](../key-vault/general/overview.md)中。 详细了解[如何使用 Azure 密钥保管库配置 Always Encrypted](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)

2. 确保授予对存储了[列主密钥 (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) 的密钥保管库的访问权限。 有关所需的权限，请参阅[此文](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults)。

3. 创建链接服务，以使用托管标识或服务主体连接到 SQL 数据库并启用“Always Encrypted”功能。 

>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 支持以下方案： 
>1. 源或接收器数据存储使用托管标识或服务主体作为密钥提供程序身份验证类型。
>2. 源和接收器数据存储都使用托管标识作为密钥提供程序身份验证类型。
>3. 源和接收器数据存储都使用同一个服务主体作为密钥提供程序身份验证类型。

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
