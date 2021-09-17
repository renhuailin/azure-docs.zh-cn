---
title: 控制无服务器 SQL 池对存储帐户的访问
description: 介绍无服务器 SQL 池如何访问 Azure 存储，以及如何在 Azure Synapse Analytics 中控制无服务器 SQL 池对存储的访问。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d3a1fe8f4b06601ed6b3e77ffa5743506e923ec4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771743"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中控制无服务器 SQL 池对存储帐户的访问

无服务器 SQL 池查询直接从 Azure 存储中读取文件。 对 Azure 存储中文件的访问权限是在以下两个级别控制的：
- **存储级别** - 用户应具有访问基础存储文件的权限。 你的存储管理员应当允许 Azure AD 主体读取/写入文件，或者生成将用来访问存储的 SAS 密钥。
- **SQL 服务级别** - 用户应已被授予使用 [外部表](develop-tables-external-tables.md)读取数据或执行 `OPENROWSET` 函数的权限。 在此部分中详细了解[所需权限](develop-storage-files-overview.md#permissions)。

本文介绍可用的凭据类型，以及为 SQL 和 Azure AD 用户进行的凭据查找是如何执行的。

## <a name="storage-permissions"></a>存储权限

Synapse Analytics 工作区中的无服务器 SQL 池可以读取 Azure Data Lake Storage 中存储的文件的内容。 需要配置存储的权限让执行 SQL 查询的用户能够读取文件。 有 3 种方法来启用文件的访问权限：
- **[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)** ：通过它能够将角色分配给存储所在的租户中的某些 Azure AD 用户。 读取者必须对存储帐户具有 RBAC 角色 `Storage Blob Data Reader`、`Storage Blob Data Contributor` 或 `Storage Blob Data Owner`。 写入 Azure 存储中数据的用户必须具有`Storage Blob Data Writer`或`Storage Blob Data Owner`角色。 请注意`Storage Owner`角色并不意味着用户也是`Storage Data Owner`。
- 通过访问控制列表 (ACL)，可对 Azure 存储中的文件和目录定义精细的[“读取(R)”、“写入(W)”和“执行(X)”权限](../../storage/blobs/data-lake-storage-access-control.md#levels-of-permission)。 ACL 可分配给 Azure AD 用户。 如果读取者要读取 Azure 存储中某个路径上的文件，必须对该文件路径中的每个文件夹具有“执行(X)”ACL 权限，对文件具有“读取(R)” ACL 权限。 [详细了解如何设置存储层中的 ACL 权限](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls)。
- **共享访问签名 (SAS)** ：使读取者能够使用限时令牌访问 Azure Data Lake Storage 上的文件。 读取者甚至无需进行 Azure AD 用户身份验证。 SAS 令牌包含授予读取者的权限以及令牌的有效期限。 SAS 令牌是任何不需要位于同一 Azure AD 租户中的用户进行时间限制的访问的好选择。 可以在存储帐户或特定目录上定义 SAS 令牌。 若要详细了解 SAS 令牌，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限](../../storage/common/storage-sas-overview.md)。

替代方法是，可通过允许匿名访问来使你的文件公开可用。 如果你有非公共数据，则不得使用此方法。 

## <a name="supported-storage-authorization-types"></a>支持的存储授权类型

如果文件不是公开可用的，则登录到无服务器 SQL 池的用户必须获得访问和查询 Azure 存储中文件的授权。 可以使用三种授权类型来访问非公共存储 - [用户标识](?tabs=user-identity)、[共享访问签名](?tabs=shared-access-signature)和[托管标识](?tabs=managed-identity)。

> [!NOTE]
> **Azure AD 直通** 是创建工作区时的默认行为。

### <a name="user-identity"></a>[用户标识](#tab/user-identity)

用户标识（也称为“Azure AD 直通”）是一种授权类型。使用这种授权时，登录到无服务器 SQL 池的 Azure AD 用户的标识将用于授予数据访问权限。 在访问数据之前，Azure 存储管理员必须向 Azure AD 用户授予权限。 如下表中所示，SQL 用户类型不支持此授权类型。

> [!IMPORTANT]
> 客户端应用程序可能会缓存 AAD 身份验证令牌。 例如，PowerBI 会缓存 AAD 令牌，并在一小时内重复使用同一令牌。 如果在执行查询的过程中，该令牌过期，则长时间运行的查询可能会失败。 如果查询时遇到 AAD 访问令牌过期导致查询失败，请考虑切换到[托管标识](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types)或[共享访问签名](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types)。

需要具有存储 Blob 数据所有者/参与者/读取者角色才能使用自己的标识来访问数据。 或者，你可以指定细化 ACL 规则以访问文件和文件夹。 即使你是存储帐户的所有者，也仍需将自己添加到存储 Blob 数据角色之一。
若要详细了解 Azure Data Lake Store Gen2 中的访问控制，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](../../storage/blobs/data-lake-storage-access-control.md)一文。


