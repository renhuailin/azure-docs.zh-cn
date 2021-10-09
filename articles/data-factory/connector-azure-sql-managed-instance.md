---
title: 在 Azure SQL 托管实例中复制和转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂或 Synapse Analytics 管道来复制和转换 Azure SQL 托管实例中的数据。
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 1e56f1ecf0345ccb13e7e5899fb1602dcad666b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744043"
---
# <a name="copy-and-transform-data-in-azure-sql-managed-instance-using-azure-data-factory-or-synapse-analytics"></a>使用 Azure 数据工厂或 Synapse Analytics 来复制和转换 Azure SQL 托管实例中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何“复制活动”功能从 Azure SQL 托管实例复制数据或将数据复制到其中，并使用“数据流”转换 Azure SQL 托管实例中的数据。 有关详细信息，请阅读 [Azure 数据工厂](introduction.md)和 [Synapse Analytics](../synapse-analytics/overview-what-is.md) 的简介文章。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SQL 托管实例连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure SQL 数据库连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。 还可选择从 SQL MI 源进行并行复制。有关详细信息，请参阅[从 SQL MI 进行并行复制](#parallel-copy-from-sql-mi)部分。
- 作为接收器，根据源架构自动创建目标表（如果不存在）；在复制过程中，使用自定义逻辑将数据追加到表或调用存储过程。

## <a name="prerequisites"></a>先决条件

若要访问 SQL 托管实例[公用终结点](../azure-sql/managed-instance/public-endpoint-overview.md)，可以使用托管的 Azure 集成运行时。 确保启用公共终结点，并在网络安全组中允许公共终结点流量，使该服务能够连接到你的数据库。 有关详细信息，请参阅[此指南](../azure-sql/managed-instance/public-endpoint-configure.md)。

若要访问 SQL 托管实例专用终结点，请设置一个能够访问数据库的[自承载集成运行时](create-self-hosted-integration-runtime.md)。 如果预配托管实例所在的虚拟网络中的自承载集成运行时，请确保集成运行时计算机与托管实例位于不同的子网中。 如果预配的自承载集成运行时与托管实例位于不同的虚拟网络中，则可使用虚拟网络对等互连或虚拟网络间的连接。 有关详细信息，请参阅[将应用程序连接到 SQL 托管实例](../azure-sql/managed-instance/connect-application-instance.md)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-azure-sql-managed-instance-using-ui"></a>使用 UI 创建到 Azure SQL 托管实例的链接服务

使用以下步骤在 Azure 门户 UI 中创建到 SQL 托管实例的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索“SQL”并选择 Azure SQL Server 托管实例连接器。

    :::image type="content" source="media/connector-azure-sql-managed-instance/azure-sql-managed-instance-connector.png" alt-text="Azure SQL Server 托管实例连接器的屏幕截图。":::    

1. 配置服务详细信息，测试连接，然后创建新的链接服务。

    :::image type="content" source="media/connector-azure-sql-managed-instance/configure-azure-sql-managed-instance-linked-service.png" alt-text="SQL 托管实例的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

以下部分详述了用来定义特定于 SQL 托管实例连接器的 Azure 数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