### <a name="shared-access-signature"></a>[共享访问签名](#tab/shared-access-signature)

共享访问签名 (SAS) 提供对存储帐户中的资源的委托访问权限。 使用 SAS，客户可向客户端授予对存储帐户中的资源的访问权限，而无需共享帐户密钥。 通过 SAS 可以精细控制向具有 SAS 的客户端授予的访问权限类型，包括有效期间隔、授予的权限、可接受的 IP 地址范围，以及可接受的协议 (https/http)。

可以导航到“Azure 门户”->“存储帐户”->“共享访问签名”->“配置权限”->“生成 SAS 和连接字符串”来获取 SAS 令牌。

> [!IMPORTANT]
> 生成某个 SAS 令牌时，其开头会包含问号（“?”）。 若要在无服务器 SQL 池中使用该令牌，必须在创建凭据时删除问号（“?”）。 例如：
>
> SAS 令牌：?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

若要允许使用 SAS 令牌进行访问，需要创建数据库范围或服务器范围的凭据 


> [!IMPORTANT]
> 你无法使用 SAS 令牌访问专用存储帐户。 请考虑切换到[托管标识](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types)或 [Azure AD 直通](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types)身份验证以访问受保护的存储。

### <a name="managed-identity"></a>[托管标识](#tab/managed-identity)

托管标识也称为 MSI。 它是 Azure Active Directory (Azure AD) 的一项功能，为无服务器 SQL 池提供 Azure 服务。 此外，它还会在 Azure AD 中部署一个自动托管的标识。 此标识可用于对有关访问 Azure 存储中的数据的请求授权。

在访问数据之前，Azure 存储管理员必须向管理标识授予访问数据的权限。 向托管标识授予权限的方式与向任何其他 Azure AD 用户授予权限的方式相同。

### <a name="anonymous-access"></a>[匿名访问](#tab/public-access)

你可以访问[允许匿名访问](../../storage/blobs/anonymous-read-access-configure.md)的 Azure 存储帐户中放置的公开可用文件。

---

### <a name="supported-authorization-types-for-databases-users"></a>支持用于数据库用户的授权类型

在下表中，可以找到可用的授权类型：

| 授权类型                    | SQL 用户    | Azure AD 用户     |
| ------------------------------------- | ------------- | -----------    |
| [用户标识](?tabs=user-identity#supported-storage-authorization-types)       | 不支持 | 支持      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | 支持     | 支持      |
| [托管标识](?tabs=managed-identity#supported-storage-authorization-types) | 支持 | 支持      |

### <a name="supported-storages-and-authorization-types"></a>支持的存储和授权类型

可以使用授权和 Azure 存储类型的以下组合：

| 授权类型  | Blob 存储   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| SAS    | 支持      | 不支持   | 支持     |
| [托管标识](?tabs=managed-identity#supported-storage-authorization-types) | 支持      | 支持        | 支持     |
| [用户标识](?tabs=user-identity#supported-storage-authorization-types)    | 支持      | 支持        | 支持     |

## <a name="firewall-protected-storage"></a>受防火墙保护的存储

通过创建[资源实例规则](../../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-from-azure-resource-instances-preview)，可以将存储帐户配置为允许访问特定的无服务器 SQL 池。
访问受防火墙保护的存储时，可使用用户标识或托管标识。 

> [!NOTE]
> 存储上的防火墙功能现为公共预览版，它在所有公共云区域中都可用。 


### <a name="user-identity"></a>[用户标识](#tab/user-identity)

若要通过用户标识访问受防火墙保护的存储，可以使用 Azure 门户 UI 或 PowerShell 模块 Az.Storage。
### <a name="configuration-via-azure-portal"></a>通过 Azure 门户配置

1. 在 Azure 门户中搜索你的存储帐户。
1. 转到“设置”部分下的“网络”。
1. 在“资源实例”部分中，为 Synapse 工作区添加例外情况。
1. 选择 Microsoft.Synapse/workspaces 作为资源类型。
1. 选择工作区名称作为实例名称。
1. 单击“保存”。

### <a name="configuration-via-powershell"></a>通过 PowerShell 进行配置

按照以下步骤配置存储帐户防火墙，并为 Synapse 工作区添加例外。

1. 打开 PowerShell 或[安装 PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. 安装 Az.Storage 3.4.0 模块和 Az.Synapse 0.7.0： 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > 确保使用 3.4.0 版。 可以通过运行以下命令来检查 Az.Storage 版本：  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. 连接到 Azure 租户： 
    ```powershell
    Connect-AzAccount
    ```
4. 在 PowerShell 中定义变量： 
    - 资源组名称 - 可以在 Azure 门户中的存储帐户概述中找到此内容。
    - 帐户名称 - 受防火墙规则保护的存储帐户的名称。
    - 租户 ID - 可在 Azure 门户中的“租户中的 Azure Active Directory 信息”中找到此内容。
    - 工作区名称 - Synapse 工作区的名称。

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > 确保资源 ID 在 resourceId 变量的输出中与此模板匹配。
    >
    > 以小写形式书写“resourcegroups”很重要。
    > 一个资源 id 的示例： 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. 添加存储网络规则： 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. 验证是否已在存储帐户中应用规则： 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

### <a name="shared-access-signature"></a>[共享访问签名](#tab/shared-access-signature)

不能使用共享访问签名来访问受防火墙保护的存储。

### <a name="managed-identity"></a>[托管标识](#tab/managed-identity)

需要[允许受信任的 Microsoft 服务设置](../../storage/common/storage-network-security.md#trusted-microsoft-services)并明确[将 Azure 角色](../../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights)分配给该资源实例的[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 在这种情况下，实例的访问范围对应于分配给托管标识的 Azure 角色。

### <a name="anonymous-access"></a>[匿名访问](#tab/public-access)

无法使用匿名访问方式来访问受防火墙保护的存储。

---

## <a name="credentials"></a>凭据

若要查询 Azure 存储中的文件，无服务器 SQL 池终结点需要一个包含身份验证信息的凭据。 使用两种类型的凭据：
- 服务器级凭据用于通过 `OPENROWSET` 函数执行的即席查询。 凭据名称必须与存储 URL 匹配。
- 数据库范围的凭据用于外部表。 外部表使用应当用来访问存储的凭据来引用 `DATA SOURCE`。

若要允许用户创建或删除凭据，管理员可对用户执行 GRANT/DENY ALTER ANY CREDENTIAL 权限操作：

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

访问外部存储的数据库用户必须有权使用凭据。

### <a name="grant-permissions-to-use-credential"></a>授予使用凭据的权限

若要使用凭据，用户必须拥有对特定凭据的 `REFERENCES` 权限。 若要向 specific_user 授予对 storage_credential 的 `REFERENCES` 权限，请执行：

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>服务器范围的凭据

当 SQL 登录名在未指定 `DATA_SOURCE` 的情况下调用 `OPENROWSET` 函数来读取某个存储帐户上的文件时，将使用服务器范围的凭据。 服务器范围的凭据的名称必须与 Azure 存储的基 URL 相匹配（可以选择后跟容器名称）。 可通过运行 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) 来添加凭据。 需要提供 CREDENTIAL NAME 参数。

> [!NOTE]
> 不支持参数 `FOR CRYPTOGRAPHIC PROVIDER`。

服务器级 CREDENTIAL 名称必须与存储帐户（以及可选容器）的完整路径匹配，格式如下：`<prefix>://<storage_account_path>[/<container_name>]`。 下表描述了存储帐户路径：

| 外部数据源       | 前缀 | 存储帐户路径                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob 存储         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

服务器范围的凭据允许使用以下身份验证类型来访问 Azure 存储：

### <a name="user-identity"></a>[用户标识](#tab/user-identity)

如果 Azure AD 用户具有 `Storage Blob Data Owner`、`Storage Blob Data Contributor` 或 `Storage Blob Data Reader` 角色，则可访问 Azure 存储中的任意文件。 Azure AD 用户无需凭据即可访问存储。 

SQL 用户无法使用 Azure AD 身份验证来访问存储。

### <a name="shared-access-signature"></a>[共享访问签名](#tab/shared-access-signature)

以下脚本将创建一个服务器级凭据，`OPENROWSET` 函数可以使用该凭据通过 SAS 令牌访问 Azure 存储上的任何文件。 创建此凭据后，用于执行 `OPENROWSET` 函数的 SQL 主体就可以读取与凭据名称中的 URL 匹配的 Azure 存储中受 SAS 密钥保护的文件。

请将 <mystorageaccountname> 替换为实际存储帐户名称，并将 <mystorageaccountcontainername> 替换为实际容器名称：

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

或者，可以只使用存储帐户的基 URL，而不使用容器名称。

### <a name="managed-identity"></a>[托管标识](#tab/managed-identity)

以下脚本将创建一个服务器级凭据，`OPENROWSET` 函数可以使用该凭据通过工作区托管标识访问 Azure 存储上的任何文件。

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

或者，可以只使用存储帐户的基 URL，而不使用容器名称。

### <a name="public-access"></a>[公共访问权限](#tab/public-access)

若要允许访问公开可用的文件，不需要使用数据库范围的凭据。 创建[不使用数据库范围的凭据的数据源](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)，以便访问 Azure 存储上公开可用的文件。

---

## <a name="database-scoped-credential"></a>数据库范围的凭据

当任何主体在使用 `DATA_SOURCE` 的情况下调用 `OPENROWSET` 函数时，或在不访问公共文件的[外部表](develop-tables-external-tables.md)中选择数据时，将使用数据库范围的凭据。 数据库范围的凭据不需要匹配存储帐户的名称。 它将在定义存储位置的数据源中显式使用。

数据库范围的凭据允许使用以下身份验证类型来访问 Azure 存储：

### <a name="azure-ad-identity"></a>[Azure AD 标识](#tab/user-identity)

如果 Azure AD 用户至少具有 `Storage Blob Data Owner`、`Storage Blob Data Contributor` 或 `Storage Blob Data Reader` 角色，则可访问 Azure 存储中的任意文件。 Azure AD 用户无需凭据即可访问存储。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL 用户无法使用 Azure AD 身份验证来访问存储。

### <a name="shared-access-signature"></a>[共享访问签名](#tab/shared-access-signature)

下面的脚本创建一个凭据，该凭据用来通过其中指定的 SAS 令牌访问存储中的文件。 该脚本将创建一个示例外部数据源，该数据源使用此 SAS 令牌访问存储。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[托管标识](#tab/managed-identity)

下面的脚本创建一个数据库范围的凭据，该凭据可用于以服务托管标识的身份模拟当前的 Azure AD 用户。 该脚本将创建一个示例外部数据源，该数据源使用工作区标识访问存储。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

数据库范围的凭据不需要匹配存储帐户的名称，因为它将在定义存储位置的 DATA SOURCE 中显式使用。

### <a name="public-access"></a>[公共访问权限](#tab/public-access)

若要允许访问公开可用的文件，不需要使用数据库范围的凭据。 创建[不使用数据库范围的凭据的数据源](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)，以便访问 Azure 存储上公开可用的文件。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

在外部数据源中使用数据库范围的凭据，以便指定将使用哪种身份验证方法来访问此存储：

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>示例

### <a name="access-a-publicly-available-data-source"></a>访问公开可用的数据源

使用以下脚本创建一个表，用以访问公开可用的数据源。

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

数据库用户可以使用外部表或使用引用数据源的 [OPENROWSET](develop-openrowset.md) 函数从数据源读取文件的内容：

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>使用凭据访问数据源

修改以下脚本来创建一个外部表，用以使用 SAS 令牌、用户的 Azure AD 标识或工作区的托管标识来访问 Azure 存储。

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

数据库用户可以使用[外部表](develop-tables-external-tables.md)或使用引用数据源的 [OPENROWSET](develop-openrowset.md) 函数从数据源读取文件的内容：

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>后续步骤

下面列出的文章可帮助你了解如何查询不同的文件夹类型、文件类型，以及如何创建和使用视图：

- [查询单个 CSV 文件](query-single-csv-file.md)
- [查询文件夹和多个 CSV 文件](query-folders-multiple-csv-files.md)
- [查询特定的文件](query-specific-files.md)
- [查询 Parquet 文件](query-parquet-files.md)
- [创建和使用视图](create-use-views.md)
- [查询 JSON 文件](query-json-files.md)
- [查询 Parquet 嵌套类型](query-parquet-nested-types.md)