SQL 托管实例链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureSqlMI**。 | 是 |
| connectionString |此属性指定通过 SQL 身份验证连接到 SQL 托管实例时所需的 **connectionString** 信息。 有关详细信息，请参阅以下示例。 <br/>默认端口为 1433。 如果将 SQL 托管实例与公共终结点配合使用，请显式指定端口 3342。<br> 还可以在 Azure Key Vault 中输入密码。 如果使用 SQL 身份验证，请从连接字符串中提取 `password` 配置。 有关详细信息，请参阅表格后面的 JSON 示例，以及[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地存储它，或[引用存储在 Azure 密钥保管库中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| tenant | 指定应用程序所在的租户的信息（例如域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| azureCloudType | 对于服务主体身份验证，请指定 Azure AD 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为 AzurePublic、AzureChina、AzureUsGovernment 和 AzureGermany   。 默认情况下，使用服务的云环境。 | 否 |
| alwaysEncryptedSettings | 指定所需的 alwaysencryptedsettings 信息来启用 Always Encrypted，以使用托管标识或服务主体保护 SQL Server 中存储的敏感数据。 有关详细信息，请参阅表格后面的 JSON 示例以及[使用 Always Encrypted](#using-always-encrypted) 部分。 如果不指定此属性，将禁用默认的 Always Encrypted 设置。 |否 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果托管实例具有公共终结点并允许服务访问该服务公共终结点，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |是 |

> [!NOTE]
> 数据流中不支持 SQL 托管实例 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true)。 

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

### <a name="sql-authentication"></a>SQL 身份验证

**示例 1：使用 SQL 身份验证**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：将 SQL 身份验证与 Azure Key Vault 中的密码结合使用**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

示例 3：将 SQL 身份验证与 Always Encrypted 配合使用

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

1. 按步骤[为托管实例预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

2. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

3. 为托管标识[创建登录名](/sql/t-sql/statements/create-login-transact-sql)。 在 SQL Server Management Studio (SSMS) 中，使用作为 **sysadmin** 的 SQL Server 帐户连接到托管实例。 在 **master** 数据库中，运行以下 T-SQL：

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. 为托管标识[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 连接到要从其复制数据或将数据复制到其中的数据库，运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. 授予托管标识所需的权限，就像通常为 SQL 用户和其他用户所做的那样。 运行以下代码。 有关更多选项，请参阅[此文档](/sql/t-sql/statements/alter-role-transact-sql)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. 配置 SQL 托管实例链接服务。

**示例：使用服务主体身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 资源托管标识身份验证

数据工厂或 Synapse 工作区可以与 [Azure 资源的托管标识](data-factory-service-identity.md)相关联，该托管标识代表了向其他 Azure 服务进行身份验证的服务。 可将此托管标识用于 SQL 托管实例身份验证。 指定的服务可以使用此标识访问数据库数据或从/向数据库复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 按步骤[为托管实例预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

2. 为托管标识[创建登录名](/sql/t-sql/statements/create-login-transact-sql)。 在 SQL Server Management Studio (SSMS) 中，使用作为 **sysadmin** 的 SQL Server 帐户连接到托管实例。 在 **master** 数据库中，运行以下 T-SQL：

    ```sql
    CREATE LOGIN [your_factory_or_workspace_ name] FROM EXTERNAL PROVIDER
    ```

3. 为托管标识[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 连接到要从其复制数据或将数据复制到其中的数据库，运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your_factory_or_workspace_name] FROM EXTERNAL PROVIDER
    ```

4. 授予托管标识所需的权限，就像通常为 SQL 用户和其他用户所做的那样。 运行以下代码。 有关更多选项，请参阅[此文档](/sql/t-sql/statements/alter-role-transact-sql)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your_factory_or_workspace_name]
    ```

5. 配置 SQL 托管实例链接服务。

**示例：使用托管标识身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

如需可用于定义数据集的节和属性的完整列表，请参阅有关数据集的文章。 此部分提供 SQL 托管实例数据集支持的属性列表。

若要从/向 SQL 托管实例复制数据，以下属性需受支持：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlMITable。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

**示例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 此部分提供 SQL 托管实例源和和接收器支持的属性列表。

### <a name="sql-managed-instance-as-a-source"></a>以 SQL 托管实例作为源

>[!TIP]
>若要使用数据分区从 SQL MI 高效地加载数据，请参阅[从 SQL MI 进行并行复制](#parallel-copy-from-sql-mi)。

若要从 SQL 托管实例复制数据，复制活动的 source 节需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **SqlMISource**。 | 是 |
| sqlReaderQuery |此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| isolationLevel | 指定 SQL 源的事务锁定行为。 允许的值为：ReadCommitted、ReadUncommitted、RepeatableRead、Serializable、Snapshot    。 如果未指定，则使用数据库的默认隔离级别。 请参阅[此文档](/dotnet/api/system.data.isolationlevel)了解更多详细信息。 | 否 |
| partitionOptions | 指定用于从 SQL MI 加载数据的数据分区选项。 <br>允许值包括：None（默认值）、PhysicalPartitionsOfTable 和 DynamicRange  。<br>启用分区选项（即该选项不为 `None`）时，用于从 SQL MI 并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| 在 `partitionSettings` 下： | | |
| partitionColumnName | 以整数类型、日期类型或日期/时间类型（`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2` 或 `datetimeoffset`）指定源列的名称，范围分区将使用它进行并行复制。 如果未指定，系统会自动检测表的索引或主键并将其用作分区列。<br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfDynamicRangePartitionCondition `。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-mi)部分。 | 否 |
| partitionUpperBound | 分区范围拆分的分区列的最大值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。  <br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-mi)部分。 | 否 |
| partitionLowerBound | 分区范围拆分的分区列的最小值。 此值用于决定分区步幅，不用于筛选表中的行。 将对表或查询结果中的所有行进行分区和复制。 如果未指定，复制活动会自动检测该值。<br>当分区选项是 `DynamicRange` 时适用。 有关示例，请参阅[从 SQL 数据库进行并行复制](#parallel-copy-from-sql-mi)部分。 | 否 |

**请注意以下几点：**

- 如果为 **SqlMISource** 指定 sqlReaderQuery，则复制活动会针对 SQL 托管实例源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数 。
- 在源中使用存储过程检索数据时，请注意，如果存储过程设计为当传入不同的参数值时返回不同的架构，则从 UI 导入架构或使用自动表创建将数据复制到 SQL 数据库时，可能会遇到故障或出现意外的结果。

**示例：使用 SQL 查询**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**示例：使用存储过程**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**存储过程定义**

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

### <a name="sql-managed-instance-as-a-sink"></a>以 SQL 托管实例作为接收器

> [!TIP]
> 若要详细了解支持的写入行为、配置和最佳做法，请参阅[将数据加载到 SQL 托管实例中的最佳做法](#best-practice-for-loading-data-into-sql-managed-instance)。

若要将数据复制到 SQL 托管实例，复制活动的 sink 节需要支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **SqlMISink**。 | 是 |
| preCopyScript |此属性指定将数据写入到 SQL 托管实例之前要由复制活动运行的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| tableOption | 指定是否根据源架构[自动创建接收器表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 接收器指定存储过程时不支持自动创建表。 允许的值为：`none`（默认值）、`autoCreate`。 |否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。<br>请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)中的示例。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| writeBatchSize |每批要插入到 SQL 表中的行数。<br/>允许的值为表示行数的整数。 默认情况下，该服务根据行大小动态确定适当的批大小。  |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值是指时间跨度。 例如，“00:30:00”表示 30 分钟。 |否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**示例 2：在复制过程中调用存储过程**

请参阅[调用 SQL MI 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)，了解更多详细信息。

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="parallel-copy-from-sql-mi"></a>从 SQL MI 进行并行复制

复制活动中的 Azure SQL 托管实例连接器提供内置的数据分区，用于并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。 

:::image type="content" source="./media/connector-sql-server/connector-sql-partition-options.png" alt-text="分区选项的屏幕截图":::

启用分区复制时，复制活动将对 SQL MI 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则该服务会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 SQL MI 检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 SQL MI 加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。        | **分区选项**：表的物理分区。 <br><br/>在执行期间，该服务将自动检测物理分区并按分区复制数据。 <br><br/>若要检查表是否有物理分区，可参考[此查询](#sample-query-to-check-physical-partition)。 |
| 从不包含物理分区但包含用于数据分区的整数或日期时间列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**（可选）：指定用于对数据进行分区的列。 如果未指定，将使用索引或主键列。<br/>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，表中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测这些值。<br><br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数或日期/日期时间列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。<br>分区上限和分区下限（可选） ：指定是否要确定分区步幅。 这不适用于筛选表中的行，查询结果中的所有行都将进行分区和复制。 如果未指定，复制活动会自动检测该值。<br><br>在执行期间，该服务会将 `?AdfRangePartitionColumnName` 替换为每个分区的实际列名称和值范围，并发送到 SQL MI。 <br>例如，如果分区列“ID”的值范围为 1 至 100，其下限设置为 20、上限设置为 80，并行复制设置为 4，则该服务会按 4 个分区检索数据，ID 范围分别为 <=20、[21, 50]、[51, 80] 和 >=81。 <br><br>下面是针对不同场景的更多示例查询：<br> 1.查询整个表： <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.使用列选择和附加的 where 子句筛选器从表中查询： <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.使用子查询进行查询： <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.在子查询中使用分区查询： <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

使用分区选项加载数据的最佳做法：

1. 选择独特的列作为分区列（如主键或唯一键），以避免数据倾斜。 
2. 如果表具有内置分区，请使用名为“表的物理分区”分区选项来提升性能。    
3. 如果使用 Azure Integration Runtime 复制数据，则可设置较大的“[数据集成单元 (DIU)](copy-activity-performance-features.md#data-integration-units)”(>4) 以利用更多计算资源。 检查此处适用的方案。
4. “[复制并行度](copy-activity-performance-features.md#parallel-copy)”可控制分区数量，将此数字设置得太大有时会损害性能，建议将此数字设置按以下公式计算的值：（DIU 或自承载 IR 节点数）*（2 到 4）。

**示例：从包含物理分区的大型表进行完整加载**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "SqlMISource",
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

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>将数据加载到 SQL 托管实例中的最佳做法

将数据复制到 SQL 托管实例中时，可能需要不同的写入行为：

- [追加](#append-data)：我的源数据只包含新记录。
- [更新插入](#upsert-data)：我的源数据包含插入和更新内容。
- [覆盖](#overwrite-the-entire-table)：我需要每次都重新加载整个维度表。
- [使用自定义逻辑进行写入](#write-data-with-custom-logic)：在将数据最终插入目标表之前，我需要额外的处理。 

有关配置步骤和最佳做法，请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 SQL 托管实例接收器连接器的默认行为。 该服务执行批量插入，以有效地在表中写入数据。 可以相应地在复制活动中配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

选项 1：当需要复制大量数据时，可以使用复制活动将所有记录大容量加载到一个临时表中，然后运行存储过程活动来一次性应用 [MERGE](/sql/t-sql/statements/merge-transact-sql) 或 INSERT/UPDATE 语句。 

复制活动当前并非原生支持将数据加载到数据库临时表中。 有一种结合多种活动进行设置的高级方法，请参阅[优化 SQL 数据库批量更新插入方案](https://github.com/scoriani/azuresqlbulkupsert)。 下面显示了使用永久表作为暂存的示例。

例如，可以创建一个管道，其中“复制活动”与“存储过程活动”相链接。 前者将数据从源存储复制到数据集中的 Azure SQL 托管实例临时表（例如，表名为“UpsertStagingTable”的表）。 然后，后者调用一个存储过程，以将临时表中的源数据合并到目标表中，并清理临时表。

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

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置 **preCopyScript** 属性。 在这种情况下，对于运行的每个复制活动，该服务首先运行脚本。 然后，运行复制来插入数据。 例如，若要使用最新数据覆盖整个表，请指定一个脚本，以先删除所有记录，然后从源批量加载新数据。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

使用自定义逻辑写入数据的步骤与[更新插入数据](#upsert-data)部分中的描述类似。 如果在将源数据最终插入目标表之前需要应用额外的处理，则可先将数据加载到临时表，然后再调用存储过程活动，或者在复制活动接收器中调用存储过程来应用数据。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 SQL 托管实例中时，还可以通过对每批的源表使用更多参数来配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](/dotnet/framework/data/adonet/sql/table-valued-parameters)。

当内置复制机制无法使用时，还可使用存储过程。 例如，在将源数据最终插入目标表之前应用额外的处理。 额外处理的示例包括合并列、查找其他值以及将数据插入多个表。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入数据和接收器 **Marketing** 表各有三列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行更新插入，并仅将其应用于名为“ProductA”的特定类别。

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

3. 在管道中，在复制活动中定义 SQL MI 接收器部分，如下所示：

    ```json
    "sink": {
        "type": "SqlMISink",
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

在映射数据流中转换数据时，可以在 Azure SQL 托管实例中读取表以及将数据写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。


### <a name="source-transformation"></a>源转换

下表列出了 Azure SQL 托管实例源支持的属性。 你可以在“源选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 表 | 如果你选择“表”作为输入，则数据流会从数据集中指定的表提取所有数据。 | 否 | - |- |
| 查询 | 如果你选择“查询”作为输入，请指定一个用来从源提取数据的 SQL 查询，这将替代在数据集中指定的任何表。 使用查询是一个好方法，它可以减少用于测试或查找的行数。<br><br>不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中的一个 UDF，它返回你可以在数据流中使用的表。<br>查询示例：`Select * from MyTable where customerId > 1000 and customerId < 2000`| 否 | 字符串 | query |
| 批大小 | 指定批大小，以将大型数据分成多个读取操作。 | 否 | Integer | batchSize |
| 隔离级别 | 选择下列隔离级别之一：<br>- 读取已提交的内容<br>- 读取未提交的内容（默认）<br>- 可重复的读取<br>- 可序列化<br>- 无（忽略隔离级别） | 否 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-sql-managed-instance-source-script-example"></a>Azure SQL 托管实例源脚本示例

使用 Azure SQL 托管实例作为源类型时，关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLMISource
```

### <a name="sink-transformation"></a>接收器转换

下表列出了 Azure SQL 托管实例接收器支持的属性。 可以在“接收器选项”选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定数据库目标上允许哪些操作。 默认设置为仅允许插入。<br>若要更新、更新插入或删除行，需要进行[“更改行”转换](data-flow-alter-row.md)才能标记这些操作的行。 | 是 | `true` 或 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 键列 | 对于更新、更新插入和删除操作，必须设置键列来确定要更改的行。<br>后续的更新、更新插入和删除将使用你选取为密钥的列名称。 因此，你必须选取存在于接收器映射中的列。 | 否 | Array | 密钥 |
| 跳过写入键列 | 如果你不希望将值写入到键列，请选择“跳过写入键列”。 | 否 | `true` 或 `false` | skipKeyWrites |
| 表操作 |确定在写入之前是否从目标表重新创建或删除所有行。<br>- **无**：不会对表进行任何操作。<br>- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。<br>- 截断：将删除目标表中的所有行。 | 否 | `true` 或 `false` | recreate<br/>truncate |
| 批大小 | 指定每批中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。 | 否 | Integer | batchSize |
| 预处理和后处理 SQL 脚本 | 指定在将数据写入接收器数据库之前（预处理）和之后（后处理）会执行的多行 SQL 脚本。 | 否 | 字符串 | preSQLs<br>postSQLs |

#### <a name="azure-sql-managed-instance-sink-script-example"></a>Azure SQL 托管实例接收器脚本示例

使用 Azure SQL 托管实例作为接收器类型时，关联的数据流脚本为：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLMISink
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-sql-managed-instance"></a>SQL 托管实例的数据类型映射

使用复制活动向/从 SQL 托管实例复制数据时，会使用以下从 SQL 托管实例数据类型到服务内部使用的临时数据类型映射。 若要了解复制活动如何从源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SQL 托管实例数据类型 | 临时服务数据类型 |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前复制活动支持的最大精度为 28。 如果数据需要的精度大于 28，请考虑在 SQL 查询中将其转换为字符串。

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
有关复制活动支持作为源和接收器的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
